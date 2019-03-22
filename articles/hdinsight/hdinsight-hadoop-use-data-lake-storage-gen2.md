---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster HDInsight di Azure.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 45b34d12fbcecbf5f6bf1225c5bb82c5385224ed
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338395"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

Gen2 di archiviazione di Azure Data Lake è un servizio di archiviazione cloud dedicato a analitica dei big data, basato su archiviazione Blob di Azure. Data Lake Storage Gen2 combina le funzionalità di archiviazione Blob di Azure e Azure Data Lake archiviazione Gen1. Il servizio risulta offre le funzionalità da Azure Data Lake archiviazione Gen1, ad esempio la semantica di file system, sicurezza a livello di directory e a livello di file e la scalabilità, nonché l'archiviazione a livelli, a basso costo, la disponibilità elevata e funzionalità di ripristino di emergenza da un archivio Blob di Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilità di Data Lake Storage Gen2

Data Lake Storage Gen2 è disponibile come opzione di archiviazione per quasi tutti i tipi di cluster HDInsight di Azure come un valore predefinito e un account di archiviazione aggiuntivo. HBase, tuttavia, può avere solo un account Data Lake Storage Gen2.

> [!Note] 
> Dopo aver selezionato Data Lake Storage Gen2 come le **tipo di archiviazione primario**, non è possibile selezionare un account Data Lake archiviazione Gen1 come risorsa di archiviazione aggiuntiva.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Creare un cluster con Data Lake Storage Gen2 tramite il portale di Azure

Per creare un cluster HDInsight che usa Data Lake archiviazione Gen2 per l'archiviazione, seguire questi passaggi per configurare un account Data Lake Storage Gen2.

### <a name="create-a-user-managed-identity"></a>Creare un'identità utente gestito

Creare un'identità gestita assegnata dall'utente, se non è già disponibile. Vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Per altre informazioni sulle attività di identità come gestito in Azure HDInsight, vedere [gestite le identità in Azure HDInsight](hdinsight-managed-identities.md).

