---
title: Crittografare i dischi di una macchina virtuale Windows in Azure | Microsoft Docs
description: Come crittografare i dischi virtuali in una VM di Windows per una maggiore sicurezza tramite Azure PowerShell
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/24/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: fe53a68e2858df87c4b93348dfc426f9cd346121
ms.lasthandoff: 04/26/2017


---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Come crittografare i dischi virtuali in una VM di Windows
Per migliorare gli aspetti di sicurezza e conformità delle macchine virtuali (VM), i dischi virtuali in Azure possono essere crittografati. I dischi vengono crittografati usando chiavi di crittografia protette in un insieme di credenziali delle chiavi di Azure. È possibile controllare queste chiavi di crittografia e il loro uso. Questo articolo descrive come crittografare i dischi virtuali in una VM di Windows tramite Azure PowerShell. È inoltre possibile [crittografare una VM di Linux usando l'interfaccia della riga di comando di Azure 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Panoramica della crittografia dei dischi
I dischi virtuali delle VM di Windows vengono crittografati a riposo mediante Bitlocker. Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. Un'entità servizio di Azure Active Directory offre un meccanismo protetto per il rilascio delle chiavi di crittografia all'accensione e allo spegnimento delle VM.

Il processo per la crittografia di una VM si articola nel modo seguente:

1. Creare una chiave di crittografia in un insieme di credenziali delle chiavi di Azure.
2. Configurare la chiave di crittografia in modo da renderla utilizzabile per la crittografia dei dischi.
3. Per leggere la chiave di crittografia dall'insieme di credenziali delle chiavi di Azure, creare un'entità servizio di Azure Active Directory con le autorizzazioni appropriate.
4. Eseguire il comando per crittografare i dischi virtuali, specificando l'entità servizio di Azure Active Directory e la chiave di crittografia appropriata da usare.
5. L'entità servizio di Azure Active Directory richiede la chiave di crittografia necessaria dall'insieme di credenziali delle chiavi di Azure.
6. I dischi virtuali vengono crittografati tramite la chiave di crittografia fornita.

## <a name="encryption-process"></a>Processo di crittografia
La crittografia del disco si basa sui componenti aggiuntivi seguenti:

* **Insieme di credenziali delle chiavi di Azure**, che consente di proteggere le chiavi crittografiche e private usate per il processo di crittografia/decrittografia dei dischi. 
  * Se disponibile, è possibile usare un insieme di credenziali delle chiavi di Azure esistente. Non è necessario dedicare un insieme di credenziali delle chiavi alla crittografia dei dischi.
  * Per separare i limiti amministrativi e la visibilità delle chiavi, è possibile creare un insieme di credenziali delle chiavi dedicato.
* **Azure Active Directory** gestisce lo scambio protetto delle chiavi di crittografia necessarie e dell'autenticazione per le azioni richieste. 
  * In genere, è possibile inserire l'applicazione in un'istanza esistente di Azure Active Directory.
  * L'entità servizio offre un meccanismo protetto per richiedere e consentire il rilascio delle chiavi di crittografia appropriate. Non si sta procedendo a sviluppare un'applicazione reale integrata con Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
Requisiti relativi alla crittografia dei dischi e scenari supportati:

* Abilitazione della crittografia in nuove macchine virtuali di Windows da immagini di Azure Marketplace o da un'immagine del disco rigido virtuale personalizzata.
* Abilitazione della crittografia nelle macchine virtuali esistenti di Windows in Azure.
* Abilitazione della crittografia nelle macchine virtuali di Windows configurate usando spazi di archiviazione.
* Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM di Windows.
* Tutte le risorse, come l'insieme di credenziali delle chiavi, l'account di archiviazione e la VM, devono risiedere nella stessa area e nella stessa sottoscrizione di Azure.
* Macchine virtuali di livello standard, ad esempio macchine virtuali di serie A, D, DS, G e GS.

La crittografia del disco non è attualmente supportata negli scenari seguenti:

* VM di base.
* VM create con il modello di distribuzione classica.
* Aggiornare le chiavi di crittografia in una VM già crittografata.
* Integrazione con il Servizio di gestione delle chiavi locale.

## <a name="create-azure-key-vault-and-keys"></a>Creare le chiavi e l'insieme di credenziali delle chiavi di Azure
Prima di iniziare verificare che sia installata la versione più recente del modulo Azure PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs). Negli esempi di comandi sostituire tutti i parametri di esempio con i propri valori di nome, percorso e chiave. Negli esempi viene usata una convenzione di `myResourceGroup`, `myKeyVault`, `myVM` e così via.

Il primo passaggio consiste nel creare un insieme di credenziali delle chiavi di Azure in cui archiviare le chiavi di crittografia. L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Per la crittografia del disco virtuale, creare il Key Vault per archiviare una chiave di crittografia usata per crittografare o decrittografare i dischi virtuali. 

