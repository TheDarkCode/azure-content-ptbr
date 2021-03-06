<properties
   pageTitle="Benefício do Uso Híbrido do Azure para Window Server | Microsoft Azure"
   description="Saiba como maximizar os benefícios do Software Assurance do Windows Server para colocar as licenças locais no Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# Benefício do uso híbrido do Azure para Windows Server

Para clientes que usam o Windows Server com o Software Assurance, você pode colocar suas licenças do Windows Server locais no Azure e executar VMs do Windows Server no Azure a um custo reduzido. O Benefício do uso híbrido do Azure permite executar VMs do Windows Server no Azure e ser cobrado somente pela a taxa de computação base. Para obter mais informações, consulte a página [Licenciamento de Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Este artigo explica como implantar VMs do Windows Server no Azure para usar este benefício de licenciamento.

> [AZURE.NOTE] Você não pode usar imagens do Azure Marketplace para implantar VMs do Windows Server utilizando o Benefício do uso híbrido do Azure. Você deve implantar suas VMs usando o PowerShell ou modelos do Resource Manager para registrar corretamente suas VMs como qualificadas para o desconto de taxa de computação base.

## Pré-requisitos
Há alguns pré-requisitos para utilizar a Vantagem do uso híbrido do Azure para VMs do Windows Server no Azure:

- Ter o módulo do Azure PowerShell instalado
- Carregar o VHD do Windows Server no Armazenamento do Azure

### Instale o Azure PowerShell
Verifique se você [instalou e configurou o Azure PowerShell mais recente](../powershell-install-configure.md). Mesmo se você for implantar suas VMs usando modelos do Resource Manager, ainda precisará ter o Azure PowerShell instalado para carregar o VHD do Windows Server (consulte a etapa a seguir).

### Carregar um VHD do Windows Server

Para implantar uma VM do Windows Server no Azure, primeiro você precisa criar um VHD que contém o build base do Windows Server. Esse VHD deve estar preparado adequadamente por meio do Sysprep antes de carregá-lo no Azure. Você pode [ler mais sobre os requisitos de VHD e o processo Sysprep](./virtual-machines-windows-upload-image.md) e [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup da VM antes de executar o Sysprep. Depois de preparar o VHD, carregue-o em sua conta de Armazenamento do Azure usando o cmdlet `Add-AzureRmVhd` da seguinte maneira:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] O Microsoft SQL Server, SharePoint Server e Dynamics também podem utilizar o licenciamento Software Assurance. Você ainda precisa preparar a imagem do Windows Server instalando os componentes do aplicativo e fornecendo as chaves de licença corretamente e, então, carregando a imagem do disco no Azure. Examine a documentação apropriada para executar o Sysprep com seu aplicativo, como [Considerações para Instalar o SQL Server usando o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Criar uma Imagem de Referência do SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Você também pode ler mais sobre como [carregar o VHD no processo do Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Este artigo se concentra na implantação de VMs do Windows Server. Você também pode implantar VMs cliente do Windows da mesma maneira. Nos exemplos a seguir, você deve substituir `Server` por `Client` adequadamente.

## Implantar uma VM por meio do Início Rápido do PowerShell
Ao implantar a VM do Windows Server por meio do PowerShell, você tem um parâmetro adicional para `-LicenseType`. Quando o VHD estiver carregado no Azure, você criará uma nova VM usando `New-AzureRmVM` e especificará o tipo de licenciamento, da seguinte forma:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

[Leia um passo a passo mais detalhado sobre como implantar uma VM no Azure por meio do PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) abaixo ou leia um guia mais descritivo sobre as diferentes etapas para [criar uma VM do Windows usando o Resource Manager e o PowerShell](./virtual-machines-windows-ps-create.md).

## Implantar uma VM por meio do Resource Manager
Nos modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Quando o VHD for carregado no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## Verifique se que sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do método de implantação do PowerShell ou do Resource Manager, verifique o tipo de licença com `Get-AzureRmVM` da seguinte maneira:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

A saída deverá ser semelhante a esta:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Isso contrasta com a seguinte VM implantada sem licenciamento do Benefício do uso híbrido do Azure, como uma VM implantada diretamente da Galeria do Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## Passo a passo detalhado do PowerShell

As seguintes etapas detalhadas do PowerShell mostram uma implantação completa de uma VM. Você pode ler mais sobre o contexto dos cmdlets reais e sobre a criação de diferentes componentes em [Criar uma VM do Windows usando o Resource Manager e o PowerShell](./virtual-machines-windows-ps-create.md). Você passa pela criação de seu grupo de recursos, da conta de armazenamento e da rede virtual e, por fim, define e crie sua VM.
 
Primeiro, obtenha credenciais com segurança, defina um local e o nome do grupo de recursos:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Criar um IP público:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Defina sua sub-rede, NIC e VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nomeie sua VM e crie uma configuração da VM:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina seu sistema operacional:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Adicione a NIC à VM:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina a conta de armazenamento a ser usada:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Carregue o VHD, preparado adequadamente, e anexe à sua VM para uso:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Finalmente, crie sua VM e defina o tipo de licenciamento para utilizar o Benefício do uso híbrido do Azure:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## Próximas etapas

Leia mais sobre o [Licenciamento do Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Saiba mais sobre como [usar os modelos do Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0831_2016-->