<properties 
   pageTitle="Como criar NSGs no modo ARM usando o portal do Azure | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no ARM usando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# Como gerenciar NSGs usando o portal do Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal. As etapas abaixo usam **RG-NSG** como o nome do grupo de recursos no qual o modelo foi implantado.

## Criar o NSG NSG-FrontEnd

Para criar o NSG **NSG-FrontEnd**, como mostra o cenário acima, siga as etapas abaixo.

1. Em um navegador, vá até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Procurar >** > **Grupos de Segurança de Rede**.

    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. Na folha **Grupos de segurança de rede**, clique em **Adicionar**.
  
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Na folha **Criar grupo de segurança de rede**, crie um NSG chamado *NSG-FrontEnd* no grupo de recursos *RG-NSG* e clique em **Criar**.

	![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## Criar regras em um NSG existente

Para criar regras em um NSG existente por meio do portal do Azure, siga as etapas abaixo.

2. Clique em **Procurar >** > **Grupos de segurança de rede**.

3. Na lista de NSGs, clique em **NSG-FrontEnd** > **Regras de segurança de entrada**

	![Portal do Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Na lista de **Regras de segurança de entrada**, clique em **Adicionar**.

	![Portal do Azure - Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Na folha **Adicionar regra de segurança de entrada**, crie uma regra chamada *web-rule* com prioridade de *200* permitindo o acesso via *TCP* à porta *80* a qualquer VM por meio de qualquer origem e, em seguida, clique em **OK**. Observe como a maioria destas configurações já é o valor padrão.

	![Portal do Azure - Configurações de regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Depois de alguns segundos, você encontrará a nova regra no NSG.

	![Portal do Azure - Nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Repita as etapas até 6 para criar uma regra de entrada chamada *rdp-rule* com uma prioridade de *250* permitindo o acesso via *TCP* à porta *3389* a qualquer VM de qualquer origem.

## Associar o NSG à sub-rede FrontEnd

1. Clique em **Procurar >** > **Grupos de recursos** > **RG-NSG**.
2. Na folha **RG-NSG**, clique em **...** > **TestVNet**.

	![Portal do Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. Na folha **Configurações**, clique em **Sub-redes** > **FrontEnd** > **Grupo de segurança de rede** > **NSG-FrontEnd**.

	![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Na folha **FrontEnd**, clique em **Salvar**.

	![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## Criar o NSG NSG-BackEnd

Para criar o NSG **NSG-BackEnd** e associá-lo à sub-rede **BackEnd**, siga as etapas abaixo.

1. Repita as etapas descritas em [Criar o NSG NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG) para criar um NSG chamado *NSG-BackEnd*
2. Repita as etapas descritas em [Criar regras em um NSG existente](#Create-rules-in-an-existing-NSG) para criar as regras de **entrada** na tabela abaixo.

	|Regra de entrada|Regra de saída|
	|---|---|
	|![Portal do Azure - regra de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portal do Azure - regra de saída](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Repita as etapas descritas em [Associar o NSG à sub-rede FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) para associar o NSG **NSG-Backend** à sub-rede **BackEnd**.

## Próximas etapas

- Saiba como [gerenciar NSGs existentes](virtual-network-manage-nsg-arm-portal.md)
- [Habilite o registro em log](virtual-network-nsg-manage-log.md) para NSGs.

<!---HONumber=AcomDC_0810_2016-->