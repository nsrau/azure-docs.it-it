---
title: Bring your own key per Apache Kafka in Azure HDInsight
description: Questo articolo descrive come usare la propria chiave di Azure Key Vault per crittografare i dati archiviati in Apache Kafka in Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 15638d90fe24938a45f6d4cce156e998f1f9afc2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000107"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Bring your own key per Apache Kafka in Azure HDInsight

Azure HDInsight include il supporto per la modalità Bring Your Own Key (BYOK) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle.

Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilita la modalità BYOK, si specifica la chiave di crittografia per HDInsight da usare e gestire con Azure Key Vault.

La crittografia BYOK è un processo costituito da un singolo passaggio gestito durante la creazione del cluster senza alcun costo aggiuntivo. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Tutti i messaggi inviati al cluster Kafka (incluse le repliche gestite da Kafka) vengono crittografati con una chiave DEK simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight. 

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando una chiave ruota, il cluster HDInsight Kafka inizia a usare la nuova chiave entro pochi minuti. Abilitare le funzionalità di protezione con chiave "eliminazione temporanea" per proteggersi da scenari ransomware e da eliminazioni accidentali. Gli insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

## <a name="get-started-with-byok"></a>Introduzione a BYOK
Per creare un cluster Kafka abilitato per BYOK, seguire questa procedura:
1. Creare identità gestite per le risorse di Azure
2. Configurare Azure Key Vault e chiavi
3. Creare un cluster HDInsight Kafka con BYOK abilitato
4. Rotazione della chiave di crittografia

