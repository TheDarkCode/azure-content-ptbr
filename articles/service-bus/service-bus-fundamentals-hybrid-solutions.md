<properties 
	pageTitle="Barramento de Serviço do Azure | Microsoft Azure" 
	description="Uma introdução ao uso do Barramento de Serviço para conectar os aplicativos do Azure a outro software." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/31/2016" 
	ms.author="sethm"/>

# Barramento de Serviço do Azure

Se um aplicativo ou serviço é executado na nuvem ou no local, geralmente precisa interagir com outros aplicativos ou serviços. Para fornecer uma maneira amplamente úteis para fazer isso, o Microsoft Azure oferece o Service Bus. Este artigo dá uma olhada essa tecnologia, que descreve o que é e por que você desejaria usá-lo.

## Conceitos fundamentais do barramento de serviço

Situações diferentes pedem estilos diferentes de comunicação. Às vezes, permitindo que aplicativos de enviar e receber mensagens por meio de uma fila simple é a melhor solução. Em outras situações, uma fila comum não é suficiente; uma fila com um mecanismo de publicação e assinatura é melhor. Em alguns casos, tudo que realmente é preciso é uma conexão entre aplicativos; filas não são necessárias. O Barramento de Serviço fornece todas as três opções, permitindo que os aplicativos interajam de várias maneiras diferentes.

O Service Bus é um serviço de nuvem de diversos clientes, o que significa que o serviço é compartilhado por vários usuários. Cada usuário, como um desenvolvedor de aplicativos, cria um *namespace*, em seguida, define os mecanismos de comunicação necessárias dentro desse namespace. A Figura 1 mostra isso.

![][1]
 
**Figura 1: O Barramento de Serviço fornece um serviço multilocatário para conexão de aplicativos por meio da nuvem.**

Dentro de um namespace, você pode usar uma ou mais instâncias de quatro mecanismos de comunicação diferentes, cada um deles conecta aplicativos de maneira diferente. As opções são:

- *Filas de*, que permitem a comunicação de um-direcional. Cada fila atua como um intermediário (às vezes chamado de um *agente*) que armazena mensagens enviadas até que eles são recebidos. Cada mensagem é recebida por um único destinatário.
- *Tópicos*, que fornecem uma comunicação unidirecional usando *assinaturas* - um único tópico pode ter várias assinaturas. Como uma fila, um tópico age como um agente, mas cada assinatura pode, opcionalmente, usar um filtro para receber apenas as mensagens que correspondem a critérios específicos.
- *Retransmissões*, que fornece comunicação bidirecional. Ao contrário das filas e dos tópicos, uma retransmissão não armazena as mensagens em trânsito. Ela não é um agente. Em vez disso, ele só as passa para o aplicativo de destino.

Ao criar uma fila, um tópico ou uma retransmissão, você atribui um nome a ele/ela. Esse nome combinado com qualquer é chamado seu espaço para nome, cria um identificador exclusivo para o objeto. Os aplicativos podem fornecer esse nome ao Barramento de Serviço e usar essa fila, tópico ou retransmissão para se comunicar uns com os outros.

Para usar qualquer um desses objetos no cenário de retransmissão, os aplicativos do Windows podem usar o WCF (Windows Communication Foundation). Para as filas e os tópicos, os aplicativos do Windows podem usar as APIs de mensagens definidas pelo Barramento de Serviço. Para tornar esses objetos mais fáceis de usar a partir de aplicativos que não são do Windows, a Microsoft fornece SDKs para Java, Node.js e outras linguagens. Você também pode acessar as filas e os tópicos usando as APIs REST no(s) HTTP(s).

É importante compreender que, embora o Barramento de Serviço em si seja executado na nuvem (ou seja, nos datacenters do Azure da Microsoft), os aplicativos que o utilizam podem ser executados em qualquer lugar. Você pode usar o Service Bus para conectar aplicativos em execução no Azure, por exemplo, ou aplicativos executados dentro de seu próprio datacenter. Também pode usá-lo para se conectar a um aplicativo em execução no Azure ou outra plataforma de nuvem com um aplicativo local ou tablets e telefones. Também é possível conectar os aparelhos domésticos, sensores e outros dispositivos para um aplicativo central ou a qualquer outra. O Barramento de Serviço é um mecanismo de comunicação na nuvem, que é acessível a partir de praticamente qualquer lugar. Como você o usa depende do que os seus aplicativos precisam fazer.

## Filas

Suponha que você queira conectar dois aplicativos usando uma fila do Barramento de Serviço. A Figura 2 ilustra essa opção.

![][2]
 
**Figura 2: Filas do Barramento de Serviço fornecem o enfileiramento de mensagens assíncronas unidirecional.**

