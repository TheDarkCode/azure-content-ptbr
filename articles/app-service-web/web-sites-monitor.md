<properties
	pageTitle="Monitorar aplicativos no Serviço de Aplicativo do Azure"
	description="Saiba como monitorar Aplicativos no Serviço de Aplicativo do Azure usando o Portal do Azure."
	services="app-service"
	documentationCenter=""
	authors="btardif"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/31/2016"
	ms.author="byvinyal"/>

# Como monitorar aplicativos Web no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) fornece a funcionalidade de monitoramento no [Portal do Azure](https://portal.azure.com). Isso inclui a capacidade de revisar **cotas** e **métricas** para um aplicativo, bem como o Plano do Serviço de Aplicativo, configurar **alertas** e até mesmo **dimensionar** automaticamente de acordo com essas métricas.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Noções básicas sobre cotas e métricas

### Cotas

Aplicativos hospedados no Serviço de Aplicativo estão sujeitos a determinados *limites* de recursos que eles podem usar. Os limites são definidos pelo **Plano do Serviço de Aplicativo** associado ao aplicativo.

Se o aplicativo estiver hospedado em um plano **Gratuito** ou **Compartilhado**, os limites de uso dos recursos que o aplicativo pode usar são definidos por **Cotas**.

Se o aplicativo estiver hospedado em um plano **Básico**, **Standard** ou **Premium**, os limites de uso dos recursos que eles podem usar são definidos pelo **tamanho** (Pequeno, Médio, Grande) e **contagem de instâncias** (1, 2, 3,...) do **Plano do Serviço de Aplicativo**.

**Cotas** para aplicativos **Gratuitos** ou **Compartilhados** são:

* **CPU(Curto)**
   * Quantidade de CPU permitida para esse aplicativo em um intervalo de três minutos. Essa cota é definida novamente a cada três minutos.
* **CPU(dia)**
   * Quantidade total de CPU permitida para esse aplicativo em um dia. Essa cota é definida novamente a cada 24 horas, à meia-noite UTC.
* **Memória**
   * Quantidade total de memória permitida para esse aplicativo.
* **Largura de banda**
   * Quantidade total de largura de banda de saída permitida para esse aplicativo em um dia. Essa cota é definida novamente a cada 24 horas, à meia-noite UTC.
* **Sistema de arquivos**
   * Quantidade total de armazenamento permitida.
   
A única cota aplicável aos aplicativos hospedados no plano **Básico**, **Standard** e **Premium** é **Sistema de arquivos**.

