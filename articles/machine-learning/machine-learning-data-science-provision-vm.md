<properties 
	pageTitle="Provisionar a Máquina Virtual de Ciência de Dados da Microsoft | Microsoft Azure" 
	description="Configure e crie uma Máquina Virtual de Ciência de Dados no Azure para realizar a análise e o aprendizado de máquina." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="bradsev" />


# Provisionar uma Máquina Virtual de Ciência de Dados da Microsoft

A Máquina Virtual de Ciência de Dados da Microsoft é uma imagem de máquina virtual (VM) do Azure pré-instalada e configurada com diversas ferramentas populares que são usadas para a análise de dados e o aprendizado de máquina. As ferramentas incluídas são:

- Microsoft R Server Developer Edition
- Distribuição do Anaconda Python
- Bloco de anotações do Jupyter (com R, kernels Python)
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server 2016 Developer Edition
- Ferramentas de Aprendizado de Máquina
    - [CNTK (Kit de Ferramentas de Rede Computacional)](https://github.com/Microsoft/CNTK): um software de aprendizado aprofundado da Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um sistema de aprendizado de máquina rápido com suporte a técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
    - [Rattle](http://rattle.togaware.com/) (a "R Analytical Tool To Learn Easily" – Ferramenta Analítica do R para Aprender com Facilidade): uma ferramenta que facilita a introdução à análise de dados e ao aprendizado de máquina em R, com uma exploração de dados baseada em GUI e modelagem com geração de código R automática.
    - [mxnet](https://github.com/dmlc/mxnet): uma estrutura de aprendizado profunda criada para eficiência e flexibilidade
- Bibliotecas em R e Python para uso em Aprendizado de Máquina do Azure e outros serviços do Azure
- Git, incluindo Git Bash para trabalhar com repositórios de código-fonte, incluindo GitHub e Visual Studio Team Services
- Portas do Windows de vários utilitários de linha de comando populares do Linux (incluindo awk, sed, perl, grep, find, wget, curl, etc.) acessíveis pelo prompt de comando.


A ciência de dados envolve a iteração em uma sequência de tarefas: encontrar, carregar e pré-processar dados, compilar e testar modelos, bem como implantar os modelos para o consumo em aplicativos inteligentes. Não é incomum que os cientistas de dados usem uma variedade de ferramentas para executar essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e baixar e instalá-las. A Máquina Virtual de Ciência de Dados da Microsoft pode facilitar essa carga fornecendo uma imagem pronta para uso que pode ser provisionada no Azure com todas as diversas ferramentas populares pré-instaladas e configuradas.

A Máquina Virtual de Ciência de Dados da Microsoft impulsiona seu projeto de análise. Ela permite que você trabalhe em tarefas em uma variedade de linguagens, incluindo R, Python, SQL e C#. O Visual Studio fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft.

Não há encargos de software para esta imagem da VM de ciência de dados. Você só paga pelas taxas de uso do Azure que dependem do tamanho da máquina virtual que será provisionada com esta imagem da VM. Mais detalhes sobre as taxas de computação podem ser encontrados [aqui](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Pré-requisitos

Antes de criar uma Máquina Virtual de Ciência de Dados da Microsoft, você deve ter o seguinte:

- **Uma assinatura do Azure**: para obter uma, confira [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Uma conta de armazenamento do Azure**: para criar uma, confira [Criar uma conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account) Como alternativa, a conta de armazenamento pode ser criada como parte do processo de criação da VM se você não desejar usar uma conta existente.


## Criar sua Máquina Virtual de Ciência de Dados da Microsoft

Veja as etapas para criar uma instância da Máquina Virtual de Ciência de Dados da Microsoft:

1.	Navegue até a listagem de máquinas virtuais no [Portal do Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Clique no botão **Criar** na parte inferior para ser levado para um assistente.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 O assistente usado para criar a Máquina Virtual de Ciência de Dados da Microsoft exige **entradas** para cada uma das **cinco etapas** enumeradas à direita da figura. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:

     **a. Noções básicas**:

   - **Nome**: o nome do servidor de ciência de dados que você está criando.
   - **Nome de Usuário**: ID de logon da conta de administrador
   - **Senha**: senha da conta de administrador
   - **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado
   - **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente
   - **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede


     **b. Tamanho**:

   - Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Você pode obter mais opções de tamanhos de VM selecionando “Exibir Tudo”

     **c. Configurações**:


   - **Tipo de disco**: escolha Premium se você preferir uma SSD (unidade de estado sólido); caso contrário, escolha "Padrão".
  
   - **Conta de Armazenamento**: é possível criar uma nova conta de armazenamento do Azure em sua assinatura ou usar uma já existente no mesmo *Local* que foi escolhido na etapa Noções básicas do assistente.
  
   - **Outros parâmetros**: na maioria dos casos, você simplesmente usará os valores padrão. É possível focalizar o link informativo para obter ajuda sobre um campo específico, caso você queira considerar o uso de valores não padrão.


     **d. Resumo**:

   - Verifique se todas as informações inseridas estão corretas.


     **e. Comprar**:

   - Clique em **Comprar** para iniciar o provisionamento. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**.


O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal do Azure.

## Como acessar uma Máquina Virtual de Ciência de Dados da Microsoft

Depois de criar a VM, é possível fazer logon nela usando a área de trabalho remota com as credenciais da conta de Administrador criada na seção Noções básicas da etapa 4.

Depois de criar e provisionar sua VM, você estará pronto para começar a usar as ferramentas que estão instaladas e configuradas nela. Há blocos do menu Iniciar e ícones da área de trabalho para várias das ferramentas.

## Como criar uma senha forte no servidor do bloco de anotações do Jupyter 

Execute o comando a seguir em um prompt de comando na Máquina Virtual de Ciência de Dados para criar sua própria senha forte para o servidor de bloco de anotações do Jupyter instalado na máquina.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Escolha uma senha forte quando solicitado.

Você verá o hash de senha no formato "sha1:xxxxxx" na saída. Copie esse hash de senha e substitua o hash existente em seu arquivo de configuração do bloco de anotações localizado em: **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** por um nome de parâmetro ***c.NotebookApp.password***.

Você só deve substituir o valor de hash existente que está entre aspas. As aspas e o prefixo ***sha1:*** para o valor do parâmetro precisam ser mantidos.

Por fim, você precisa interromper e reiniciar o servidor do Jupyter que está em execução na VM como uma tarefa agendada do Windows chamada “Start\_IPython\_Notebook”. Se sua nova senha não for aceita após a reinicialização desta tarefa, tente encerrar todos os processos do Python em execução no Gerenciador de Tarefas e reinicie a tarefa agendada OU tente reinicializar a máquina virtual.

## Ferramentas Instaladas na Máquina Virtual de Ciência de Dados da Microsoft

### Microsoft R Server Developer Edition
Se você quiser usar o R para sua análise, a VM tem a edição Microsoft R Server Developer instalada. O Microsoft R Server é uma plataforma de análise empresarial amplamente implementável com base em R e com suporte, escalonável e segura. Com suporte a diversas estatísticas de Big Data, modelos de previsão e recursos de aprendizado de máquina, o R Server oferece suporte a uma gama completa de análises: exploração, análise, visualização e modelagem. Usando e estendendo o software livre R, o Microsoft R Server é totalmente compatível com scripts e funções R e pacotes CRAN, a fim de analisar os dados em escala empresarial. Ele também soluciona as limitações de memória do software livre R adicionando o processamento paralelo e em blocos no Microsoft R Server, permitindo que os usuários executem análises em uma quantidade de dados muito maior do que cabe na memória principal. O Visual Studio Community Edition incluído na VM contém a extensão Ferramentas do R para Visual Studio que fornece um IDE completo para trabalhar com o R. Você também pode baixar e usar outros IDEs, bem como o [RStudio](http://www.rstudio.com).

### Python
Para o desenvolvimento com Python, as distribuições 2.7 e 3.5 do Anaconda Python foram instaladas. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar Ferramentas Python para Visual Studio (PTVS) que são instaladas na edição do Visual Studio 2015 Community ou um dos IDEs agrupado com Anaconda como IDLE ou Spyder. Você pode iniciar um desses pesquisando na barra de pesquisa (tecla **Win** + **S**).

>[AZURE.NOTE] Para apontar as Ferramentas do Python para Visual Studio para o Anaconda Python 2.7 e 3.5, você precisa criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2015 Community Edition, navegue até **Ferramentas** -> **Ferramentas do Python** -> **Ambientes do Python** e clique em **+ Personalizado**.

O Anaconda Python 2.7 é instalado em C:\\Anaconda e o Anaconda Python 3.5 é instalado em c:\\Anaconda\\envs\\py35. Consulte a [documentação do PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter as etapas detalhadas.

### Bloco de anotações do Jupyter
A distribuição do Anaconda também acompanha um bloco de anotações do Jupyter, um ambiente de compartilhamento de código e de análise. Um servidor de bloco de anotações do Jupyter foi previamente configurado com os kernels do Python 2, do Python 3 e do R. Há um ícone de área de trabalho chamado "Bloco de anotações do Jupyter" para iniciar o navegador a fim de acessar o servidor do Notebook. Se estiver usando a VM por meio da área de trabalho remota, você também poderá visitar [https://localhost:9999/](https://localhost:9999/) para acessar o servidor do bloco de anotações do Jupyter quando estiver conectado na VM.
 
>[AZURE.NOTE] Continue se você obtiver quaisquer avisos de certificado.

Empacotamos exemplos de blocos de anotações, um em Python em outro em R. Você pode ver o link para os exemplos na home page do bloco de anotações após a autenticação no bloco de anotações do Jupyter usando a senha criada na etapa anterior. Você pode encontrar blocos de anotações de exemplo do Jupyter que mostram como trabalhar com o Microsoft R Server, SQL Server 2016 R Services (análise no banco de dados), Python e outras tecnologias do Azure depois que fizer logon no Jupyter.

### Visual Studio 2015 Community edition
Visual Studio Community edition instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode ser usada para fins de avaliação e para equipes muito pequenas. Você pode consultar os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547). Abra o Visual Studio clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar**. Você também pode pesquisar programas com **Win** + **S** e inserindo “Visual Studio”. Nesse local, você pode criar projetos em linguagens como C#, Python, R e node.js. Você também encontrará plug-ins instalados que facilitam o trabalho com os serviços do Azure, como o Catálogo de Dados do Azure, o Azure HDInsight (Hadoop, Spark) e o Azure Data Lake.

>[AZURE.NOTE] Você poderá receber uma mensagem informando que seu período de avaliação expirou. Você precisa inserir suas credenciais da Conta da Microsoft ou criar uma conta gratuita e inseri-las para obter acesso ao Visual Studio Community Edition.

### SQL Server 2016 Developer Edition
Uma versão de desenvolvedor do SQL Server 2016 com os Serviços do R para execução na análise no banco de dados é fornecida na VM. Os Serviços do R fornecem uma plataforma para desenvolver e implantar aplicativos inteligentes que descobrem novas informações. Você pode usar a linguagem avançada e poderosa do R e os vários pacotes da comunidade para criar modelos e gerar previsões usando seus dados do SQL Server. Como os Serviços do R (No banco de dados) integram-se à linguagem do R com o SQL Server, é possível manter a análise próxima aos dados e eliminar os custos e riscos de segurança associados à movimentação de dados. O SQL Server 2016 Developer Edition pode ser usado apenas para fins de desenvolvimento e teste (você precisará de uma licença para executá-lo em produção).

Você pode acessar o SQL Server iniciando o **SQL Server Management Studio**. O nome da VM será populado como o Nome do Servidor. Use a Autenticação do Windows quando estiver conectado como o administrador no Windows. Quando estiver no SQL Server Management Studio, você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL.

Para habilitar a análise no banco de dados usando o Microsoft R, você precisa executar o comando a seguir como uma ação de tempo no SQL Server Management Studio depois de fazer logon como administrador do servidor.

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### As tabelas 
Várias ferramentas do Azure são instaladas na VM:
- Há um atalho da área de trabalho para acessar a documentação do SDK do Azure.
- **AzCopy**: usado para mover dados para dentro e fora de sua Conta de Armazenamento do Microsoft Azure. Basta digitar **Azcopy** em um prompt de comando para ver o uso.
- **Gerenciador de Armazenamento do Microsoft Azure**: usado para procurar os objetos armazenados em sua Conta do Armazenamento do Azure e transferir dados para dentro e fora do Armazenamento do Azure. Você pode digitar **Gerenciador de Armazenamento** na pesquisa ou encontrá-lo no menu Iniciar do Windows para acessar essa ferramenta.
- **Adlcopy**: usado para mover dados para o Azure Data Lake. Basta digitar **adlcopy** em um prompt de comando para ver o uso.
- **dtui**: usado para mover dados para dentro e fora do Azure DocumentDB, um banco de dados NoSQL na nuvem. Basta digitar **dtui** no prompt de comando.
- **Gateway de Gerenciamento de Dados da Microsoft**: permite a movimentação de dados entre fontes de dados locais e a nuvem. É usado em ferramentas como o Azure Data Factory.
- **Microsoft Azure PowerShell**: uma ferramenta usada para administrar os recursos do Azure na linguagem de script do PowerShell também é instalada em sua VM.

###Power BI

Para ajudá-lo a compilar ótimos painéis e visualizações, o **Power BI Desktop** foi instalado. Use essa ferramenta para extrair dados de fontes diferentes, criar painéis e relatórios e publicá-los na nuvem. Para saber mais, confira o site do [Power BI](http://powerbi.microsoft.com). Você pode encontrar o Power BI Desktop no menu Iniciar.

>[AZURE.NOTE] Você precisará de uma conta do Office 365 para acessar o Power BI.

## Ferramentas de desenvolvimento adicionais da Microsoft
O [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser usado para descobrir e baixar outras ferramentas de desenvolvimento da Microsoft. Também é um atalho para a ferramenta fornecida na área de trabalho de Máquina de Virtual de Ciência de Dados da Microsoft.

## Diretórios importantes na VM

| Item | Diretório |
| ------------------------------| ---------------- |
|Configurações de servidor do bloco de anotações do Jupyter | C:\\ProgramData\\jupyter |
|Diretório base de amostras do Bloco de Anotações do Jupyter| c:\\dsvm\\notebooks|
|Outras amostras | c:\\dsvm\\samples|
| Anaconda (padrão: Python 2.7) | c:\\Anaconda |
| Ambiente do Anaconda Python 3.5 | c:\\Anaconda\\envs\\py35|
|Diretório de instância do R Server Autônomo (instância padrão do R) | C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER |
| Diretório de instância do R Server no banco de dados | C:\\Program Files\\Microsoft SQL Server\\MSSQL13.MSSQLSERVER\\R\_SERVICES |
| Ferramentas diversas | c:\\dsvm\\tools|

>[AZURE.NOTE] As instâncias da Máquina Virtual de Ciência de Dados da Microsoft criadas antes da versão 1.5.0 (antes de 3 de setembro de 2016) usavam uma estrutura de diretório um pouco diferente que a descrita acima.

## Próximas etapas
Veja algumas das próximas etapas para continuar sua aprendizagem e exploração.

* Explore as várias ferramentas de ciência de dados na VM de ciência de dados clicando no menu Iniciar e conferindo as ferramentas listadas no menu
* Navegue até **C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER\\library\\RevoScaleR\\demoScripts** para obter amostras de como usar a biblioteca RevoScaleR no R, que dá suporte à análise de dados em escala empresarial.
* Leia o artigo: [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](http://aka.ms/dsvmtenthings)
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)
* Visite a [Galeria do Cortana Intelligence](http://gallery.cortanaintelligence.com) para obter amostras de aprendizado de máquina e análise de dados que usam o Cortana Intelligence Suite. Também fornecemos um ícone no menu Iniciar e na área de trabalho na máquina virtual para facilitar o acesso

<!---HONumber=AcomDC_0907_2016-->