O processo é simples: um remetente enviar uma mensagem para uma fila do Service Bus e um receptor pega essa mensagem em um momento posterior. Uma fila pode ter apenas um único destinatário, como a Figura 2 mostra. Ou vários aplicativos podem ler na mesma fila. Na última situação, cada mensagem é lida por apenas um destinatário. Para um serviço de vários casts, você deve usar um tópico.

Cada mensagem tem duas partes: um conjunto de propriedades, cada um par chave/valor e um corpo de mensagem binária. Como eles são usados depende de que um aplicativo está tentando fazer. Por exemplo, um aplicativo de envio de uma mensagem sobre uma venda recente pode incluir as propriedades *vendedor = "&"* e *valor = 10000*. O corpo da mensagem pode conter uma imagem digitalizada do contrato assinado da venda ou, se não houver um, apenas permanecerão vazio.

Um receptor pode ler uma mensagem de uma fila do Service Bus de duas maneiras diferentes. A primeira opção, chamada *ReceiveAndDelete*, remove uma mensagem da fila e a exclui imediatamente. Isso é simples, mas se o receptor falhar antes de terminar de processar a mensagem, a mensagem será perdida. Porque ele é removido da fila, nenhum outro receptor pode acessá-lo.

A segunda opção, *PeekLock*, é destinada a ajudar com esse problema. Assim como **ReceiveAndDelete**, uma leitura **PeekLock** remove uma mensagem da fila. Ela não exclui a mensagem, no entanto. Em vez disso, ela bloqueia a mensagem, tornando-a invisível para outros destinatários, e espera por um dos três eventos:

- Se o receptor processar a mensagem com êxito, ele a denominará como **Concluída** e a fila excluirá a mensagem.
- Se o receptor decidir que não é possível processar a mensagem com êxito, ele a denominará como **Abandonar**. A fila remove o bloqueio da mensagem e o torna disponível para outros destinatários.
- Se o receptor chama nenhuma delas dentro de um período de tempo configurável (por padrão, 60 segundos), a fila supõe que o receptor falhou. Nesse caso, comporta-se como se o destinatário tivesse chamado **Abandonar**, disponibilizando a mensagem para outros destinatários.

Observe o que pode acontecer aqui: A mesma mensagem pode ser entregue duas vezes, talvez para dois destinatários diferentes. Aplicativos usando filas do Service Bus devem estar preparados para isso. Para facilitar a detecção de duplicidades, cada mensagem tem uma propriedade **MessageID** exclusiva que, por padrão, permanece a mesma, independentemente de quantas vezes a mensagem é lida em uma fila.

Filas são úteis em algumas situações. Eles permitem que os aplicativos se comuniquem mesmo quando ambos não são executados ao mesmo tempo, algo que é especialmente útil com lotes e aplicativos móveis. Uma fila com vários receptores também fornece balanceamento de carga automático, desde que as mensagens enviadas sejam distribuídas entre esses receptores.

## Tópicos

Úteis como são, as filas nem sempre são a solução certa. Às vezes, os tópicos do Barramento de Serviço são melhores. Figura 3 ilustra a ideia.

![][3]
 
**Figura 3: Com base no filtro que especifica um aplicativo de assinatura, ele pode receber algumas ou todas as mensagens enviadas para um tópico do Service Bus.**

Um *tópico* é semelhante, de muitas formas, a uma fila. Os remetentes enviam mensagens a um tópico da mesma maneira como enviam mensagens para uma fila e essas mensagens parecem com filas. A grande diferença é que os tópicos permitem que cada aplicativo receptor crie sua própria *assinatura* definindo um *filtro*. Um assinante verá apenas as mensagens que correspondem ao filtro. Por exemplo, a Figura 3 mostra um remetente e um tópico com três assinantes, cada um com seu próprio filtro:

- Assinante 1 recebe apenas as mensagens que contêm a propriedade *vendedor = "&"*.
- Assinante 2 recebe mensagens que contêm a propriedade *vendedor = "Ruby"* e/ou conter um *valor* propriedade cujo valor é maior que 100.000. Talvez Ruby seja a gerente de vendas e, portanto, ela deseja ver próprias vendas e todas as vendas grandes independentemente de quem realizá-las.
- Assinante 3 definiu seu filtro como *True*, que significa que ele recebe todas as mensagens. Por exemplo, esse aplicativo pode ser responsável por manter uma trilha de auditoria e, portanto, ele precisa ver todas as mensagens.

Assim como nas filas, os assinantes de um tópico podem ler as mensagens usando **ReceiveAndDelete** ou **PeekLock**. Ao contrário das filas, no entanto, uma única mensagem enviada a um tópico pode ser recebida por várias assinaturas. Essa abordagem, conhecida como *publicar e assinar* (ou *pub/sub*), é útil sempre que vários aplicativos estão interessados nas mesmas mensagens. Definindo o filtro à direita, cada assinante pode tocar na parte de fluxo de mensagens que precisa ver.

