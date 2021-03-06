<properties 
	pageTitle="Mover dados do Cassandra usando o Data Factory | Microsoft Azure" 
	description="Saiba mais sobre como mover dados de um banco de dados Cassandra local usando o Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Mover dados de um banco de dados Cassandra local usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para copiar dados de um banco de dados Cassandra local em qualquer repositório de dados na coluna Coletores da seção [Fontes de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores). Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, o Data Factory permite apenas a movimentação de dados de um banco de dados Cassandra para [repositórios de dados de coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores), mas não mover dados de outros repositórios de dados para um banco de dados Cassandra.

## Pré-requisitos
Para o serviço Azure Data Factory poder se conectar ao banco de dados Cassandra local, você deve instalar o seguinte:

- Gateway de Gerenciamento de Dados 2.0 ou superior no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar disputa por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.
  
	Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft Cassandra usado para se conectar ao banco de dados Cassandra.

> [AZURE.NOTE] Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copie dados de um banco de dados Cassandra para qualquer um dos repositórios de dados compatíveis é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do banco de dados Cassandra para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.


## Exemplo: Copiar dados do Cassandra no Blob
O exemplo copia dados de um banco de dados Cassandra em um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos. Os dados podem ser copiados diretamente para qualquer um dos coletores declarados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.

- Um serviço vinculado do tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [CassandraTable](#cassandratable-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [CassandraSource](#cassandrasource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Serviço vinculado Cassandra**

Este exemplo usa o serviço vinculado **Cassandra**. Confira a seção [Serviço vinculado Cassandra](#onpremisescassandra-linked-service-properties) para ver as propriedades compatíveis com esse serviço vinculado.

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
        	}
    	}
	}


**Serviço vinculado de armazenamento do Azure**

	{
		"name": "AzureStorageLinkedService",
		"properties": {
		"type": "AzureStorage",
			"typeProperties": {
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
			}
		}
	}

**Conjunto de dados de entrada do Cassandra**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1).

	{
		"name": "AzureBlobOutput",
		"properties":
		{
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties":
			{
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **CassandraSource** e o tipo **sink** está definido como **BlobSink**.

Confira [Propriedades do tipo RelationalSource](#cassandrasource-type-properties) para obter a lista de propriedades permitidas pelo RelationalSource.
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
					},
					"sink": {
						"type": "BlobSink"
					}
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
					"concurrency": 1,
					"executionPriorityOrder": "OldestFirst",
					"retry": 0,
					"timeout": "01:00:00"
				}
			}
			]	
		}
	}
## Propriedades do serviço vinculado OnPremisesCassandra

A tabela a seguir fornece a descrição de elementos JSON específicos para o serviço vinculado Cassandra.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade type deve ser definida como: **OnPremisesCassandra** | Sim |
| host | Um ou mais endereços IP ou nomes de host dos servidores Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar simultaneamente a todos os servidores. | Sim | 
| porta | A porta TCP usada pelo servidor Cassandra para ouvir conexões de cliente. | Não, valor padrão: 9042 |
| authenticationType | Básica, ou Anônima | Sim |
| Nome de Usuário |Especifique o nome de usuário da conta de usuário. | Sim, se authenticationType for definida como Básica. |
| Senha | Especifique a senha para a conta de usuário. | Sim, se authenticationType for definida como Básica. |
| gatewayName | O nome do gateway que será usado para se conectar ao servidor Cassandra local. | Sim |
| encryptedCredential | Credencial criptografada pelo gateway. | Não | 

## Propriedades de CassandraTable

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| keyspace | Nome do keyspace ou do esquema no banco de dados Cassandra. | Sim (se a **consulta** para **CassandraSource** não estiver definida). |
| tableName | Nome da tabela no banco de dados Cassandra. | Sim (se a **consulta** para **CassandraSource** não estiver definida). |