Abilitare il provider dell'Azure Key Vault nella sottoscrizione di Azure e creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `West US`:

```powershell
$rgName = "myResourceGroup"
$location = "West US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

L'insieme di credenziali delle chiavi di Azure contenente le chiavi di crittografia e le risorse di calcolo associate, come l'archiviazione e la VM stessa, devono risiedere nella stessa area. Creare un Azure Key Vault e abilitare il Key Vault per l'uso con crittografia del disco. Specificare un nome univoco per l'insieme di credenziali delle chiavi per `keyVaultName` come indicato di seguito:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location -ResourceGroupName $rgName -VaultName $keyVaultName -EnabledForDiskEncryption
```

È possibile archiviare le chiavi di crittografia usando il software o il modulo di protezione hardware. L'uso di un modulo di protezione hardware richiede un insieme di credenziali delle chiavi premium. Esiste un costo aggiuntivo per creare un insieme di credenziali delle chiavi premium, anziché standard, in cui archiviare le chiavi protette tramite software. Per creare un Key Vault premium, aggiungere i parametri `-Sku "Premium"` nel passaggio precedente. L'esempio seguente usa chiavi protette tramite software, poiché viene creato un insieme di credenziali delle chiavi standard. 

Per entrambi i modelli di protezione, è necessario consentire alla piattaforma Azure di accedere all'avvio della VM per richiedere le chiavi di crittografia con cui decrittografare i dischi virtuali. Creare una chiave di crittografia nel Key Vault. Nell'esempio seguente viene creata una chiave denominata `myKey`:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName -Name "myKey" -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Creare un'entità servizio di Azure Active Directory
Al momento di crittografare o decrittografare i dischi virtuali, specificare un account per gestisce l'autenticazione e lo scambio delle chiavi di crittografia dall'insieme di credenziali delle chiavi. Tale account, un'entità servizio di Azure Active Directory, consente alla piattaforma Azure di richiedere le chiavi di crittografia appropriate per conto della VM. Un'istanza di Azure Active Directory predefinita è già disponibile nella sottoscrizione, anche se molte organizzazioni hanno directory di Azure Active Directory dedicate.

Creare un'entità servizio in Azure Active Directory. Per specificare una password sicura seguire [Restrizioni e criteri password in Azure Active Directory](../../active-directory/active-directory-passwords-policy.md):

```powershell
$appName = "My App"
$securePassword = "P@ssword!"
$app = New-AzureRmADApplication -DisplayName $appName -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Per crittografare o decrittografare correttamente i dischi virtuali, le autorizzazioni per la chiave di crittografia archiviata nell'insieme di credenziali delle chiavi devono essere impostate in modo tale da consentire all'entità servizio di Azure Active Directory di leggere le chiavi. Impostare le autorizzazioni per il Key Vault:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "all" -PermissionsToSecrets "all"
```


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per testare il processo di crittografia è necessario creare una macchina virtuale. Nell'esempio seguente viene creata una VM denominata `myVM` usando un'immagine di **Windows Server 2016 Datacenter**:

```powershell
subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Location $location `
    -Name myVnet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rgName -Location $location `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"

$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $rgName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$cred = Get-Credential

$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```


## <a name="encrypt-virtual-machine"></a>Crittografare una macchina virtuale
Per crittografare i dischi virtuali, è necessario riunire tutti i componenti precedenti:

1. Specificare un'entità servizio e la password di Azure Active Directory.
2. Specificare l'insieme di credenziali delle chiavi in cui memorizzare i metadati dei dischi crittografati.
3. Specificare le chiavi di crittografia da usare per la crittografia e la decrittografia effettive.
4. Specificare se si desidera crittografare il disco del sistema operativo, i dischi dati o tutti i dischi.

Crittografare la macchina virtuale tramite la chiave di Azure Key Vault e le credenziali dell'entità servizio di Azure Active Directory. Nell'esempio seguente vengono recuperate tutte le principali informazioni e quindi viene crittografata la macchina virtuale denominata `myVM`:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName -VMName $vmName -AadClientID $app.ApplicationId `
    -AadClientSecret $securePassword -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Accettare la richiesta di continuare la crittografia della macchina virtuale. Durante questo processo, la VM viene riavviata. Dopo aver completato il processo di crittografia e aver riavviato la macchina virtuale, controllare lo stato di crittografia:

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName $vmName
```

L'output è simile all'esempio seguente:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione di Azure Key Vault, vedere [Configurare il Key Vault per le macchine virtuali](key-vault-setup.md).
* Per altre informazioni sulla crittografia del disco, tra cui come preparare una VM personalizzata con crittografia da caricare in Azure, vedere [Crittografia dischi di Azure](../../security/azure-security-disk-encryption.md).