Para saber mais sobre cotas, limites e recursos específicos disponíveis para os SKUs de Serviço de Aplicativo diferentes, confira: [Limites do serviço de assinatura do Azure](../azure-subscription-service-limits.md#app-service-limits)

#### Aplicação de cota

Se, durante o uso, um aplicativo exceder a cota **CPU (curto)**, **CPU (dia)** ou **largura de banda**, o aplicativo será interrompido até que a cota seja redefinida. Durante esse tempo, todas as solicitações de entrada resultarão em um **HTTP 403**. ![][http403]

Se a cota **memória** do aplicativo for excedida, o aplicativo será reiniciado.

Se a cota **Sistema de arquivos** for excedida, qualquer operação falhará, incluindo a gravação em logs.

As cotas podem ser aumentadas ou removidas de seu aplicativo pela atualização de seu Plano do Serviço de Aplicativo.

### Métricas

**Métricas** fornecem informações sobre o aplicativo, ou sobre o comportamento do Plano do Serviço de Aplicativo.

Para um **Aplicativo**, as métricas disponíveis são:

* **Tempo Médio de Resposta**
   * O tempo médio necessário para o aplicativo atender às solicitações em ms.
* **Conjunto de trabalho de memória média**
   * A quantidade média de memória em MiBs usada pelo aplicativo.
* **Tempo de CPU**
   * A quantidade de CPU em segundos consumida pelo aplicativo. Para saber mais sobre essa métrica, consulte: [Tempo de CPU versus porcentagem de CPU](#cpu-time-vs-cpu-percentage)
* **Entrada de Dados**
   * A quantidade de largura de banda de entrada consumida pelo aplicativo em MiBs.
* **Saída de dados**
   * A quantidade de largura de banda de saída consumida pelo aplicativo em MiBs.
* **Http 2xx**
   * Contagem de solicitações que resultam em um código de status http > = 200, mas < 300.
* **Http 3xx**
   * Contagem de solicitações que resultam em um código de status http > = 300, mas < 400.
* **Http 401**
   * Contagem de solicitações que resultam em um código de status HTTP 401.
* **Http 403**
   * Contagem de solicitações que resultam em um código de status HTTP 403.
* **Http 404**
   * Contagem de solicitações que resultam em um código de status HTTP 404.
* **Http 406**
   * Contagem de solicitações que resultam em um código de status HTTP 406.
* **Http 4xx**
   * Contagem de solicitações que resultam em um código de status http > = 400, mas < 500.
* **Erros do Servidor Http**
   * Contagem de solicitações que resultam em um código de status http > = 500, mas < 600.
* **Conjunto de trabalho de memória**
   * Quantidade atual de memória usada pelo aplicativo em MiBs.
* **Solicitações**
   * Número total de solicitações, independentemente de seu código de status HTTP resultante.

Para um **Plano do Serviço de Aplicativo**, as métricas disponíveis são:

>[AZURE.NOTE] As métricas do Plano do Serviço de Aplicativo só estão disponíveis para os planos no SKU **Básico**, **Standard** e **Premium**.

* **Porcentagem de CPU**
   * A média de CPU usada em todas as instâncias do plano.
* **Porcentagem de Memória**
   * A média de memória usada em todas as instâncias do plano.
* **Entrada de Dados**
   * A média de largura de banda de entrada usada em todas as instâncias do plano.
* **Saída de dados**
   * A média de largura de banda de saída usada em todas as instâncias do plano.
* **Tamanho da fila do disco**
   * O número médio de solicitações de leitura e gravação enfileiradas no armazenamento. Um tamanho grande de fila de disco é uma indicação de um aplicativo que pode estar lento devido ao excesso de E/S de disco.
* **Tamanho da Fila de Http**
   * O número médio de solicitações HTTP que tiveram de esperar na fila antes de serem atendidas. Um tamanho de fila HTTP alto ou crescente é um sintoma de um plano sob carga pesada.

### Tempo de CPU versus Porcentagem de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Há duas métricas que refletem o uso da CPU. **Tempo de CPU** e **Porcentagem de CPU**

**Tempo de CPU** é útil para aplicativos hospedados nos planos **Gratuito** ou **Compartilhado** desde que uma de suas cotas seja definida em minutos de CPU usados pelo aplicativo.

**Porcentagem de CPU** por outro lado é útil para aplicativos hospedados em planos **Básico**, **Standard** e **Premium**, já que eles podem ser expandidos, e essa métrica é uma boa indicação do uso geral em todas as instâncias.

##Granularidade de Métricas e a Política de Retenção

As métricas de um aplicativo e de um Plano do Serviço de Aplicativo são registradas e agregadas pelo serviço com as seguintes granularidades e políticas de retenção:

 * Métricas de granularidade de **minuto** são mantidas por **24 horas**
 * Métricas de granularidade de **Hora** são mantidas por **7 dias**
 * Métricas de granularidade de **Dia** são mantidas por **30 dias**

## Monitoramento de cotas e métricas no Portal do Azure.

Você pode examinar o status das diferentes **cotas** e **métricas** que afetam um aplicativo no [Portal do Azure](https://portal.azure.com).

![][quotas] **Cotas** podem ser encontradas em Configurações >**Cotas**. A experiência do usuário permite que você examine: (1) o nome das cotas, (2) o intervalo de redefinição, (3) o limite atual e (4) o valor atual.

![][metrics] **Métricas** podem ser acessadas diretamente da folha do recurso. Você também pode personalizar o gráfico da seguinte maneira: (1) **clique** nele e (2) selecione **editar gráfico**. A partir daqui, você pode alterar (3) o **intervalo de tempo**, (4) o **tipo de gráfico** e (5) as **métricas** a serem exibidas.

Saiba mais sobre métricas aqui: [Monitorar as métricas do serviço](../azure-portal/insights-how-to-customize-monitoring.md).

## Alertas e dimensionamento automático
As métricas de um Aplicativo ou Plano do Serviço de Aplicativo podem ser conectadas a alertas, para saber mais sobre isso, consulte [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md)

Aplicativos do Serviço de Aplicativo hospedados em Planos do Serviço de Aplicativo Básico, Standard ou Premium oferecem suporte ao **Dimensionamento Automático**. Isso permite que você configure regras que monitoram as métricas do Plano do Serviço de Aplicativo e podem aumentar ou diminuir a contagem de instâncias, fornecendo recursos adicionais conforme o necessário, ou economizando dinheiro quando o aplicativo é provisionado de forma excessiva. Saiba mais sobre o dimensionamento automático aqui: [Como dimensionar](../azure-portal/insights-how-to-scale.md) e aqui [Práticas recomendadas para o dimensionamento automático do Azure Insights](../azure-portal/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

<!---HONumber=AcomDC_0803_2016-->