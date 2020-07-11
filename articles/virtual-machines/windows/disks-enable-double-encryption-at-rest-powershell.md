---
title: Azure PowerShell abilitare la crittografia doppia nei dischi gestiti di Rest
description: Abilitare la crittografia doppia per i dati dei dischi gestiti usando Azure PowerShell.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0f386e4ba4a1835b88b753574bde23e93f7f8d17
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235896"
---
# <a name="azure-powershell---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure PowerShell abilitare la crittografia doppia sui dischi gestiti

Archiviazione su disco di Azure supporta la crittografia doppia per i dischi gestiti. Per informazioni di carattere generale sulla crittografia dei dati inattivi, oltre ad altri tipi di crittografia del disco gestito, vedere la sezione relativa alla crittografia [a doppio](disk-encryption.md#double-encryption-at-rest) inattivo dell'articolo crittografia del disco.

## <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Prerequisiti

Installare la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps)e accedere a un account Azure usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-4.3.0).

## <a name="getting-started"></a>Introduzione

1. Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare l'eliminazione temporanea e la protezione dall'eliminazione. L'eliminazione temporanea assicura che l'insieme di credenziali delle chiavi mantenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorni). La protezione dall'eliminazione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un insieme di credenziali delle chiavi per la crittografia dei dischi gestiti.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Creare un DiskEncryptionSet con encryptionType impostato come EncryptionAtRestWithPlatformAndCustomerKeys. Usare l'API versione **2020-05-01** nel modello di Azure Resource Manager (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName "yourDESForDoubleEncryption" `
    -keyVaultId "subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.KeyVault/vaults/yourKeyVaultName" `
    -keyVaultKeyUrl "https://yourKeyVaultName.vault.azure.net/keys/yourKeyName/403445136dee4a57af7068cab08f7d42" `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region "CentralUSEUAP"
    ```

1. Concedere al set di crittografia dischi l'accesso all'insieme di credenziali delle chiavi.

    > [!NOTE]
    > Potrebbero essere necessari alcuni minuti prima che Azure crei l'identità del set di crittografia dischi in Azure Active Directory. Se quando si esegue il comando seguente viene visualizzato un errore simile a "Impossibile trovare l'oggetto Active Directory", attendere qualche minuto e riprovare.

    ```powershell  
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state create e configurate queste risorse, è possibile usarle per proteggere i dischi gestiti. I collegamenti seguenti contengono script di esempio, ognuno con un rispettivo scenario, che è possibile usare per proteggere i dischi gestiti.

[Azure PowerShell-abilitare le chiavi gestite dal cliente con la crittografia lato server-Managed disks](disks-enable-customer-managed-keys-powershell.md) 
 [Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)