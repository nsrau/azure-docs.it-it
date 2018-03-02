---
title: "Crittografia dischi di Azure per set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Informazioni su come crittografare i dischi collegati in un set di scalabilità di macchine virtuali."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Crittografare il disco del sistema operativo e i dischi dati collegati in un set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) di Azure supportano Crittografia dischi di Azure.  Questa funzionalità può essere abilitata per i set di scalabilità di macchine virtuali Windows e Linux per proteggere i dati inattivi dei set di scalabilità tramite la tecnologia di crittografia standard di settore. Per altre informazioni, leggere l'articolo Crittografia dischi di Azure per macchine virtuali Windows e Linux.

> [!NOTE]
>  Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è attualmente disponibile come anteprima pubblica in tutte le aree pubbliche di Azure.

Crittografia dischi di Azure è supportata per le risorse seguenti:
- Set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
- Volumi di dati e sistema operativo nei set di scalabilità Windows. La disabilitazione della crittografia è supportata per volumi di dati e sistema operativo nei set di scalabilità Windows.
- Volumi di dati nei set di scalabilità Linux. Nell'anteprima corrente la crittografia del disco del sistema operativo NON è supportata per i set di scalabilità Linux.

Le operazioni di creazione di una nuova immagine e di aggiornamento delle macchine virtuali dei set di scalabilità non sono supportate nell'anteprima corrente. È consigliabile usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali solo in ambienti di test. Usando l'anteprima, non abilitare la crittografia dei dischi in ambienti di produzione in cui può essere necessario aggiornare un'immagine del sistema operativo in un set di scalabilità crittografato.

## <a name="prerequisites"></a>Prerequisiti
Installare le versioni più recenti di [Azure Powershell](https://github.com/Azure/azure-powershell/releases), che contiene i comandi di crittografia.

Per usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è necessario eseguire la registrazione automatica della sottoscrizione tramite i comandi di PowerShell seguenti: 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Attendere circa 10 minuti fino a quando non viene restituito lo stato "Registrato" dal comando seguente: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creare un insieme di credenziali delle chiavi di Azure abilitato per la crittografia dei dischi
Creare un nuovo insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità e impostare il criterio di accesso "EnabledForDiskEncryption".

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

In alternativa, abilitare un insieme di credenziali delle chiavi esistente nella stessa sottoscrizione e nella stessa area del set di scalabilità per la crittografia dei dischi.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Abilitare la crittografia
I comandi seguenti eseguono la crittografia di un disco dati in un set di scalabilità in esecuzione tramite un insieme di credenziali delle chiavi nello stesso gruppo di risorse. Per eseguire la crittografia dei dischi in un [set di scalabilità Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o un [set di scalabilità Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) in esecuzione, è possibile anche usare modelli.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Verificare lo stato della crittografia
Usare i comandi seguenti per visualizzare lo stato della crittografia del set di scalabilità.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Disabilitare la crittografia
Disabilitare la crittografia in un set di scalabilità di macchine virtuali in esecuzione usando i comandi seguenti. Per disabilitare la crittografia in un [set di scalabilità Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) o un [set di scalabilità Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) in esecuzione è possibile anche usare modelli.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