## <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestite per le risorse di Azure

   Per eseguire l'autenticazione a Key Vault, creare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di [portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)o [Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](../hdinsight-managed-identities.md). Mentre Azure Active Directory è necessario per le identità gestite e BYOK per Kafka, Enterprise Security Package (ESP) non è un requisito. Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

   ![Creare l'identità gestita assegnata dall'utente nel portale di Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Configurare il Key Vault e le chiavi

   In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che le chiavi devono avere "eliminazione temporanea". La funzionalità di eliminazione temporanea è disponibile tramite le interfacce REST, .NET/C#, PowerShell e l'interfaccia della riga di comando di Azure.

   1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la guida introduttiva [Azure Key Vault](../../key-vault/key-vault-overview.md). Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi usando il comando [AZ Key Vault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.
        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Creare chiavi

        a. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.

        ![Genera una nuova chiave in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Genera una nuova chiave in Azure Key Vault")

        b. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Genera nome chiave](./media/apache-kafka-byok/apache-kafka-create-key.png "Genera nome chiave")

        c. Selezionare la chiave creata nell'elenco di chiavi.

        ![Elenco di chiavi di Azure Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. Quando si usa la propria chiave per la crittografia di cluster Kafka, è necessario specificare l'URI della chiave. Copiare l'**identificatore di chiave** e salvarlo in qualunque posizione fino a quando non si è pronti per creare il cluster.

        ![Copiare l'identificatore di chiave](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Aggiungere l'identità gestita al criterio di accesso dell'insieme di credenziali delle chiavi.

        a. Creare un nuovo criterio di accesso di Azure Key Vault.

        ![Creare il nuovo criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. In **Selezionare un'entità** scegliere l'identità gestita assegnata dall'utente creata.

        ![Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Impostare **Autorizzazioni chiave** su **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.

        ![Impostare le autorizzazioni chiave per Azure Key Vault Access Policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Impostare le autorizzazioni chiave per Azure Key Vault Access Policy1")

        d. Impostare **Autorizzazioni segrete** su **Recupera**, **Imposta** ed **Elimina**.

        ![Impostare le autorizzazioni chiave per Azure Key Vault Access policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Impostare le autorizzazioni chiave per Azure Key Vault Access policy2")

        e. Fare clic su **Save**. 

        ![Salva i criteri di accesso Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Crea cluster HDInsight

   È ora possibile creare un nuovo cluster HDInsight. BYOK è applicabile solo ai nuovi cluster durante la creazione di un cluster. La crittografia non può essere rimossa dai cluster BYOK e la modalità BYOK non può essere aggiunta ai cluster esistenti.

   ![Crittografia dei dischi Kafka nel portale di Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Durante la creazione del cluster, specificare l'URL della chiave completo, inclusa la versione della chiave. Ad esempio `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. È anche necessario assegnare l'identità gestita al cluster e fornire l'URI della chiave.

## <a name="rotating-the-encryption-key"></a>Rotazione della chiave di crittografia
   Potrebbero esserci scenari in cui potrebbe essere necessario modificare le chiavi di crittografia usate dal cluster Kafka dopo che è stato creato. Questo può essere facilmente tramite il portale. Per questa operazione, il cluster deve avere accesso sia alla chiave corrente che alla nuova chiave prevista. in caso contrario, l'operazione di rotazione del tasto avrà esito negativo.

   Per ruotare la chiave, è necessario avere l'URL completo della nuova chiave (vedere il passaggio 3 della [procedura di impostazione del Key Vault e delle chiavi](#setup-the-key-vault-and-keys)). Al termine, passare alla sezione proprietà del cluster Kafka nel portale e fare clic su **cambia chiave** in **URL chiave di crittografia del disco**. Immettere l'URL della nuova chiave e inviare per ruotare la chiave.

   ![Kafka ruotare la chiave di crittografia del disco](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Domande frequenti su BYOK per Apache Kafka

**Come accede il cluster Kafka all'insieme di credenziali delle chiavi?**

   Associare un'identità gestita al cluster HDInsight Kafka durante la creazione del cluster. È possibile creare questa identità gestita prima o durante la creazione del cluster. È anche necessario concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi in cui viene archiviata la chiave.

**Questa funzionalità è disponibile per tutti i cluster Kafka in HDInsight?**

   La crittografia BYOK è possibile solo per i cluster Kafka 1.1 e versioni successive.

**È possibile avere chiavi diverse a seconda degli argomenti o delle partizioni?**

   No, tutti i dischi gestiti nel cluster vengono crittografati dalla stessa chiave.

**Cosa accade se il cluster perde l'accesso all'insieme di credenziali delle chiavi o alla chiave?**
Se il cluster perde l'accesso alla chiave, gli avvisi vengono visualizzati nel portale di Apache Ambari. In questo stato, l'operazione di **modifica della chiave** avrà esito negativo. Una volta ripristinato l'accesso alla chiave, gli avvisi di Ambari verranno abbandonati e le operazioni come la rotazione delle chiavi possono essere eseguite correttamente.

   ![Avviso di Ambari per l'accesso alla chiave Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Come è possibile recuperare il cluster se le chiavi vengono eliminate?**

   Poiché sono supportate solo le chiavi abilitate per l'eliminazione temporanea, se le chiavi vengono ripristinate nell'insieme di credenziali delle chiavi, il cluster dovrebbe riottenere l'accesso alle chiavi. Per recuperare una chiave di Azure Key Vault, vedere [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) o [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**È possibile avere applicazioni producer/consumer che usano contemporaneamente un cluster BYOK e un cluster non BYOK?**

   Sì. L'uso di BYOK è trasparente per le applicazioni producer/consumer. La crittografia avviene a livello di sistema operativo. Non è necessario apportare modifiche alle applicazioni Kafka producer/consumer esistenti.

**Vengono crittografati anche i dischi del sistema operativo/dischi delle risorse?**

   No. I dischi del sistema operativo e i dischi delle risorse non vengono crittografati.

**Se un cluster passa a un piano superiore, i nuovi broker supporteranno BYOK senza problemi?**

   Sì. Il cluster deve avere accesso alla chiave nell'insieme di credenziali delle chiavi durante il passaggio a un piano superiore. La stessa chiave viene usata per crittografare tutti i dischi gestiti nel cluster.

**Come è possibile sapere se BYOK è disponibile nella propria località?**

   BYOK per Kafka è disponibile in tutti i cloud pubblici.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Azure Key Vault, vedere [Informazioni su Azure Key Vault](../../key-vault/key-vault-overview.md).
* Per iniziare a usare Azure Key Vault, vedere [Introduzione ad Azure Key Vault](../../key-vault/key-vault-overview.md).
