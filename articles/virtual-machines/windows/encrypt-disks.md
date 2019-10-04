---
title: Crittografare i dischi di una macchina virtuale Windows in Azure | Microsoft Docs
description: Crittografare i dischi virtuali in una macchina virtuale Windows per una maggiore sicurezza usando Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: bee4b108d2747e8c386abe41dc61b5b19aecf4f5
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828694"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Crittografare i dischi virtuali in una macchina virtuale Windows
Per migliorare gli aspetti di sicurezza e conformità delle macchine virtuali (VM), i dischi virtuali in Azure possono essere crittografati. I dischi vengono crittografati usando chiavi di crittografia protette in un'istanza di Azure Key Vault. È possibile controllare queste chiavi di crittografia e il loro uso. Questo articolo descrive come crittografare i dischi virtuali in una macchina virtuale Windows usando Azure PowerShell. È anche possibile [crittografare una macchina virtuale Linux usando l'interfaccia della riga di comando di Azure](../linux/disk-encryption-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Panoramica della crittografia dei dischi
I dischi virtuali delle macchine virtuali Windows vengono crittografati quando sono inattivi usando BitLocker. Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate in un'istanza di Azure Key Vault usando la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. Le chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla macchina virtuale. È possibile mantenere il controllo su queste chiavi di crittografia e su come vengono usate. 

Il processo per la crittografia di una VM si articola nel modo seguente:

1. Creare una chiave di crittografia in un insieme di credenziali delle chiavi di Azure.
1. Configurare la chiave di crittografia in modo da renderla utilizzabile per la crittografia dei dischi.
1. Abilitare la crittografia del disco per i dischi virtuali.
1. Le chiavi di crittografia necessarie sono richieste da Azure Key Vault.
1. I dischi virtuali vengono crittografati tramite la chiave di crittografia fornita.


## <a name="requirements-and-limitations"></a>Requisiti e limitazioni

Requisiti relativi alla crittografia dei dischi e scenari supportati:

* Abilitazione della crittografia in nuove macchine virtuali Windows da immagini di Azure Marketplace o da immagini del disco rigido virtuale personalizzate.
* Abilitazione della crittografia nelle macchine virtuali esistenti di Windows in Azure.
* Abilitazione della crittografia nelle macchine virtuali Windows configurate usando spazi di archiviazione.
* Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM di Windows.
* Macchine virtuali di livello standard, ad esempio macchine virtuali di serie A, D, DS, G e GS.

    > [!NOTE]
    > Tutte le risorse, inclusi Key Vault, l'account di archiviazione e la macchina virtuale, devono risiedere nella stessa area e nella stessa sottoscrizione di Azure.

La crittografia del disco non è attualmente supportata negli scenari seguenti:

* VM di base.
* Macchine virtuali create con il modello di distribuzione classica.
* Aggiornare le chiavi di crittografia in una VM già crittografata.
* Integrazione con il servizio di gestione delle chiavi locale.


## <a name="create-an-azure-key-vault-and-keys"></a>Creare le chiavi e un Azure Key Vault
Prima di iniziare, verificare che sia installata la versione più recente del modulo Azure PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Negli esempi di comando seguenti sostituire tutti i parametri di esempio con i propri nomi, località e valori chiave, ad esempio *myResourceGroup*, *myKeyVault*, *myVM* e così via.

Il primo passaggio consiste nel creare un insieme di credenziali delle chiavi di Azure in cui archiviare le chiavi di crittografia. Le istanze di Azure Key Vault consentono di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Per la crittografia del disco virtuale, creare un'istanza di Key Vault per archiviare una chiave di crittografia usata per crittografare o decrittografare i dischi virtuali. 

Abilitare il provider di Azure Key Vault nella sottoscrizione di Azure con [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider) e creare un gruppo di risorse con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un nome del gruppo di risorse *myResourceGroup* nella posizione *Stati Uniti Orientali*:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

L'istanza di Azure Key Vault contenente le chiavi di crittografia e le risorse di calcolo associate, come lo spazio di archiviazione e la macchina virtuale stessa, devono essere nella stessa area. Creare un Azure Key Vault con [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) e abilitare il Key Vault per l'uso con la crittografia del disco. Specificare un nome univoco di Key Vault per *keyVaultName* come indicato di seguito:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

È possibile archiviare le chiavi di crittografia usando la protezione software o del modulo di protezione hardware.  Un'istanza di Key Vault standard archivia solo chiavi protette tramite software. L'uso di un modulo di protezione hardware richiede un'istanza di Key Vault Premium a un costo aggiuntivo. Per creare un'istanza di Key Vault premium, aggiungere il parametro *-Sku "Premium"* nel passaggio precedente. L'esempio seguente usa chiavi protette tramite software, poiché viene creato un insieme di credenziali delle chiavi standard. 

Per entrambi i modelli di protezione, è necessario consentire alla piattaforma Azure di accedere all'avvio della VM per richiedere le chiavi di crittografia con cui decrittografare i dischi virtuali. Creare una chiave crittografica in Key Vault con [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). Nell'esempio seguente viene creata una chiave denominata *myKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Per testare il processo di crittografia, creare una VM con [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L'esempio seguente crea una VM denominata *myVM* usando un'immagine di *Windows Server 2016 Datacenter*. Quando vengono richieste le credenziali, immettere un nome utente e una password da usare per la VM:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Crittografare una macchina virtuale
Crittografare la macchina virtuale con [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) usando la chiave di Azure Key Vault. Nell'esempio seguente vengono recuperate tutte le principali informazioni e quindi viene crittografata la macchina virtuale denominata *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Accettare la richiesta di continuare la crittografia della macchina virtuale. Durante questo processo, la VM viene riavviata. Dopo aver completato il processo di crittografia e aver riavviato la macchina virtuale, controllare lo stato di crittografia con [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

L'output è simile all'esempio seguente:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione di un'istanza di Azure Key Vault, vedere [Configurare un'istanza di Key Vault per le macchine virtuali](key-vault-setup.md).
* Per altre informazioni sulla crittografia del disco, tra cui come preparare una VM personalizzata con crittografia da caricare in Azure, vedere [Crittografia dischi di Azure](../../security/azure-security-disk-encryption.md).
