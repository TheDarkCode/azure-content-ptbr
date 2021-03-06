<properties
    pageTitle="Adicionar o conector do Banco de Dados SQL em Aplicativos Lógicos | Microsoft Azure"
    description="Visão geral do conector do Banco de Dados SQL do Azure com parâmetros da API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/25/2016"
   ms.author="mandia"/>


# Introdução ao conector do Banco de Dados SQL
Usando o conector do Banco de Dados SQL, crie fluxos de trabalho para sua organização que gerenciam dados nas tabelas.

Com o Banco de Dados SQL, você:

- Compile o fluxo de trabalho adicionando um novo cliente a um banco de dados de clientes ou atualizando um pedido em um banco de dados de pedidos.
- Use as ações para obter uma linha de dados, inserir uma nova linha e até mesmo excluir. Por exemplo, quando um registro é criado no Dynamics CRM Online (um gatilho), insira uma linha em um Banco de Dados SQL do Azure (uma ação).

Este tópico mostra como usar o conector do Banco de Dados SQL em um aplicativo lógico e também lista as ações.

>[AZURE.NOTE] Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA).

Para saber mais sobre Aplicativos Lógicos, confira [O que são aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao Banco de Dados SQL do Azure

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma conexão estabelece conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Banco de Dados SQL, você cria uma *conexão* do Banco de Dados SQL. Para criar uma conexão, insira as credenciais que normalmente usa para acessar o serviço ao qual você está se conectando. Desse modo, no Banco de Dados SQL, insira suas credenciais do Banco de Dados SQL para criar a conexão.

#### Criar a conexão

>[AZURE.INCLUDE [Criar a conexão com o SQL Azure](../../includes/connectors-create-api-sqlazure.md)]

## Usar um gatilho

Esse conector não tem gatilhos. Use outros gatilhos para iniciar o aplicativo lógico, como um gatilho de Recorrência, um gatilho de Webhook HTTP, gatilhos disponíveis com outros conectores e muito mais. [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) fornece um exemplo.

## Usar uma ação
	
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **Adicionar uma ação**, **Adicionar uma condição** ou uma das opções **Mais**.

	![](./media/connectors-create-api-sqlazure/add-action.png)

2. Escolha **Adicionar uma ação**.

3. Na caixa de texto, digite "sql" para obter uma lista de todas as ações disponíveis.

	![](./media/connectors-create-api-sqlazure/sql-1.png)

4. Em nosso exemplo, escolha **SQL Server – obter linha**. Se uma conexão já existir, escolha o **Nome da tabela** na lista suspensa e insira a **ID da Linha** que deseja retornar.

	![](./media/connectors-create-api-sqlazure/sample-table.png)

	Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-sqlazure.md#create-the-connection) neste tópico descreve estas propriedades.

	> [AZURE.NOTE] Nesse exemplo, retornamos uma linha de uma tabela. Para ver os dados nessa linha, adicione outra ação que cria um arquivo usando os campos da tabela. Por exemplo, adicione uma ação do OneDrive que usa os campos FirstName e LastName para criar um novo arquivo na conta de armazenamento de nuvem.

5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.


## Detalhes técnicos

## Ações do Banco de Dados SQL
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. O conector do Banco de Dados SQL inclui as ações a seguir.

|Ação|Descrição|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Executa um procedimento armazenado no SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Recupera uma única linha de uma tabela SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Recupera linhas de uma tabela SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Insere uma nova linha em uma tabela SQL|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|Exclui uma linha de uma tabela SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Recupera as tabelas de um banco de dados SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Atualiza uma linha existente em uma tabela SQL|

### Detalhes da ação

Nesta seção, consulte os detalhes específicos sobre cada ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.


#### Executar procedimento armazenado
Executa um procedimento armazenado no SQL.

| Nome da Propriedade| Nome de exibição |Descrição|
| ---|---|---|
|procedure * | Nome do procedimento | O nome do procedimento armazenado que você deseja executar |
|parameters * | Parâmetros de entrada | Os parâmetros são dinâmicos e se baseiam no procedimento armazenado escolhido. <br/><br/> Por exemplo, se você estiver usando o banco de dados de exemplo Adventure Works, escolha o procedimento armazenado *ufnGetCustomerInformation*. O parâmetro de entrada **ID do Cliente** é exibido. Insira "6" ou uma das outas IDs de cliente. |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da saída
ProcedureResult: carrega o resultado da execução do procedimento armazenado

| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|OutputParameters|objeto|Valores do parâmetro de saída |
|ReturnCode|inteiro|Código de retorno de um procedimento |
|ResultSets|objeto| Conjuntos de resultados|


#### Obter linha 
Recupera uma única linha de uma tabela SQL.

| Nome da Propriedade| Nome de exibição |Descrição|
| ---|---|---|
|table * | Nome da tabela |Nome da tabela SQL|
|id * | Id da linha |O identificador exclusivo da linha a ser recuperado|

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da saída
Item

| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|


#### Obter linhas 
Recupera linhas de uma tabela SQL.

|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|table*|Nome da tabela|Nome da tabela SQL|
|$skip|Ignorar contagem|Número de entradas a serem ignoradas (padrão = 0)|
|$top|Obter Contagem Máxima|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|Consulta de filtro|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|Ordenar por|Uma consulta orderBy do ODATA para especificar a ordem das entradas|

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|


#### Inserir linha 
Insere uma nova linha em uma tabela SQL.

|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|table*|Nome da tabela|Nome da tabela SQL|
|item*|Linha|Linha para inserir na tabela especificada no SQL|

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da saída
Item

| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|


#### Excluir linha 
Exclui uma linha de uma tabela SQL.

|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|table*|Nome da tabela|Nome da tabela SQL|
|id*|Id da linha|Identificador exclusivo da linha a ser excluída|

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
Nenhum.

#### Obter tabelas 
Recupera tabelas de um banco de dados SQL.

Não existem parâmetros para esta chamada.

##### Detalhes da saída 
TablesList

| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|

#### Atualizar linha 
Atualiza uma linha existente em uma tabela SQL.

|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|table*|Nome da tabela|Nome da tabela SQL|
|id*|Id da linha|Identificador exclusivo da linha a ser atualizada|
|item*|Linha|Linhas com valores atualizados|

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da saída  
Item

| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|


### Respostas HTTP

Ao fazer chamadas a diferentes ações, você pode obter determinadas respostas. A tabela a seguir descreve as respostas e suas descrições:

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## Próximas etapas

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->