## Propriedades do tipo CassandraSource
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso de Atividade de Cópia, quando a fonte for do tipo **CassandraSource**, as seguintes propriedades estarão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | Consulta SQL-92 ou consulta CQL. Veja [Referência ao CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique **keyspace name.table name** para representar a tabela que deseja consultar. | Não (se tableName e keyspace no conjunto de dados estiverem definidos). |
| consistencyLevel | O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados ao aplicativo cliente. O Cassandra verifica o número especificado de réplicas de dados atender à solicitação de leitura. | ONE, TWO, THREE, QUORUM, ALL, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. Confira [Configuring data consistency (Configurando a consistência de dados)](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. | Não. O valor padrão é ONE. |  


### Mapeamento de tipo para Cassandra
Tipo Cassandra | Tipo baseado no .Net
--------------- | ---------------
ASCII |	Cadeia de caracteres 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Booliano
DECIMAL | Decimal
DOUBLE | Duplo
FLOAT | Single
INET | Cadeia de caracteres
INT | Int32
TEXT | Cadeia de caracteres
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | Cadeia de caracteres
VARINT | Decimal

> [AZURE.NOTE]  
Para tipos de coleção (mapa, conjunto, lista, etc...), veja a seção [Trabalhar com coleções usando tabela virtual](#work-with-collections-using-virtual-table).
> 
> Não há suporte para tipos definidos pelo usuário.
> 
> O comprimento da coluna Binário e os comprimentos da coluna Cadeia de Caracteres não pode ser maior que 4000.

## Trabalhar com coleções usando tabela virtual
O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados Cassandra e copiar dados dele. Para tipos de coleção, incluindo mapa, conjunto e lista, o driver normaliza novamente os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver colunas de coleção, o driver vai gerar as seguintes tabelas virtuais:

-	Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de coleção. A tabela base usa o mesmo nome da tabela real que ela representa.
-	Uma **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da tabela real, um separador "_vt_" e o nome da coluna.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Veja a seção Exemplo abaixo para obter detalhes. Você pode acessar o conteúdo das coleções de Cassandra consultando e unindo as tabelas virtuais.

Você pode aproveitar o [Assistente de Cópia](data-factory-data-movement-activities.md#data-factory-copy-wizard) para exibir intuitivamente a lista de tabelas no banco de dados Cassandra, incluindo as tabelas virtuais, e visualizar os dados internos. Também é possível construir uma consulta no Assistente de Cópia e validar para ver o resultado.

### Exemplo
Por exemplo, "ExampleTable" abaixo é uma tabela de banco de dados Cassandra que contém uma coluna de chave primária de inteiro chamada "pk\_int", uma coluna de texto chamado valor, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (chamada "StringSet").

pk\_int | Valor | Listar | Mapa |	StringSet
------ | ----- | ---- | --- | --------
1 | "valor de exemplo 1" | ["1", "2", "3"] | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valor de exemplo 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

O driver geraria várias tabelas virtuais para representar essa tabela única. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primário na tabela real e indicam à qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable", mostra abaixo. A tabela base contém os mesmos dados da tabela de banco de dados original, exceto para as coleções, que são omitidas da tabela e expandidas em outras tabelas virtuais.

pk\_int | Valor
------ | -----
1 | "valor de exemplo 1"
3 | "valor de exemplo 3"

As tabelas a seguir mostram as tabelas virtuais que normalizam novamente os dados nas colunas Lista, Mapa e StringSet. As colunas com nomes que terminam com "\_index" ou "\_key" indicam a posição dos dados na lista ou mapa original. As colunas com nomes que terminam com "\_value" contêm os dados expandidos da coleção.

#### Tabela "ExampleTable\_vt\_List":
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### Tabela "ExampleTable\_vt\_Map":
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | Uma
1 | S2 | b
3 | S1 | t

#### Tabela "ExampleTable\_vt\_StringSet":
pk\_int | StringSet\_value
------ | ---------------
1 | Uma
1 | B
1 | C
3 | Uma
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0817_2016-->