![Creare un'identità gestita assegnata dall'utente](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Creare un account di Data Lake Storage Gen2

Creare un account di archiviazione di Azure Data Lake Storage Gen2. Assicurarsi che il **spazio dei nomi gerarchico** opzione è abilitata. Per altre informazioni, vedere [Avvio rapido: Creare un account di archiviazione di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurare le autorizzazioni per l'identità gestita per l'account Data Lake Storage Gen2

Assegnare l'identità gestita al ruolo **Proprietario dei dati del BLOB di archiviazione (anteprima)** nell'account di archiviazione. Per altre informazioni, vedere [Gestire i diritti di accesso a dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo (anteprima)](../storage/common/storage-auth-aad-rbac.md).

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare l'account di archiviazione, quindi selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
    
    ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Selezionare il **+ Aggiungi assegnazione di ruolo** pulsante per aggiungere un nuovo ruolo.
1. Nella finestra **Aggiungi assegnazione di ruolo** selezionare il ruolo **Proprietario dei dati del BLOB di archiviazione (anteprima)**. Selezionare quindi la sottoscrizione a cui sono associati l'identità gestita e l'account di archiviazione. Individuare l'identità gestita assegnata dall'utente creata in precedenza. Infine, selezionare l'identità gestita e verrà elencato sotto **i membri selezionati**.
    
    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Selezionare **Salva**. L'identità assegnata dall'utente selezionato viene elencato sotto il ruolo selezionato.
1. Al termine della configurazione iniziale, è possibile creare un cluster tramite il portale. Il cluster deve trovarsi nella stessa area di Azure dell'account di archiviazione. Nella sezione **Archiviazione** del menu di creazione del cluster selezionare le opzioni seguenti:
        
    * Per la **tipo di archiviazione primario**, selezionare **Gen2 di archiviazione di Azure Data Lake**.
    * Sotto **selezionare un account di archiviazione**, cercare e selezionare l'account di archiviazione Gen2 di archivio Data Lake appena creato.
        
        ![Impostazioni di archiviazione per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Sotto **identità**, selezionare la sottoscrizione corretta e l'oggetto appena creato assegnata dall'utente identità gestita.
        
        ![Impostazioni di identità per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> È possibile aggiungere uno o più account Data Lake Storage Gen2 come risorsa di archiviazione secondaria nello stesso cluster. Ripetere i passaggi precedenti in ogni account Data Lake Storage Gen2 che si desidera aggiungere usando la stessa identità gestita.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Creare un cluster con Data Lake Storage Gen2 tramite la CLI di Azure

È possibile [scarica un file di modello di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [scaricare un file di parametri di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Prima di usare il modello, sostituire la stringa `<SUBSCRIPTION_ID>` con l'ID sottoscrizione di Azure effettive. Inoltre, sostituire la stringa `<PASSWORD>` con la password scelta per impostare sia la password che si userà per accedere al cluster e la password SSH.

Il frammento di codice riportato di seguito effettua le seguenti operazioni:

1. Accede al proprio account Azure.
1. Imposta la sottoscrizione attiva in cui verranno eseguite le operazioni di creazione.
1. Crea un nuovo gruppo di risorse per le nuove attività di distribuzione denominato `hdinsight-deployment-rg`.
1. Crea un'identità gestita assegnata dall'utente denominata `test-hdinsight-msi`.
1. Aggiunge un'estensione per il comando di Azure per usare le funzionalità per Data Lake Storage Gen2.
1. Crea un nuovo account Data Lake Storage Gen2 denominato `hdinsightadlsgen2`, usando il `--hierarchical-namespace true` flag.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Successivamente, accedere al portale. Aggiungere la nuova identità gestita assegnata dall'utente per il **collaboratore ai dati di archiviazione Blob (anteprima)** ruolo nell'account di archiviazione, come descritto nel passaggio 3 nella sezione [usando il portale di Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Dopo aver assegnato il ruolo per l'identità gestito assegnata dall'utente, è possibile distribuire il modello usando il frammento di codice seguente.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controllo di accesso per Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quali tipi di autorizzazioni sono supportati da Data Lake Storage Gen2?

Data Lake Storage Gen2 Usa un modello di controllo di accesso che supporta il controllo di accesso basato sui ruoli (RBAC) sia elenchi di controllo di accesso di tipo POSIX (ACL). Data Lake archiviazione Gen1 supporta l'accesso gli elenchi di controllo solo per il controllo dell'accesso ai dati.

RBAC Usa le assegnazioni di ruolo da applicare in modo efficace set di autorizzazioni per gli utenti, gruppi ed entità di servizio per le risorse di Azure. In genere, le risorse di Azure sono vincolate a risorse di primo livello (ad esempio, gli account di archiviazione di Azure). Per archiviazione di Azure e Data Lake Storage Gen2, questo meccanismo è stato esteso per la risorsa del file system.

 Per altre informazioni sulle autorizzazioni per file con RBAC, vedere [controllo degli accessi in base al ruolo di Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Per altre informazioni sulle autorizzazioni per file con gli ACL, vedere [elenchi di controllo di accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Come si controlla l'accesso ai dati in Data Lake Storage Gen2?

Capacità del cluster HDInsight di accedere ai file in Data Lake Storage Gen2 viene controllato mediante identità gestite. Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali vengono gestite da Azure. Con identità gestite, non è necessario registrare le entità servizio in Azure AD o gestire le credenziali, ad esempio i certificati.

Servizi di Azure hanno due tipi di identità gestite: assegnato dal sistema e assegnata dall'utente. HDInsight Usa identità gestite assegnata dall'utente per l'accesso a Data Lake Storage Gen2. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure.

Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. Per altre informazioni sulle identità gestita, vedere [come eseguire le identità gestite per il lavoro di risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Impostazione delle autorizzazioni per gli utenti di Azure AD per eseguire query sui dati in Data Lake Storage Gen2 con Hive o altri servizi?

Per impostare le autorizzazioni per gli utenti di eseguire query sui dati, usare i gruppi di sicurezza di Azure AD come entità assegnato negli ACL. Non assegnare autorizzazioni di accesso ai file direttamente a singoli utenti o entità servizio. Quando si usano i gruppi di sicurezza di Azure AD per controllare il flusso di autorizzazioni, è possibile aggiungere e rimuovere utenti o entità servizio senza riapplicare gli ACL a una struttura di directory completa. È sufficiente aggiungere o rimuovere gli utenti dal gruppo di sicurezza di Azure AD appropriato. Gli ACL non vengono ereditati. è quindi riapplicare gli ACL sarà necessario aggiornare l'elenco ACL per ogni file e sottodirectory.

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'anteprima di Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integrazione di Azure HDInsight con Data Lake Storage Gen2 (anteprima) - Aggiornamento di ACL e sicurezza)
* [Anteprima di Azure Data Lake Storage Gen2 - Introduzione](../storage/blobs/data-lake-storage-introduction.md)
