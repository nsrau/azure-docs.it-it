---
title: Crittografia del disco della chiave gestita dal cliente per Azure HDInsight
description: Questo articolo descrive come usare la propria chiave di crittografia da Azure Key Vault per crittografare i dati archiviati nei dischi gestiti nei cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c22ee0ef0393c0dae64674d18bae5a2e92969b4c
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206061"
---
# <a name="customer-managed-key-disk-encryption"></a>Crittografia del disco con chiavi gestite dal cliente

Azure HDInsight supporta la crittografia delle chiavi gestita dal cliente per i dati nei dischi gestiti e nei dischi delle risorse collegati alle macchine virtuali del cluster HDInsight. Questa funzionalità consente di usare Azure Key Vault per gestire le chiavi di crittografia che proteggono i dati inattivi nei cluster HDInsight. 

Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilitano le chiavi gestite dal cliente per HDInsight, si specificano le chiavi di crittografia per HDInsight per l'uso e la gestione delle chiavi usando Azure Key Vault.

Questo documento non indirizza i dati archiviati nell'account di archiviazione di Azure. Per altre informazioni sulla crittografia di archiviazione di Azure, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi. I cluster possono avere uno o più account di archiviazione di Azure collegati in cui le chiavi di crittografia possono anche essere gestite da Microsoft o gestite dal cliente, ma il servizio di crittografia è diverso.

## <a name="introduction"></a>Introduzione

La crittografia a chiave gestita dal cliente è un processo che viene gestito in un unico passaggio durante la creazione del cluster senza costi aggiuntivi. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Sia il disco delle risorse che i dischi gestiti in ogni nodo del cluster vengono crittografati con una chiave di crittografia dei dati simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight.

Se il firewall dell'insieme di credenziali delle chiavi è abilitato nell'insieme di credenziali delle chiavi in cui è archiviata la chiave di crittografia del disco, gli indirizzi IP del provider di risorse di HDInsight per l'area in cui verrà distribuito il cluster devono essere aggiunti alla configurazione del firewall di Key Vault. Questa operazione è necessaria perché HDInsight non è un servizio di Azure Key Vault attendibile.

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando si ruota un tasto, il cluster HDInsight inizia a usare la nuova chiave in pochi minuti. Abilitare le funzionalità di protezione delle chiavi di [eliminazione](../key-vault/key-vault-ovw-soft-delete.md) temporanea per la protezione da scenari ransomware e da eliminazioni accidentali. Gli insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

|Tipo di cluster |Disco del sistema operativo (disco gestito) |Disco dati (disco gestito) |Disco dati temporanei (unità SSD locale) |
|---|---|---|---|
|Kafka, HBase con scritture accelerate|Crittografia SSE|Crittografia SSE + crittografia CMK facoltativa|Crittografia CMK facoltativa|
|Tutti gli altri cluster (Spark, Interactive, Hadoop, HBase senza scritture accelerate)|Crittografia SSE|N/D|Crittografia CMK facoltativa|

## <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per creare un cluster HDInsight abilitato per la chiave gestita dal cliente, seguire questa procedura:

1. Creare identità gestite per le risorse di Azure
1. Creare un Azure Key Vault
1. Creare la chiave
1. Crea criteri di accesso
1. Creare un cluster HDInsight con la chiave gestita dal cliente abilitata
1. Rotazione della chiave di crittografia

## <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestite per le risorse di Azure

Creare un'identità gestita assegnata dall'utente per l'autenticazione Key Vault.

