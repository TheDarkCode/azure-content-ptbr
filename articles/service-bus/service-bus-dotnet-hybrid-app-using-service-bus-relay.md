<properties
	pageTitle="Aplicativo híbrido local/na nuvem (.NET) | Microsoft Azure"
	description="Saiba como criar um aplicativo híbrido .NET local/na nuvem usando a retransmissão do Barramento de Serviço do Azure."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/23/2016"
	ms.author="sethm"/>

# Aplicativo híbrido .NET local/na nuvem usando retransmissão do Barramento de Serviço do Azure

## Introdução

Este artigo descreve como compilar um aplicativo de nuvem híbrido com o Microsoft Azure e o Visual Studio. Este tutorial pressupõe que você não tem uma experiência anterior com o Azure. Em menos de 30 minutos, você terá um aplicativo que usa vários recursos do Azure em funcionamento na nuvem.

Você aprenderá:

-   Criar ou adaptar um serviço Web existente para consumo por uma solução de Web.
-   Usar a Retransmissão do Barramento de Serviço para compartilhar dados entre um aplicativo do Azure e um serviço Web hospedado em outro lugar.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Como a retransmissão do Barramento de Serviço ajuda com soluções híbridas

As soluções de negócios geralmente são compostas por uma combinação de código personalizado escrito para lidar com os requisitos de negócios novos e exclusivos e a funcionalidade existente fornecida pelas soluções e sistemas que já estão estabelecidos.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de requisitos de escala e custos operacionais mais baixos. Ao fazer isso, eles descobrem serviços ativos existentes que gostariam de aproveitar, como blocos de construção para suas soluções estão dentro do firewall corporativo e fora de alcance fácil para acesso pela solução de nuvem. Muitos serviços internos não são construídos ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

A retransmissão do Barramento de Serviço foi desenvolvida para o caso de utilizar os serviços Web do WCF (Windows Communication Foundation) existentes e torná-los acessíveis com segurança para soluções que residem fora do perímetro corporativo, sem exigir alterações intrusivas na infraestrutura da rede corporativa. Esses serviços de retransmissão do Barramento de Serviço ainda estão hospedados dentro de seu ambiente existente, mas delegam a escuta de entrada sessões e solicitações para o Barramento de Serviço hospedado na nuvem. O Barramento de Serviço também protege esses serviços contra acesso não autorizado usando a autenticação [SAS (Assinatura de Acesso Compartilhado)](service-bus-sas-overview.md).

## Cenário da solução

Neste tutorial, você criará um site ASP.NET que permitirá ver uma lista de produtos na página do inventário de produtos.

![][0]

O tutorial assume que você tem informações sobre produtos em um sistema local existente e utiliza Retransmissão do Barramento de Serviço para acessar esse sistema. Isso é simulado por um serviço Web executado em um aplicativo de console simples e com o suporte de um conjunto de produtos na memória. Você poderá executar esse aplicativo de console em seu próprio computador e implantar a função web no Azure. Fazendo isso, você verá como a função de web em execução no datacenter do Azure realmente será chamada em seu computador, embora seu computador certamente resida atrás de pelo menos um firewall e de uma camada de NAT (conversão de endereços de rede).

A captura de tela da página inicial do aplicativo Web completo é mostrada abaixo.

![][1]

## Configurar o ambiente de desenvolvimento

Antes começar a desenvolver os aplicativos do Azure, obtenha as ferramentas e configure seu ambiente de desenvolvimento.

1.  Instale o SDK do Azure para .NET em [Obter ferramentas e SDK][].

2. 	Clique em **Instalar o SDK** da versão do Visual Studio que você está usando. As etapas neste tutorial usam o Visual Studio 2015.

4.  Quando for solicitado a executar ou salvar o instalador, clique em **Executar**.

5.  No **Web Platform Installer**, clique em **Instalar** e prossiga com a instalação.

6.  Quando a instalação estiver concluída, você terá tudo o que é necessário para iniciar o desenvolvimento do aplicativo. O SDK inclui ferramentas que permitem que você desenvolva facilmente aplicativos do Azure no Visual Studio. Se você não tiver instalado o Visual Studio, o SDK também instala o Visual Studio Express gratuito.

