---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2c1f61dbb060f9df0b88f301008e3fd45a91fe8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609386"
---
1. Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di avere eseguito la registrazione a un account di Azure con [az login](/cli/azure/reference-index).

1. Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare l'eliminazione temporanea e la protezione dall'eliminazione. L'eliminazione temporanea assicura che l'insieme di credenziali delle chiavi mantenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorni). La protezione dall'eliminazione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un insieme di credenziali delle chiavi per la crittografia dei dischi gestiti.

    > [!IMPORTANT]
    > Non usare la notazione Camel per l'area, altrimenti è possibile che si verifichino problemi durante l'assegnazione di dischi aggiuntivi alla risorsa nel portale di Azure.

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

1.    Creare un'istanza di un set di crittografia dischi. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Concedere al set di crittografia dischi l'accesso all'insieme di credenziali delle chiavi. 

        > [!NOTE]
        > Potrebbero essere necessari alcuni minuti prima che Azure crei l'identità del set di crittografia dischi in Azure Active Directory. Se quando si esegue il comando seguente viene visualizzato un errore simile a "Impossibile trovare l'oggetto Active Directory", attendere qualche minuto e riprovare.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
