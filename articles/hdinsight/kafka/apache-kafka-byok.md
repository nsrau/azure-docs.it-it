---
title: Bring Your Own Key per Apache Kafka in Azure HDInsight (anteprima)
description: Questo articolo descrive come usare la propria chiave di Azure Key Vault per crittografare i dati archiviati in Apache Kafka in Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b5f7c472c8ebd60d8e7f928534834c9672fe3b14
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471308"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Bring Your Own Key per Apache Kafka in Azure HDInsight (anteprima)

Azure HDInsight include il supporto per la modalità Bring Your Own Key (BYOK) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle. 

Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilita la modalità BYOK, si specifica la chiave di crittografia per HDInsight da usare e gestire con Azure Key Vault. 

La crittografia BYOK è un processo costituito da un singolo passaggio gestito durante la creazione del cluster senza alcun costo aggiuntivo. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Tutti i messaggi inviati al cluster Kafka (incluse le repliche gestite da Kafka) vengono crittografati con una chiave DEK simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight. 

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando una chiave ruota, il cluster HDInsight Kafka inizia a usare la nuova chiave entro pochi minuti. Abilitare le funzionalità di protezione con chiave "Eliminazione temporanea" per la protezione da ransomware scenari e l'eliminazione accidentale. Insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Introduzione a BYOK
Per creare una modalità BYOK abilitato cluster Kafka, verranno esaminati i passaggi seguenti:
1. Creare identità gestita per le risorse di Azure
2. Configurare Azure Key Vault e chiavi
3. Creare cluster HDInsight Kafka con BYOK abilitata

## <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestita per le risorse di Azure

   Per eseguire l'autenticazione in Key Vault, creare un'identità gestito assegnata dall'utente usando il [portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), o [ Interfaccia della riga di comando Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Per altre informazioni sulle attività di identità come gestito in Azure HDInsight, vedere [gestite le identità in Azure HDInsight](../hdinsight-managed-identities.md). Mentre Azure Active Directory è necessario per le identità gestite e BYOK per Kafka, Enterprise Security Package (ESP) non è un requisito. Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

   ![Creare l'identità gestita assegnata dall'utente nel portale di Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Configurare l'insieme di credenziali delle chiavi e chiavi

   In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che le chiavi devono disporre di "Eliminazione temporanea". La funzionalità di "Eliminazione temporanea" è disponibile attraverso il resto, .NET /C#, interfacce di PowerShell e CLI di Azure.

   1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la guida introduttiva [Azure Key Vault](../../key-vault/key-vault-overview.md). Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Abilitare "eliminazione temporanea" nell'insieme di credenziali chiave tramite il [aggiornamento di az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) riga di comando.
        ' ' Aggiornamento di az CLI azure Key Vault-- nome <Key Vault Name> -enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.
        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save** 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