## Criar um namespace

Para começar a usar os recursos de Barramento de Serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Criar um servidor local

Primeiro você irá criar um sistema de catálogo de produtos (fictício) local. Será muito simples, você pode ver isso como uma representação de um sistema de catálogo de produtos real local com uma superfície de serviço completa que estamos tentando integrar.

Este projeto é um aplicativo de console do Visual Studio e usa o [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas do Barramento de Serviço e as definições da configuração.

### Criar o projeto

1.  Utilizando privilégios do administrador, inicie o Microsoft Visual Studio. Para iniciar o Visual Studio com privilégios do administrador, clique com o botão direito no ícone do programa **Visual Studio** e clique em **Executar como administrador**.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.

3.  Em **Modelos Instalados**, abaixo de **Visual C#**, clique em **Aplicativo de Console**. Na caixa **Nome**, digite o nome **ProductsServer**:

    ![][11]

4.  Clique em **OK** para criar o projeto **ProductsServer**.

7.  Se você já tiver instalado o Gerenciador de Pacotes NuGet para Visual Studio, vá para a próxima etapa. Caso contrário, visite [NuGet][] e clique em [Instalar o NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os prompts para instalar o Gerenciador de Pacotes NuGet e, em seguida, reinicie o Visual Studio.

7.  No Gerenciador de Soluções, clique com o botão direito no projeto **ProductsServer** e clique em **Gerenciar Pacotes NuGet**.

8.  Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de uso.

    ![][13]

    Observe que os assemblies do cliente necessários agora são referenciados.

9.  Adicione uma nova classe para seu contrato de produto. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsServer**, clique em **Adicionar** e em **Classe**.

10. Na caixa **Nome**, digite o nome **ProductsContract.cs**. Clique em **Adicionar**.

11. Em **ProductsContract.cs**, substitua a definição do namespace pelo código a seguir, que define o contrato do serviço.

	```
	namespace ProductsServer
	{
	    using System.Collections.Generic;
	    using System.Runtime.Serialization;
	    using System.ServiceModel;
	
	    // Define the data contract for the service
	    [DataContract]
	    // Declare the serializable properties.
	    public class ProductData
	    {
	        [DataMember]
	        public string Id { get; set; }
	        [DataMember]
	        public string Name { get; set; }
	        [DataMember]
	        public string Quantity { get; set; }
	    }
	
	    // Define the service contract.
	    [ServiceContract]
	    interface IProducts
	    {
	        [OperationContract]
	        IList<ProductData> GetProducts();
	
	    }
	
	    interface IProductsChannel : IProducts, IClientChannel
	    {
	    }
	}
	```

12. Em Program.cs, substitua a definição do namespace pelo código a seguir, que adiciona o serviço de perfil e o host para ele.

	```
	namespace ProductsServer
	{
	    using System;
	    using System.Linq;
	    using System.Collections.Generic;
	    using System.ServiceModel;
	
	    // Implement the IProducts interface.
	    class ProductsService : IProducts
	    {
	
	        // Populate array of products for display on website
	        ProductData[] products =
	            new []
	                {
	                    new ProductData{ Id = "1", Name = "Rock",
	                                     Quantity = "1"},
	                    new ProductData{ Id = "2", Name = "Paper",
	                                     Quantity = "3"},
	                    new ProductData{ Id = "3", Name = "Scissors",
	                                     Quantity = "5"},
	                    new ProductData{ Id = "4", Name = "Well",
	                                     Quantity = "2500"},
	                };
	
	        // Display a message in the service console application
	        // when the list of products is retrieved.
	        public IList<ProductData> GetProducts()
	        {
	            Console.WriteLine("GetProducts called.");
	            return products;
	        }
	
	    }
	
	    class Program
	    {
	        // Define the Main() function in the service application.
	        static void Main(string[] args)
	        {
	            var sh = new ServiceHost(typeof(ProductsService));
	            sh.Open();
	
	            Console.WriteLine("Press ENTER to close");
	            Console.ReadLine();
	
	            sh.Close();
	        }
	    }
	}
	```

13. No Gerenciador de Soluções, clique duas vezes no arquivo **App.config** para abri-lo no editor do Visual Studio. Na parte inferior do elemento**&lt;system.ServiceModel&gt;** (mas ainda em &lt;system.ServiceModel&gt;), adicione o seguinte código XML. Substitua *seuNamespaceDeServiço* pelo nome do seu namespace e *suaChave* pela chave SAS recuperada anteriormente no portal:

    ```
    <system.serviceModel>
	...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Ainda em App.config, no elemento **&lt;appSettings&gt;**, substitua o valor da cadeia de conexão pela cadeia de conexão obtida anteriormente no portal.

	```
	<appSettings>
   	<!-- Service Bus specific app settings for messaging connections -->
   	<add key="Microsoft.ServiceBus.ConnectionString"
	       value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
	</appSettings>
	```

14. Pressione **Ctrl+Shift+B** ou no menu **Compilar**, clique em **Compilar Solução** para compilar o aplicativo e verificar a precisão de seu trabalho até o momento.

## Criar um aplicativo ASP.NET

Nesta seção você criará um aplicativo ASP.NET simples que exibe os dados recuperados do seu serviço de produto.

### Criar o projeto

1.  Certifique-se de que o Visual Studio está sendo executado com os privilégios de administrador.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.

3.  Em **Modelos instalados**, em **Visual C#**, clique em **Aplicativo Web ASP.NET**. Nomeie o projeto **ProductsPortal**. Em seguida, clique em **OK**.

    ![][15]

4.  Na lista **Selecionar um modelo**, clique em **MVC**.

6.  Marque a caixa **Host na nuvem**.

    ![][16]

5. Clique no botão **Alterar Autenticação**. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**. Para este tutorial, você está implantando um aplicativo que não precisa de um logon de usuário.

	![][18]

6. 	Na seção **Microsoft Azure** da caixa de diálogo **Novo Projeto ASP.NET**, certifique-se de que **Host na nuvem** está selecionado e que **Serviço de Aplicativo** está selecionado na lista suspensa.

	![][19]

7. Clique em **OK**.

8. Agora você deve configurar os recursos do Azure para um novo aplicativo Web. Siga todas as etapas na seção [Configurar recursos do Azure para um novo aplicativo Web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Em seguida, retorne a este tutorial e prossiga para a próxima etapa.

5.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Modelos**, clique em **Adicionar**e em **Classe**. Na caixa **Nome**, digite o nome **Product.cs**. Clique em **Adicionar**.

    ![][17]

### Modificar o aplicativo web

1.  No arquivo Product.cs, no Visual Studio, substitua a definição de namespace existente pelo código a seguir.

	```
	// Declare properties for the products inventory.
 	namespace ProductsWeb.Models
	{
    	public class Product
    	{
    	    public string Id { get; set; }
    	    public string Name { get; set; }
    	    public string Quantity { get; set; }
    	}
	}
	```

2.  No Gerenciador de Soluções, expanda a pasta **Controladores**, clique duas vezes no arquivo **HomeController.cs** para abri-lo no Visual Studio.

3. Em **HomeController.cs**, substitua a definição do namespace existente pelo código a seguir.

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Collections.Generic;
	    using System.Web.Mvc;
	    using Models;
	
	    public class HomeController : Controller
	    {
	        // Return a view of the products inventory.
	        public ActionResult Index(string Identifier, string ProductName)
	        {
	            var products = new List<Product>
	                {new Product {Id = Identifier, Name = ProductName}};
	            return View(products);
	        }
	     }
	}
	```

3.  No Gerenciador de Soluções, expanda a pasta Views\\Shared e clique duas vezes em **\_Layout.cshtml** para abri-lo no editor do Visual Studio.

5.  Altere todas as ocorrências de **Meu aplicativo ASP.NET** para **Produtos da LITWARE**.

6. Remova os links **Página Inicial**, **Sobre** e **Contato**. No exemplo a seguir, exclua o código destacado.

	![][41]

7.  No Gerenciador de Soluções, expanda a pasta Views\\Home e clique duas vezes em **Index.cshtml** para abri-lo no editor do Visual Studio. Substitua todo o conteúdo do arquivo pelo código a seguir.

	```
	@model IEnumerable<ProductsWeb.Models.Product>
	
	@{
	 		ViewBag.Title = "Index";
	}
	
	<h2>Prod Inventory</h2>
	
	<table>
	  		<tr>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Name)
	      		</th>
	              <th></th>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Quantity)
	      		</th>
	  		</tr>
	
	@foreach (var item in Model) {
	  		<tr>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Name)
	      		</td>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Quantity)
	      		</td>
	  		</tr>
	}
	
	</table>
	```

9.  Para verificar a precisão de seu trabalho até o momento, você pode pressionar **Ctrl+Shift+B** para compilar o projeto.


### Executar o aplicativo localmente

Execute o aplicativo para verificar se ele funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto e selecione **Definir como Projeto de Inicialização**.
2.  No Visual Studio, pressione F5.
3.  Seu aplicativo deve aparecer em execução em um navegador.

    ![][21]

## Juntar as peças

A próxima etapa é vincular o servidor de produtos local com o aplicativo ASP.NET.

1.  Se ele ainda não estiver aberto, no Visual Studio, reabra o projeto **ProductsPortal** criado na seção “Criar um Aplicativo ASP.NET”.

2.  Semelhante à etapa na seção "Criar um servidor local", adicione o pacote NuGet às referências do projeto. No Gerenciador de Soluções, clique com o botão direito no projeto **ProductsPortal** e clique em **Gerenciar Pacotes NuGet**.

3.  Procure "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure**. Então conclua a instalação e feche esta caixa de diálogo.

4.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsPortal**, clique em **Adicionar** e em **Item Existente**.

5.  Navegue até o arquivo **ProductsContract.cs** do projeto de console **ProductsServer**. Clique para realçar ProductsContract.cs. Clique na seta para baixo próxima a **Adicionar** e clique em **Adicionar como Link**.

	![][24]

6.  Agora abra o arquivo **HomeController.cs** no editor do Visual Studio e substitua a definição de namespace pelo código a seguir. Certifique-se de substituir *yourServiceNamespace* pelo nome do seu namespace de serviço e *yourKey* pela sua chave SAS. Isso permitirá que o cliente chame o serviço local, retornando o resultado da chamada.

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Linq;
	    using System.ServiceModel;
	    using System.Web.Mvc;
	    using Microsoft.ServiceBus;
	    using Models;
	    using ProductsServer;
	
	    public class HomeController : Controller
	    {
	        // Declare the channel factory.
	        static ChannelFactory<IProductsChannel> channelFactory;
	
	        static HomeController()
	        {
	            // Create shared access signature token credentials for authentication.
	            channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
	                "sb://yourServiceNamespace.servicebus.windows.net/products");
	            channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
	                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                    "RootManageSharedAccessKey", "yourKey") });
	        }
	
	        public ActionResult Index()
	        {
	            using (IProductsChannel channel = channelFactory.CreateChannel())
	            {
	                // Return a view of the products inventory.
	                return this.View(from prod in channel.GetProducts()
	                                 select
	                                     new Product { Id = prod.Id, Name = prod.Name,
	                                         Quantity = prod.Quantity });
	            }
	        }
	    }
	}
	```

7.  No Gerenciador de Soluções, clique com o botão direito na solução **ProductsPortal**, clique em **Adicionar** e em **Projeto Existente**.

8.  Navegue até o projeto **ProductsServer** e clique duas vezes no arquivo de solução **ProductsServer.csproj** para adicioná-lo.

9.  **ProductsServer** deve estar em execução para exibir os dados em **ProductsPortal**. No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal** e clique em **Propriedades**. A caixa de diálogo **Páginas da Propriedade** é exibida.

10. No lado esquerdo, clique em **Projeto de Inicialização**. No lado direito, clique em **Vários projeto de Inicialização**. Verifique se **ProductsServer** e **ProductsPortal** aparecem, nessa ordem, com **Iniciar** definido como a ação para ambos.

      ![][25]

11. Ainda na caixa de diálogo **Propriedades**, clique em **Dependências do Projeto** no lado esquerdo.

12. Na lista **Projetos**, clique em **ProductsServer**. Confirme se **ProductsPortal** não **está** selecionado.

14. Na lista **Projetos**, clique em **ProductsPortal**. Verifique se **ProductsServer** está selecionado.

    ![][26]

15. Clique em **OK** na caixa de diálogo **Páginas da Propriedade**.

## Executar o projeto localmente

Para testar o aplicativo localmente, no Visual Studio, pressione **F5**. O servidor local (**ProductsServer**) deve iniciar primeiro e, então, o aplicativo **ProductsPortal** deve iniciar em uma janela do navegador. Desta vez, você verá que o inventário de produtos lista dados recuperados do sistema local de serviço de produto.

![][10]

Pressione **Atualizar** na página **ProductsPortal**. Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` de **ProductsServer** for chamado.

