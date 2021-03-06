## Capacidade de repetição durante a cópia

Ao copiar dados de e para repositórios relacionais, você precisa ter em mente a capacidade de repetição para evitar resultados não intencionais.

**Observação:** uma fatia pode ser reexecutada automaticamente no Azure Data Factory, de acordo com a política de repetição especificada. É recomendável definir uma política de repetição para se proteger contra falhas transitórias. Portanto, a capacidade de repetição é um aspecto importante a ser observado durante a movimentação de dados.

**Como uma fonte:**
> [AZURE.NOTE] Os exemplos a seguir são para o Azure SQL, mas são aplicáveis a qualquer repositório de dados com suporte a conjuntos de dados retangulares. Talvez seja necessário ajustar o **tipo** de origem e a propriedade **query** (por exemplo: query em vez de sqlReaderQuery) para o repositório de dados.   

Na maioria dos casos, ao ler repositórios relacionais, convém ler apenas os dados correspondentes a essa fatia. Uma maneira de fazer isso é usando as variáveis WindowStart e WindowEnd disponíveis no Azure Data Factory. Leia sobre as variáveis e funções no Azure Data Factory aqui, no artigo [Planejamento e execução](../articles/data-factory/data-factory-scheduling-and-execution.md). Exemplo:
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

A consulta anterior lê dados de 'MyTable' dentro do intervalo de duração da fatia. A reexecução dessa fatia também garante esse comportamento todas as vezes.

Em outros casos, talvez seja conveniente ler a tabela inteira (por exemplo, para mover apenas uma vez) e definir sqlReaderQuery da seguinte maneira:

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!-----HONumber=AcomDC_0224_2016-->