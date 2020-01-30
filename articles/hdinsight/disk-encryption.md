---
title: Crittografia del disco della chiave gestita dal cliente per Azure HDInsight
description: Questo articolo descrive come usare la propria chiave di crittografia da Azure Key Vault per crittografare i dati archiviati nei dischi gestiti nei cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: b452cb986e6f662aeb33c2a475f18695ebc75745
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846099"
---
# <a name="customer-managed-key-disk-encryption"></a>Crittografia del disco con chiavi gestite dal cliente

Azure HDInsight supporta le chiavi gestite dal cliente, nota anche come crittografia Bring Your Own Key (BYOK) per i dati nei dischi gestiti e nei dischi delle risorse collegati alle VM del cluster HDInsight. Questa funzionalità consente di usare Azure Key Vault per gestire le chiavi di crittografia che proteggono i dati inattivi nei cluster HDInsight. I cluster possono avere uno o più account di archiviazione di Azure collegati in cui le chiavi di crittografia possono anche essere gestite da Microsoft o gestite dal cliente, ma il servizio di crittografia è diverso.

Questo documento non indirizza i dati archiviati nell'account di archiviazione di Azure. Per altre informazioni sull'ambiente del servizio app, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.

Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilitano le chiavi gestite dal cliente per HDInsight, si specificano le chiavi di crittografia per HDInsight per l'uso e la gestione delle chiavi usando Azure Key Vault.

La crittografia a chiave gestita dal cliente è un processo che viene gestito in un unico passaggio durante la creazione del cluster senza costi aggiuntivi. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Sia il disco delle risorse che i dischi gestiti in ogni nodo del cluster vengono crittografati con una chiave di crittografia dei dati simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight.

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando si ruota un tasto, il cluster HDInsight inizia a usare la nuova chiave in pochi minuti. Abilitare le funzionalità di protezione con chiave "eliminazione temporanea" per proteggersi da scenari ransomware e da eliminazioni accidentali. Gli insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

## <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per creare un cluster HDInsight abilitato per la chiave gestita dal cliente, seguire questa procedura:

1. Creare identità gestite per le risorse di Azure
2. Configurare Azure Key Vault e chiavi
3. Creare un cluster HDInsight con la chiave gestita dal cliente abilitata
4. Rotazione della chiave di crittografia

## <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestite per le risorse di Azure

Per eseguire l'autenticazione a Key Vault, creare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di [portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)o [Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](hdinsight-managed-identities.md). Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

## <a name="set-up-the-key-vault-and-keys"></a>Configurare il Key Vault e le chiavi

In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che le chiavi devono avere "eliminazione temporanea". La funzionalità di eliminazione temporanea è disponibile tramite le interfacce REST, .NET/C#, PowerShell e l'interfaccia della riga di comando di Azure.

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la guida introduttiva [Azure Key Vault](../key-vault/key-vault-overview.md). Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).

1. Abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi usando il comando [AZ Key Vault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Creazione di chiavi.

    a. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.

    ![Genera una nuova chiave in Azure Key Vault](./media/disk-encryption/create-new-key.png "Generare una nuova chiave in Azure Key Vault")

    b. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

    ![genera il nome della chiave](./media/disk-encryption/create-key.png "Genera nome chiave")

    c. Selezionare la chiave creata nell'elenco di chiavi.

    ![Elenco chiavi Key Vault](./media/disk-encryption/key-vault-key-list.png)

    d. Quando si usa una chiave personalizzata per la crittografia del cluster HDInsight, è necessario specificare l'URI della chiave. Copiare l'**identificatore di chiave** e salvarlo in qualunque posizione fino a quando non si è pronti per creare il cluster.

    ![ottenere l'identificatore di chiave](./media/disk-encryption/get-key-identifier.png)

1. Aggiungere l'identità gestita al criterio di accesso dell'insieme di credenziali delle chiavi.

    a. Creare un nuovo criterio di accesso di Azure Key Vault.

    ![Creare il nuovo criterio di accesso di Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy.png)

    b. In **Selezionare un'entità** scegliere l'identità gestita assegnata dall'utente creata.

    ![Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

    c. Impostare **Autorizzazioni chiave** su **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.

    ![Impostare le autorizzazioni chiave per Azure Key Vault Access Policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Impostare le autorizzazioni chiave per Azure Key Vault Access Policy1")

    d. Impostare **Autorizzazioni segrete** su **Recupera**, **Imposta** ed **Elimina**.

    ![Impostare le autorizzazioni chiave per Azure Key Vault Access policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Impostare le autorizzazioni chiave per Azure Key Vault Access policy2")

    e. Selezionare **Salva**.

    ![Salva i criteri di accesso Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Creare un cluster con crittografia del disco della chiave gestita dal cliente

È ora possibile creare un nuovo cluster HDInsight. La chiave gestita dal cliente può essere applicata solo ai nuovi cluster durante la creazione del cluster. Non è possibile rimuovere la crittografia dai cluster di chiavi gestite dal cliente e non è possibile aggiungere la chiave gestita dal cliente ai cluster esistenti.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Durante la creazione del cluster, specificare l'URL della chiave completo, inclusa la versione della chiave. Ad esempio: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. È anche necessario assegnare l'identità gestita al cluster e fornire l'URI della chiave.

![Crea un nuovo cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per creare un nuovo cluster di Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere la documentazione dell'interfaccia della riga di comando di [Azure AZ HDInsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) .

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

Per ruotare la chiave, è necessario avere l'URL completo della nuova chiave (vedere il passaggio 3 della [procedura di impostazione del Key Vault e delle chiavi](#set-up-the-key-vault-and-keys)). Al termine, passare alla sezione proprietà del cluster HDInsight nel portale e fare clic su **cambia chiave** in **URL chiave di crittografia del disco**. Immettere l'URL della nuova chiave e inviare per ruotare la chiave.

![ruotare la chiave di crittografia del disco](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Nell'esempio seguente viene illustrato come ruotare la chiave di crittografia del disco per un cluster HDInsight esistente. Per altri dettagli, vedere l'interfaccia della riga di comando di [Azure AZ HDInsight rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) .

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

* [Panoramica della sicurezza aziendale in Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