## Implantar o projeto ProductsPortal em um aplicativo Web do Azure

A próxima etapa é converter o front-end **ProductsPortal** em um aplicativo Web do Azure. Primeiro, implante o projeto **ProductsPortal**, seguindo todas as etapas na seção [Implantar o projeto Web para o aplicativo Web do Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Após a implantação ser concluída, retorne a este tutorial e prossiga para a próxima etapa.

Copie a URL do aplicativo Web implantado, pois você precisará dela na próxima etapa. Você também pode obter essa URL na janela Atividade do Serviço de Aplicativo do Azure no Visual Studio:

![][9]
   

> [AZURE.NOTE] Você verá uma mensagem de erro na janela do navegador quando o projeto Web **ProductsPortal** for iniciado automaticamente após a implantação. Isso é esperado e ocorre porque o aplicativo **ProductsServer** não está sendo executado ainda.

### Defina ProductsPortal como o aplicativo Web

Antes de executar o aplicativo na nuvem, você deve garantir que **ProductsPortal** seja inicializado de dentro do Visual Studio como um aplicativo Web.

1. No Visual Studio, clique com o botão direito no projeto **ProjectsPortal** e em **Propriedades**.

3. Na coluna à esquerda, clique em **Web**.

5. Na seção **Iniciar Ação**, clique no botão **Iniciar URL** e na caixa de texto, insira a URL de seu aplicativo Web implantado anteriormente; por exemplo, `http://productsportal1234567890.azurewebsites.net/`.

	![][27]

6. No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.

7. No menu Compilar no Visual Studio, clique em **Recompilar Solução**.

## Executar o aplicativo

2.  Pressione F5 para compilar e executar o aplicativo. O servidor local (o aplicativo de console **ProductsServer**) deve iniciar primeiro, em seguida, o aplicativo **ProductsPortal** deve iniciar em uma janela do navegador, como mostrado na captura de tela a seguir. Observe novamente que o inventário de produtos lista os dados recuperados no sistema local do serviço de produto e exibe os dados em um aplicativo Web. Verifique a URL para saber se **ProductsPortal** está em execução na nuvem, como um aplicativo Web do Azure.

    ![][1]

	> [AZURE.IMPORTANT] O aplicativo de console **ProductsServer** deve estar em execução e ser capaz de fornecer dados para o aplicativo **ProductsPortal**. Se o navegador exibir um erro, aguarde mais alguns segundos para **ProductsServer** carregar e exibir a mensagem a seguir. Em seguida, pressione **Atualizar** no navegador.

	![][37]

3. No navegador, pressione **Atualizar** na página **ProductsPortal**. Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` de **ProductsServer** for chamado.

	![][38]

## Próximas etapas  

Para obter mais informações sobre o Barramento de Serviço, consulte os seguintes recursos:

* [Barramento de Serviço do Azure][sbwacom]
* [Como usar as filas do Barramento de Serviço][sbwacomqhowto]


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obter ferramentas e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md

<!---HONumber=AcomDC_0824_2016-->