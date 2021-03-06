Essa condição avaliará o campo de endereço de email de cada novo cliente potencial do Salesforce. Se o endereço de email contiver *amazon.com*, o resultado da condição será *True*.

1. Selecione **+ Nova Etapa**.  
![Imagem de condição do Salesforce 1](./media/connectors-create-api-salesforce/condition-1.png)  
- Selecione **Adicionar uma condição**.  
![Imagem de condição do Salesforce 2](./media/connectors-create-api-salesforce/condition-2.png)  
- Selecione **Escolher um valor**.  
![Imagem de condição do Salesforce 3](./media/connectors-create-api-salesforce/condition-3.png)  
- Selecione o token *Email* do cliente potencial do gatilho.  
![Imagem de condição do Salesforce 4](./media/connectors-create-api-salesforce/condition-4.png)  
- Selecione *Contém*.  
![Imagem de condição do Salesforce 5](./media/connectors-create-api-salesforce/condition-5.png)  
- Selecione **Escolher um valor** na parte inferior do controle.  
![Imagem de condição do Salesforce 6](./media/connectors-create-api-salesforce/condition-6.png)  
- Digite *amazon.com* como o valor para o qual você gostaria de avaliar o endereço de email do novo cliente potencial. Se o endereço de email contiver *amazon.com*, a condição será avaliada como *True* e as outras etapas no seu aplicativo lógico poderão continuar.  
![Imagem de condição do Salesforce 7](./media/connectors-create-api-salesforce/condition-7.png)  
- Salve seus aplicativos lógicos.

<!---HONumber=AcomDC_0727_2016-->