Per i passaggi specifici, vedere [creare un'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](hdinsight-managed-identities.md). Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

## <a name="create-azure-key-vault"></a>Creare un Azure Key Vault

Creare un insieme di credenziali delle chiavi. Per i passaggi specifici, vedere [creare Azure Key Vault](../key-vault/quick-create-portal.md) .

In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che l'insieme di credenziali delle chiavi deve avere l' **eliminazione** temporanea abilitata. Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Creare la chiave

1. Dal nuovo insieme di credenziali delle chiavi passare a **impostazioni** > **chiavi** >  **+ genera/importa**.

    ![Genera una nuova chiave in Azure Key Vault](./media/disk-encryption/create-new-key.png "Generare una nuova chiave in Azure Key Vault")

1. Specificare un nome e quindi selezionare **Crea**. Mantenere il **tipo di chiave** predefinito di **RSA**.

    ![genera il nome della chiave](./media/disk-encryption/create-key.png "Genera nome chiave")

1. Quando si torna alla pagina **chiavi** , selezionare la chiave creata.

    ![Elenco chiavi Key Vault](./media/disk-encryption/key-vault-key-list.png)

1. Selezionare la versione per aprire la pagina della **versione della chiave** . Quando si usa una chiave personalizzata per la crittografia del cluster HDInsight, è necessario specificare l'URI della chiave. Copiare l'**identificatore di chiave** e salvarlo in qualunque posizione fino a quando non si è pronti per creare il cluster.

    ![ottenere l'identificatore di chiave](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Crea criteri di accesso

1. Dal nuovo insieme di credenziali delle chiavi passare a **impostazioni** > **criteri di accesso** >  **+ Aggiungi criteri di accesso**.

    ![Creare il nuovo criterio di accesso di Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Nella pagina **Aggiungi criteri di accesso** specificare le informazioni seguenti:

    |Proprietà |Descrizione|
    |---|---|
    |Autorizzazioni chiave|Selezionare **Get**, **Unwrap Key**e **Wrap Key**.|
    |Autorizzazioni segrete|Selezionare **Get**, **set**ed **Delete**.|
    |Seleziona entità|Selezionare l'identità gestita assegnata dall'utente creata in precedenza.|

    ![Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Fare clic su **Aggiungi**.

1. Selezionare **Salva**.

    ![Salva i criteri di accesso Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Creare un cluster con crittografia del disco della chiave gestita dal cliente

È ora possibile creare un nuovo cluster HDInsight. La chiave gestita dal cliente può essere applicata solo ai nuovi cluster durante la creazione del cluster. Non è possibile rimuovere la crittografia dai cluster di chiavi gestite dal cliente e non è possibile aggiungere la chiave gestita dal cliente ai cluster esistenti.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Durante la creazione del cluster, specificare l' **identificatore di chiave**completo, inclusa la versione della chiave. Ad esempio: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. È anche necessario assegnare l'identità gestita al cluster e fornire l'URI della chiave.

![Crea nuovo cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per creare un nuovo cluster di Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure AZ HDInsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Rotazione della chiave di crittografia

Potrebbero esserci scenari in cui potrebbe essere necessario modificare le chiavi di crittografia usate dal cluster HDInsight dopo che è stato creato. Questo può essere facilmente tramite il portale. Per questa operazione, il cluster deve avere accesso sia alla chiave corrente che alla nuova chiave prevista. in caso contrario, l'operazione di rotazione del tasto avrà esito negativo.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per ruotare la chiave, è necessario l'URI dell'insieme di credenziali delle chiavi di base. Al termine, passare alla sezione proprietà del cluster HDInsight nel portale e fare clic su **cambia chiave** in **URL chiave di crittografia del disco**. Immettere l'URL della nuova chiave e inviare per ruotare la chiave.

![ruotare la chiave di crittografia del disco](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Nell'esempio seguente viene illustrato come ruotare la chiave di crittografia del disco per un cluster HDInsight esistente. Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure AZ HDInsight rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Domande frequenti sulla crittografia della chiave gestita dal cliente

**In che modo il cluster HDInsight accede all'insieme di credenziali delle chiavi?**

HDInsight accede all'istanza di Azure Key Vault usando l'identità gestita associata al cluster HDInsight. È possibile creare questa identità gestita prima o durante la creazione del cluster. È anche necessario concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi in cui viene archiviata la chiave.

**Questa funzionalità è disponibile per tutti i cluster in HDInsight?**

La crittografia della chiave gestita dal cliente è disponibile per tutti i tipi di cluster eccetto Spark 2,1 e 2,2.

**È possibile usare più chiavi per crittografare dischi o cartelle differenti?**

No, tutti i dischi gestiti e i dischi delle risorse vengono crittografati con la stessa chiave.

**Cosa accade se il cluster perde l'accesso all'insieme di credenziali delle chiavi o alla chiave?**

Se il cluster perde l'accesso alla chiave, gli avvisi vengono visualizzati nel portale di Apache Ambari. In questo stato, l'operazione di **modifica della chiave** avrà esito negativo. Una volta ripristinato l'accesso alla chiave, gli avvisi di Ambari verranno abbandonati e le operazioni come la rotazione delle chiavi possono essere eseguite correttamente.

![avviso di Ambari di accesso alla chiave](./media/disk-encryption/ambari-alert.png)

**Come è possibile recuperare il cluster se le chiavi vengono eliminate?**

Poiché sono supportate solo le chiavi abilitate per l'eliminazione temporanea, se le chiavi vengono ripristinate nell'insieme di credenziali delle chiavi, il cluster dovrebbe riottenere l'accesso alle chiavi. Per recuperare una chiave di Azure Key Vault, vedere [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) o [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Quali tipi di dischi sono crittografati? Sono crittografati anche dischi del sistema operativo/dischi delle risorse?**

I dischi di risorse e i dischi dati/gestiti sono crittografati. I dischi del sistema operativo non sono crittografati.

**Se si aumenta la scalabilità verticale di un cluster, i nuovi nodi supporteranno le chiavi gestite dal cliente in modo uniforme?**

Sì. Il cluster deve avere accesso alla chiave nell'insieme di credenziali delle chiavi durante il passaggio a un piano superiore. La stessa chiave viene usata per crittografare i dischi gestiti e i dischi di risorse nel cluster.

**Le chiavi gestite dal cliente sono disponibili nella mia posizione?**

Le chiavi gestite dal cliente di HDInsight sono disponibili in tutti i cloud pubblici e nei cloud nazionali.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault](../key-vault/key-vault-overview.md).
* [Panoramica della sicurezza aziendale in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
