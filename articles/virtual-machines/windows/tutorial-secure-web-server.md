
---
title: Proteggere IIS con i certificati SSL in Azure | Microsoft Docs
description: Informazioni su come proteggere il server Web IIS con i certificati SSL in una VM Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Proteggere il server Web IIS con i certificati SSL in una macchina virtuale Windows in Azure
Per proteggere i server Web, si può usare un certificato Secure Sockets Layer (SSL) per crittografare il traffico Web. Questi certificati SSL possono essere archiviati in Azure Key Vault e consentono distribuzioni sicure dei certificati nelle macchine virtuali (VM) Windows in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web IIS
> * Inserire il certificato nella macchina virtuale e configurare IIS con un'associazione SSL

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="overview"></a>Panoramica
Azure Key Vault consente di proteggere chiavi di crittografia, chiavi private, certificati e password. Key Vault semplifica il processo di gestione dei certificati e consente di mantenere il controllo delle chiavi che accedono a tali certificati. È possibile creare un certificato autofirmato in Key Vault o caricarne uno esistente, un certificato attendibile di cui si è già proprietari.

Invece di usare un'immagine di macchina virtuale personalizzata che include certificati incorporati, si inseriscono i certificati in una macchina virtuale in esecuzione. Questo processo assicura che, durante la distribuzione, in un server Web vengano installati i certificati più aggiornati. Se si rinnova o sostituisce un certificato, non è necessario creare anche una nuova immagine di macchina virtuale personalizzata. I certificati più recenti vengono automaticamente inseriti quando si creano macchine virtuali aggiuntive. Durante l'intero processo, i certificati non lasciano mai la piattaforma Azure e non vengono mai esposti in uno script, in una cronologia della riga di comando o in un modello.


## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault
Per poter creare un insieme di credenziali delle chiavi e i certificati, è prima necessario creare un gruppo di risorse con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupSecureWeb* nella località *East US*:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Creare quindi un insieme di credenziali delle chiavi con [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Ogni Key Vault deve avere un nome univoco in lettere minuscole. Nell'esempio seguente, sostituire `<mykeyvault>` con il nome univoco del proprio Key Vault:

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generare un certificato e archiviarlo in Key Vault
Per la produzione è necessario importare un certificato valido firmato da un provider attendibile con il comando [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Per questa esercitazione, l'esempio seguente illustra come sia possibile generare un certificato autofirmato con il comando [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) che usi i criteri dei certificati predefiniti da [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Impostare nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

A questo punto è possibile creare la macchina virtuale con il comando [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Nell'esempio seguente vengono creati i componenti della rete virtuale necessari e viene eseguita la configurazione del sistema operativo. Viene quindi creata una macchina virtuale denominata *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Per creare la macchina virtuale sono necessari alcuni minuti. L'ultimo passaggio usa l'estensione Script personalizzato di Azure per installare il server Web IIS con [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Aggiungere un certificato alla macchina virtuale da Key Vault
Per aggiungere un certificato da Key Vault a una macchina virtuale, ottenere l'ID del certificato con [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Aggiungere il certificato alla macchina virtuale con [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurare IIS per usare il certificato
Usare di nuovo l'estensione Script personalizzato con [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per aggiornare la configurazione di IIS. Questo aggiornamento applica il certificato inserito da Key Vault a IIS e configura l'associazione Web:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testare l'applicazione Web protetta
Ottenere l'indirizzo IP pubblico della macchina virtuale con il comando [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP `myPublicIP` creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

È ora possibile aprire un Web browser e immettere `https://<myPublicIP>` bella barra degli indirizzi. Per accettare l'avviso di sicurezza se si è usato un certificato autofirmato, selezionare **Dettagli** e quindi **Continua per la pagina Web**:

![Accettare l'avviso di sicurezza del Web browser](./media/tutorial-secure-web-server/browser-warning.png)

Il sito Web IIS protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Visualizzare il sito protetto IIS in esecuzione](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è protetto un server Web IIS con un certificato SSL archiviato in Azure Key Vault. Si è appreso come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web IIS
> * Inserire il certificato nella macchina virtuale e configurare IIS con un'associazione SSL

Seguire questo collegamento per vedere esempi di script predefiniti delle macchine virtuali.

> [!div class="nextstepaction"]
> [Esempi di script delle macchine virtuali Windows](./powershell-samples.md)
