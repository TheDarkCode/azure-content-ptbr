<properties
  pageTitle="Azure IoT Suite e Aplicativos Lógicos | Microsoft Azure"
  description="Um tutorial sobre como conectar aplicativos lógicos para o Azure IoT Suite para processo empresarial."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# Tutorial: conectar um aplicativo lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite

A solução pré-configurada de monitoramento remoto do [Microsoft Azure IoT Suite][lnk-internetofthings] é uma ótima maneira de começar rapidamente com um conjunto de recursos de ponta a ponta que é um exemplo de uma solução de IoT. Este tutorial explica como adicionar um aplicativo lógico à sua solução pré-configurada de monitoramento remoto do Microsoft Azure IoT Suite. Essas etapas demonstram como é possível aproveitar ainda mais sua solução de IoT conectando-a a um processo empresarial.

_Se você estiver procurando um passo a passo sobre como provisionar uma solução pré-configurada de monitoramento remoto, consulte [Tutorial: introdução às soluções de IoT pré-configuradas][lnk-getstarted]._

Antes de iniciar este tutorial, você deve:

- Provisionar a solução pré-configurada de monitoramento remoto na sua assinatura do Azure.

- Criar uma conta SendGrid para que você possa enviar um email que dispare o processo empresarial. Você pode se inscrever para uma conta de avaliação gratuita no [SendGrid](https://sendgrid.com/) clicando em **Teste gratuitamente**. Depois de fazer o registro para sua conta de avaliação gratuita, você precisa criar uma [chave de API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) no SendGrid, que concede permissões para enviar email. Você precisa dessa chave de API mais tarde no tutorial.

Supondo que você já tenha provisionado a solução pré-configurada de monitoramento remoto, navegue até o grupo de recursos dessa solução no [Portal do Azure][lnk-azureportal]. O grupo de recursos tem o mesmo nome que o nome da solução que você escolheu quando provisionou sua solução de monitoramento remoto. No grupo de recursos, você pode ver todos os recursos do Azure provisionados para sua solução, com exceção do aplicativo do Azure Active Directory, que você pode encontrar no Portal Clássico do Azure. A captura de tela a seguir mostra uma folha **Grupo de recursos** de exemplo para uma solução pré-configurada de monitoramento remoto:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Para começar, configure o aplicativo lógico a ser usado com a solução pré-configurada.

## Configurar o aplicativo lógico

1. Clique em __Adicionar__ na parte superior da folha de seu grupo de recursos no Portal do Azure.

2. Procure o __Aplicativo Lógico__, selecione-o e clique em **Criar**.

3. Preencha o __Nome__ e use a mesma **Assinatura** e o **Grupo de recursos** que você usou ao provisionar sua solução de monitoramento remoto. Clique em __Criar__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Quando a implantação for concluída, você verá que o Aplicativo Lógico estará listado como um recurso em seu grupo de recursos.

5. Clique no Aplicativo Lógico para navegar até a folha Aplicativo Lógico, escolha o modelo **Aplicativo Lógico em Branco** para abrir o **Designer de Aplicativos Lógicos**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Escolha __Solicitar__. Essa ação especifica que uma solicitação HTTP de entrada com um carga no formato JSON específico atua como um gatilho.

7. Cole o seguinte no esquema JSON do corpo da solicitação:

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    > [AZURE.NOTE] Você pode copiar a URL para o HTTP post após você salvar o aplicativo lógico, mas primeiro você deve adicionar uma ação.

8. Clique em __+ Nova etapa__ no gatilho manual. Em seguida, clique em **Adicionar uma ação**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Procure **SendGrid – Enviar email** e clique nele.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Insira um nome para a conexão, como **SendGridConnection**, insira a **Chave de API do SendGrid** criada quando você configurou sua conta do SendGrid e clique em **Criar**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Adicione endereços de email que você possui nos campos **De** e **Para**. Adicione **Alerta de monitoramento remoto [DeviceId]** ao campo **Assunto**. No campo **Corpo do Email**, adicione **O dispositivo [DeviceId] foi relatado [measurementName] com o valor [measuredValue].** Você pode adicionar **[DeviceId]**, **[measurementName]** e **[measuredValue]** clicando na seção **Você pode inserir dados das etapas anteriores**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Clique em __Salvar__ no menu superior.

13. Clique no gatilho **Solicitar** e copie o valor __HTTP POST para esta URL__. Você precisará dessa URL mais tarde neste tutorial.

> [AZURE.NOTE] Aplicativos Lógicos permitem executar [muitos tipos de ação diferentes][lnk-logic-apps-actions], incluindo ações no Office 365.

## Configurar o Trabalho Web EventProcessor

Nesta seção, você conecta sua solução pré-configurada ao Aplicativo Lógico criado. Para concluir essa tarefa, adicione a URL para disparar o Aplicativo Lógico à ação, que dispara quando um valor de sensor de dispositivo excede um limite.

1. Use seu cliente git para clonar a versão mais recente do [repositório github azure-iot-remote-monitoring][lnk-rmgithub]. Por exemplo:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. No Visual Studio, abra o __RemoteMonitoring.sln__ da cópia local do repositório.

3. Abra o arquivo __ActionRepository.cs__ na pasta **Infrastructure\\Repository**.

4. Atualize o dicionário **actionIds** com o __Http Post para esta URL__ que você anotou do seu Aplicativo Lógico, como se segue:

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Salve as alterações na solução e saia do Visual Studio.

## Implantar usando a linha de comando

Nesta seção, você implanta a versão atualizada da solução de monitoramento remoto para substituir a versão em execução atualmente no Azure.

1. Veja a seguir as instruções de [configuração de desenvolvimento][lnk-devsetup] para configurar o ambiente para implantação.

2.  Para implantar localmente, siga as instruções de [implantação local][lnk-localdeploy].

3.  Para implantar na nuvem e atualizar sua implantação de nuvem existente, siga as instruções de [implantação de nuvem][lnk-clouddeploy]. Use o nome de sua implantação original como o nome da implantação. Por exemplo, se a implantação original foi chamada de **demologicapp**, use o seguinte comando:

    ``
    build.cmd cloud release demologicapp
    ``
    
    Quando o script de compilação for executado, certifique-se de usar a mesma conta, assinatura e região do Azure e a mesma instância do Active Directory que você usou ao provisionar a solução.

## Ver seu aplicativo lógico em ação

A solução pré-configurada de monitoramento remoto tem duas regras configuradas por padrão ao provisionar uma solução. Ambas as regras estão no dispositivo **SampleDevice001**:

* Temperatura > 38,00
* Umidade > 48,00

A regra de temperatura dispara a ação **Gerar Alarme**, enquanto a regra de Umidade dispara a ação **SendMessage**. Pressupondo que você usou a mesma URL para ambas as ações, a classe **ActionRepository**, seu aplicativo lógico é disparado para uma regra ou outra. Ambas as regras usam SendGrid para enviar um email ao endereço **Para** com detalhes do alerta.

> [AZURE.NOTE] O Aplicativo Lógico continua disparando toda vez que o limite é atingido. Para evitar emails desnecessários, você pode desabilitar as regras no portal da solução ou desabilitar o Aplicativo Lógico no [Portal do Azure][lnk-azureportal].

No portal, além de receber emails, você também pode ver quando o aplicativo lógico é executado:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## Próximas etapas

Agora que você já usou um aplicativo lógico para conectar a solução pré-configurada a um processo empresarial, saiba mais sobre as opções de personalização das soluções pré-configuradas:

- [Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto][lnk-dynamic]
- [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=AcomDC_0824_2016-->