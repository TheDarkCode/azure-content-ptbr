<properties
   pageTitle="Gerenciar seus aplicativos no Visual Studio | Microsoft Azure"
   description="Use o Visual Studio para criar, desenvolver, empacotar, implantar e depurar seus aplicativos e serviços do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# Usar o Visual Studio para simplificar a escrita e o gerenciamento de seus aplicativos do Service Fabric

É possível gerenciar os serviços e aplicativos do Service Fabric do Azure por meio do Visual Studio. Depois de [configurar o ambiente de desenvolvimento](service-fabric-get-started.md),você pode usar o Visual Studio para criar aplicativos do Service Fabric, adicionar serviços, ou pacotes, registrar e implantar aplicativos no cluster de desenvolvimento local.

## Implantar o aplicativo do Service Fabric

Por padrão, a implantação de um aplicativo combina as etapas a seguir em uma única operação:

1. Criar o pacote de aplicativo
2. Carregar o pacote de aplicativo no repositório de imagens
3. Registrar o tipo de aplicativo
4. Remover as instâncias de aplicativo em execução
5. Criar uma nova instância do aplicativo

No Visual Studio, pressionar **F5** também implanta seu aplicativo e anexa o depurador a todas as instâncias do aplicativo. Você pode usar **Ctrl + F5** para implantar um aplicativo sem depuração ou pode publicar um cluster local ou remoto usando o perfil de publicação. Para saber mais, confira [Publicar um aplicativo em um cluster remoto usando o Visual Studio](service-fabric-publish-app-remote-cluster.md)

### Modo de Depuração do Aplicativo

Por padrão, o Visual Studio removerá as instâncias existentes do seu tipo de aplicativo quando você interromper a depuração ou (se você implantou o aplicativo sem anexar o depurador), quando você reimplantar o aplicativo. Nesse caso, todos os dados do aplicativo serão removidos. Quando você está depurando localmente, convém manter os dados que você criou ao testar uma nova versão do aplicativo. As Ferramentas do Service Fabric para Visual Studio fornecem uma propriedade denominada **Modo de Depuração do Aplicativo**, que controla se **F5** deve desinstalar o aplicativo ou manter o aplicativo após uma sessão de depuração terminar.

#### Para definir a propriedade Modo de Depuração do Aplicativo

1. No menu de atalho do projeto de aplicativo, escolha **Propriedades** (ou pressione a tecla **F4**).
2. Na janela **Propriedades**, defina a propriedade **Modo de Depuração do Aplicativo** para **Remover** ou ** Atualização Automática**.

    ![Definir a Propriedade Modo de Depuração do Aplicativo][debugmodeproperty]

Definir esse valor da propriedade para **Atualização Automática** deixará o aplicativo em execução no cluster local. O próximo **F5** tratará a implantação como uma atualização usando o modo automático não monitorado para atualizar rapidamente o aplicativo para uma versão mais recente com uma cadeia de caracteres de data anexada. O processo de atualização preserva todos os dados inseridos em uma sessão de depuração anterior.

![Exemplo da nova versão do aplicativo com a data1 incluída][preservedate]

Os dados são preservados, aproveitando os recursos de atualização de aplicativo do Service Fabric, mas são ajustados para otimizar o desempenho em vez da segurança. Para obter mais informações sobre como atualizar aplicativos e como executar uma atualização em um ambiente real, confira [atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md).

>[AZURE.NOTE] Essa propriedade não existe antes da versão 1.1 das Ferramentas do Service Fabric para o Visual Studio. Antes da versão 1.1, use a propriedade **Preservar Dados no Início** para ter o mesmo comportamento.

## Adicione um serviço ao aplicativo da Malha de Serviços

Você pode adicionar novos serviços a seu aplicativo para estender sua funcionalidade. Para garantir que o serviço esteja incluído no seu pacote de aplicativos, adicione o serviço usando o item de menu **Novo Serviço de Malha...**.

![Adicionar um novo serviço de malha ao aplicativo][newservice]

Selecione um tipo de projeto da Malha do Serviço para adicionar ao aplicativo e especifique um nome para o serviço. Confira [Como escolher uma estrutura para o serviço](service-fabric-choose-framework.md) para ajudar com a decisão de que tipo de serviço usar.

![Selecionar um tipo de projeto do Serviço de Malha para adicionar ao aplicativo][addserviceproject]

O novo serviço será adicionado à solução e ao pacote de aplicativo existente. As referências de serviço e uma instância de serviço padrão serão adicionadas ao manifesto do aplicativo. O serviço será criado e iniciado na próxima vez que você implantar o aplicativo.

![O novo serviço será adicionado ao manifesto do aplicativo][newserviceapplicationmanifest]

## Empacotar o aplicativo do Service Fabric

Para implantar o aplicativo e seu serviço em um cluster, você precisa criar um pacote de aplicativos. O pacote organiza o manifesto do aplicativo, os manifestos do serviço e outros arquivos necessários em um layout específico. O Visual Studio configura e gerencia o pacote na pasta do projeto do aplicativo, no diretório 'pkg'. Clicar em **Pacote** no menu de contexto **Aplicativo** cria ou atualiza o pacote de aplicativos. Convém fazer isso se você implantar o aplicativo usando scripts personalizados de PowerShell.

## Remover aplicativos e tipos de aplicativo usando o Gerenciador de Nuvem

Você pode executar operações de gerenciamento de cluster básico no Visual Studio usando o Gerenciador de Nuvem, que você pode iniciar por meio do menu **Exibir**. Por exemplo, você pode excluir aplicativos e desprovisionar tipos de aplicativos em clusters locais ou remotos.

![Remover um aplicativo](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Para funcionalidade de gerenciamento de cluster mais avançada, confira [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

- [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md)
- [Implantação de aplicativo da Malha do Serviço](service-fabric-deploy-remove-applications.md)
- [Gerenciando parâmetros do aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depurando o aplicativo da Malha do Serviço](service-fabric-debugging-your-application.md)
- [Visualização do cluster usando o Gerenciador do Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0713_2016-->