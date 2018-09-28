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
ms.openlocfilehash: 46105ee92a5c98cb8180b2499d0ad295702aac43
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953372"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Bring Your Own Key per Apache Kafka in Azure HDInsight (anteprima)

Azure HDInsight include il supporto per la modalità Bring Your Own Key (BYOK) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle. 

Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilita la modalità BYOK, si specifica la chiave di crittografia per HDInsight da usare e gestire con Azure Key Vault. 

La crittografia BYOK è un processo costituito da un singolo passaggio gestito durante la creazione del cluster senza alcun costo aggiuntivo. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Tutti i messaggi inviati al cluster Kafka (incluse le repliche gestite da Kafka) vengono crittografati con una chiave DEK simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight. 

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando una chiave ruota, il cluster HDInsight Kafka inizia a usare la nuova chiave entro pochi minuti. Abilitare le funzionalità di protezione dall'eliminazione e di eliminazione temporanea delle chiavi per proteggersi da scenari di ransomware e dall'eliminazione accidentale. Le chiavi senza queste funzionalità di protezione non sono supportate.

## <a name="get-started-with-byok"></a>Introduzione a BYOK

1. Creare identità gestite per le risorse di Azure.

   Per l'autenticazione in Key Vault, creare un'identità gestita assegnata dall'utente con il [portale di Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md) o l'[interfaccia della riga di comando di Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Mentre Azure Active Directory è necessario per le identità gestite e BYOK per Kafka, Enterprise Security Package (ESP) non è un requisito. Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

   ![Creare l'identità gestita assegnata dall'utente nel portale di Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Creare o importare Azure Key Vault.

   In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Ricordare che le chiavi devono avere le funzionalità di protezione dall'eliminazione e di eliminazione temporanea abilitate. Le funzionalità di eliminazione temporanea e di protezione dall'eliminazione sono disponibili attraverso le interfacce REST, .NET/C#, PowerShell e della riga di comando di Azure.

   Per creare un nuovo insieme di credenziali delle chiavi, seguire la guida introduttiva [Azure Key Vault](../../key-vault/key-vault-get-started.md). Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md).

3. Aggiungere l'identità gestita al criterio di accesso dell'insieme di credenziali delle chiavi.

   Creare un nuovo criterio di accesso di Azure Key Vault.

   ![Creare il nuovo criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Impostare **Autorizzazioni chiave** su **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.

   ![Impostare Autorizzazioni chiave per il criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Impostare **Autorizzazioni segrete** su **Recupera**, **Imposta** ed **Elimina**.

   ![Impostare Autorizzazioni chiave per il criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

   In **Selezionare un'entità** scegliere l'identità gestita assegnata dall'utente creata.

   ![Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

4. Creare un cluster HDInsight

   È ora possibile creare un nuovo cluster HDInsight. BYOK è applicabile solo ai nuovi cluster durante la creazione di un cluster. La crittografia non può essere rimossa dai cluster BYOK e la modalità BYOK non può essere aggiunta ai cluster esistenti.

   ![Crittografia dei dischi Kafka nel portale di Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Durante la creazione del cluster, specificare l'URL della chiave completo, inclusa la versione della chiave. Ad esempio: `myakv.azure.com/KEK1/v1`. È anche necessario assegnare l'identità gestita al cluster e fornire l'URI della chiave.

## <a name="faq-for-byok-to-kafka"></a>Domande frequenti su BYOK per Kafka

**Come accede il cluster Kafka all'insieme di credenziali delle chiavi?**

   Associare un'identità gestita al cluster HDInsight Kafka durante la creazione del cluster. È possibile creare questa identità gestita prima o durante la creazione del cluster. È anche necessario concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi in cui viene archiviata la chiave.

**Questa funzionalità è disponibile per tutti i cluster Kafka in HDInsight?**

   La crittografia BYOK è possibile solo per i cluster Kafka 1.1 e versioni successive.

**È possibile avere chiavi diverse a seconda degli argomenti o delle partizioni?**

   No, tutti i dischi gestiti nel cluster vengono crittografati dalla stessa chiave.

**Come è possibile recuperare il cluster se le chiavi vengono eliminate?**

   Poiché sono supportate solo le chiavi abilitate per l'eliminazione temporanea, se le chiavi vengono ripristinate nell'insieme di credenziali delle chiavi, il cluster dovrebbe ottenere nuovamente l'accesso alle chiavi. Per ripristinare una chiave di Azure Key Vault, vedere [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**È possibile avere applicazioni producer/consumer che usano contemporaneamente un cluster BYOK e un cluster non BYOK?**

   Sì. L'uso di BYOK è trasparente per le applicazioni producer/consumer. La crittografia avviene a livello di sistema operativo. Non è necessario apportare modifiche alle applicazioni Kafka producer/consumer esistenti.

**Vengono crittografati anche i dischi del sistema operativo/dischi delle risorse?**

   No. I dischi del sistema operativo e i dischi delle risorse non vengono crittografati.

**Se un cluster passa a un piano superiore, i nuovi broker supporteranno BYOK senza problemi?**

   Sì. Il cluster deve avere accesso alla chiave nell'insieme di credenziali delle chiavi durante il passaggio a un piano superiore. La stessa chiave viene usata per crittografare tutti i dischi gestiti nel cluster.

**Come è possibile sapere se BYOK è disponibile nella propria località?**

   BYOK per Kafka è disponibile in tutti i cloud pubblici.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Azure Key Vault, vedere [Informazioni su Azure Key Vault](../../key-vault/key-vault-whatis.md).
* Per iniziare a usare Azure Key Vault, vedere [Introduzione ad Azure Key Vault](../../key-vault/key-vault-get-started.md).
