---
title: Abilitare la crittografia doppia inattiva-dischi gestiti dell'interfaccia della riga di comando di Azure
description: Abilitare la crittografia doppia per i dati dei dischi gestiti usando l'interfaccia della riga di comando di Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 55a49c5f8d066cd6893c46ef9942462bb7911307
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360616"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Usare l'interfaccia della riga di comando di Azure per abilitare la crittografia doppia per i dischi gestiti

Archiviazione su disco di Azure supporta la crittografia doppia per i dischi gestiti. Per informazioni di carattere generale sulla crittografia dei dati inattivi, oltre ad altri tipi di crittografia del disco gestito, vedere la sezione relativa alla crittografia [a doppio](../disk-encryption.md#double-encryption-at-rest) inattivo dell'articolo crittografia del disco.

## <a name="prerequisites"></a>Prerequisiti

Installare la versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-az-cli2) e accedere a un account Azure con [AZ login](/cli/azure/reference-index).

## <a name="getting-started"></a>Introduzione

1. Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare l'eliminazione temporanea e la protezione dall'eliminazione. L'eliminazione temporanea assicura che l'insieme di credenziali delle chiavi mantenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorni). La protezione dall'eliminazione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un insieme di credenziali delle chiavi per la crittografia dei dischi gestiti.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Creare un DiskEncryptionSet con encryptionType impostato come EncryptionAtRestWithPlatformAndCustomerKeys. Usare l'API versione **2020-05-01** nel modello di Azure Resource Manager (ARM). 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Concedere al set di crittografia dischi l'accesso all'insieme di credenziali delle chiavi. 

        > [!NOTE]
        > Potrebbero essere necessari alcuni minuti prima che Azure crei l'identità del set di crittografia dischi in Azure Active Directory. Se quando si esegue il comando seguente viene visualizzato un errore simile a "Impossibile trovare l'oggetto Active Directory", attendere qualche minuto e riprovare.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state create e configurate queste risorse, è possibile usarle per proteggere i dischi gestiti. I collegamenti seguenti contengono script di esempio, ognuno con un rispettivo scenario, che è possibile usare per proteggere i dischi gestiti.

- [Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Abilitare le chiavi gestite dal cliente con la crittografia lato server-esempi](disks-enable-customer-managed-keys-cli.md#examples)