## Retransmissão

As filas e os tópicos fornecem uma comunicação unidirecional assíncrona por meio de um agente. O tráfego flui em apenas uma direção, e não há nenhuma conexão direta entre remetentes e receptores. Mas e se você não quiser isso? Suponha que seus aplicativos precisam enviar e receber mensagens, ou talvez você deseja um link direto entre eles e você não precisa de um agente para armazenar mensagens. Para lidar com cenários como este, o Barramento de Serviço fornece *retransmissões*, como mostra a Figura 4.

![][4]
 
**Figura 4: a retransmissão do Barramento de Serviço fornece comunicação bidirecional síncrona entre os aplicativos.**

Esta é a questão óbvia para perguntar sobre retransmissões: por que usar um? Mesmo que eu não precise filas, por que tornar os aplicativos se comuniquem através de um serviço de nuvem, e não apenas interagem diretamente? A resposta é que falando diretamente pode ser mais difícil do que você imagina.

Suponha que você deseja conectar dois aplicativos locais, ambos em execução dentro de data centers corporativos. Cada um desses aplicativos fica atrás de um firewall, e cada centro de dados provavelmente usará a conversão de endereços de rede (NAT). O firewall bloqueia os dados de entrada em tudo menos algumas portas e NAT significa que cada aplicativo da máquina seja executado e não tenha um endereço IP fixo que você pode acessar diretamente de fora do data center. Sem alguma ajuda extra, conectar esses aplicativos por meio da Internet pública é algo problemático.

Uma retransmissão do Barramento de Serviço pode ajudar. Para a comunicação bidirecional por meio de uma retransmissão, cada aplicativo estabelece uma conexão TCP de saída com o Service Bus e mantém aberta. Toda a comunicação entre os dois aplicativos percorre essas conexões. Porque cada conexão foi estabelecida de dentro do data center, o firewall permite o tráfego de entrada para cada aplicativo sem abrir novas portas. Essa abordagem também contorna o problema NAT, porque cada aplicativo tem um ponto de extremidade consistente em toda a comunicação. Trocando dados por meio de retransmissão, os aplicativos podem evitar os problemas que seriam caso contrário dificultam a comunicação.

Para usar retransmissões do Barramento de Serviço, aplicativos se baseiam no WCF (Windows Communication Foundation). O Service Bus fornece ligações do WCF que tornam simples para aplicativos do Windows interagir por meio de retransmissões. Aplicativos que já usam o WCF podem normalmente apenas especificar uma dessas vinculações e conversar entre si através de uma retransmissão. Ao contrário das filas e dos tópicos, no entanto, usar as retransmissões de aplicativos não Windows, embora seja possível, requer um esforço de programação. Não há bibliotecas padrão fornecidas.

Ao contrário das filas e dos tópicos, os aplicativos não criam as retransmissões explicitamente. Em vez disso, quando um aplicativo que deseja receber mensagens estabelece uma conexão TCP com o Service Bus, uma retransmissão é criada automaticamente. Quando a conexão for interrompida, a retransmissão é excluída. Para permitir que um aplicativo encontre a retransmissão criada por um ouvinte específico, o Barramento de Serviço fornece um registro que permite que os aplicativos localizem uma retransmissão específica por nome.

As retransmissões são a solução ideal quando você precisa direcionar a comunicação. Por exemplo, considere um sistema de reservas de viagens em execução em um data center local que deve ser acessado de outros computadores, dispositivos móveis e quiosques de check-in. Aplicativos em execução em todos esses sistemas poderiam confiar nas retransmissões do Service Bus na nuvem para se comunicar, onde talvez esteja em execução.

## Resumo

A conexão de aplicativos sempre fez parte da criação de soluções completas e a variedade de cenários que exigem aplicativos e serviços que se comuniquem entre si estiver definida para aumentar mais assim como aplicativos e dispositivos que estão conectados à Internet. Fornecendo tecnologias baseadas em nuvem para fazer isso por meio de filas, tópicos e retransmissões, o Barramento de Serviço visa tornar essa função essencial mais fácil de implementar e mais amplamente disponível.

## Próximas etapas

Agora que você aprendeu os conceitos básicos do barramento de serviço do Azure, siga estes links para obter mais informações.

- Como usar as [filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
- Como usar os [tópicos do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Como usar a [retransmissão do Barramento de Serviço](service-bus-dotnet-how-to-use-relay.md)
- [Exemplos do Barramento de Serviço](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png

<!---HONumber=AcomDC_0831_2016-->