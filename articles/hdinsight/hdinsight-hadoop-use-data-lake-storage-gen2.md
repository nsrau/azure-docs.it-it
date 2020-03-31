---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster di Azure HDInsight.Learn how to use Azure Data Lake Storage Gen2 with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272292"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

Azure Data Lake Storage Gen2 è un servizio di archiviazione cloud dedicato all'analisi dei Big Data, basato sull'archiviazione BLOB di Azure.Azure Data Lake Storage Gen2 is a cloud storage service dedicated to big data analytics, built on Azure Blob storage. Data Lake Storage Gen2 combina le funzionalità di Archiviazione BLOB di Azure e Azure Data Lake Storage Gen1. Il servizio risultante offre funzionalità offerte da Azure Data Lake Storage Gen1, ad esempio la semantica del file system, la sicurezza a livello di directory e di file e la scalabilità, insieme alle funzionalità a basso costo, archiviazione a livelli, disponibilità elevata e ripristino di emergenza dall'archiviazione BLOB di Azure.From Azure Blob storage.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilità di Data Lake Storage Gen2

Data Lake Storage Gen2 è disponibile come opzione di archiviazione per quasi tutti i tipi di cluster di Azure HDInsight sia come account predefinito che come account di archiviazione aggiuntivo. HBase, tuttavia, può avere un solo account Data Lake Storage Gen2.

Per un confronto completo delle opzioni di creazione del cluster usando Data Lake Storage Gen2, vedere Confrontare le opzioni di archiviazione da usare con i cluster di Azure HDInsight.For a complete comparison of cluster creation options using Data Lake Storage Gen2, see [Compare storage options for use with Azure HDInsight clusters.](hdinsight-hadoop-compare-storage-options.md)

> [!Note]  
> Dopo aver selezionato Data Lake Storage Gen2 come tipo di **archiviazione principale,** non è possibile selezionare un account Data Lake Storage Gen1 come spazio di archiviazione aggiuntivo.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Creare un cluster con Data Lake Storage Gen2 tramite il portale di AzureCreate a cluster with Data Lake Storage Gen2 through the Azure portal

Per creare un cluster HDInsight che usa Data Lake Storage Gen2 per l'archiviazione, seguire questi passaggi per configurare un account Data Lake Storage Gen2.To create an HDInsight cluster that uses Data Lake Storage Gen2 for storage, follow these steps to configure a Data Lake Storage Gen2 account.

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente, se non è già disponibile.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **utente assegnato** e fare clic su **Identità gestita assegnata dall'utente**.
1. Fare clic su **Crea**.
1. Immettere un nome per l'identità gestita, selezionare la sottoscrizione, il gruppo di risorse e il percorso corretti.
1. Fare clic su **Crea**.

Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere Identità gestite in Azure HDInsight.For more information on how managed identities work in Azure HDInsight, see [Managed identities in Azure HDInsight.](hdinsight-managed-identities.md)

![Creare un'identità gestita assegnata dall'utente](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Creare un account di Data Lake Storage Gen2

Creare un account di archiviazione di Azure Data Lake Storage Gen2.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **storage** e fare clic su **Account di archiviazione**.
1. Fare clic su **Crea**.
1. Nella schermata **Crea account di archiviazione:On** the Create storage account screen:
    1. Selezionare la sottoscrizione e il gruppo di risorse corretti.
    1. Immettere un nome per l'account Data Lake Storage Gen2. Per altre informazioni sulle convenzioni di denominazione degli account di archiviazione, vedere Convenzioni di denominazione per le risorse di Azure.For more information on storage account naming [conventions, see Naming conventions for Azure resources.](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)
    1. Fare clic sulla scheda **Avanzate.**
    1. Fare clic su **Abilitato** accanto a **Spazio dei nomi gerarchico** in **Data Lake Storage Gen2**.
    1. Fare clic su **Revisione e creazione**.
    1. Fare clic su **Crea**

Per altre informazioni sulle altre opzioni durante la creazione dell'account di archiviazione, vedere Guida introduttiva: Creare un account di archiviazione di Azure Data Lake Storage Gen2.For more information on other options during storage account [creation, see Quickstart: Create an Azure Data Lake Storage Gen2 storage account.](../storage/blobs/data-lake-storage-quickstart-create-account.md)

![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurare le autorizzazioni per l'identità gestita nell'account Data Lake Storage Gen2Set up permissions for the managed identity on the Data Lake Storage Gen2 account

Assegnare l'identità gestita al ruolo **Proprietario dati BLOB di archiviazione** nell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare l'account di archiviazione, quindi selezionare Controllo di accesso **(IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Per aggiungere un nuovo ruolo, fare clic sul pulsante **: Aggiungi assegnazione ruolo.**
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo **Proprietario dati BLOB di archiviazione.** Selezionare quindi la sottoscrizione a cui sono associati l'identità gestita e l'account di archiviazione. Individuare l'identità gestita assegnata dall'utente creata in precedenza. Infine, selezionare l'identità gestita e verrà elencata in **Membri selezionati**.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selezionare **Salva**. L'identità assegnata dall'utente selezionata è ora elencata sotto il ruolo selezionato.
1. Al termine della configurazione iniziale, è possibile creare un cluster tramite il portale. Il cluster deve trovarsi nella stessa area di Azure dell'account di archiviazione. Nella scheda **Archiviazione** del menu di creazione cluster, selezionare le opzioni seguenti:

    * Per **Tipo di archiviazione primaria**selezionare Archiviazione dati di Azure **2**.
    * In **Account di archiviazione primario**cercare e selezionare l'account di archiviazione Data Lake Storage Gen2 appena creato.

    * In **Identità**selezionare l'identità gestita assegnata dall'utente appena creata.

        ![Impostazioni di archiviazione per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Per aggiungere un account Data Lake Storage Gen2 secondario, a livello di account di archiviazione è sufficiente assegnare l'identità gestita creata in precedenza al nuovo account di archiviazione Data Lake Storage Gen2 che si vuole aggiungere. Tenere presente che l'aggiunta di un account Data Lake Storage Gen2 secondario tramite il pannello "Account di archiviazione aggiuntivi" in HDInsight non è supportata.
    > * È possibile abilitare RA-GRS o RA-'RS nell'account di archiviazione di Azure utilizzato da HDInsight.You can enable RA-GRS or RA-'RS on the Azure storage account that HDInsight uses. Tuttavia, la creazione di un cluster per l'endpoint secondario RA-GRS o RA-'RS non è supportata.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Creare un cluster con Data Lake Storage Gen2 tramite l'interfaccia della riga di comando di AzureCreate a cluster with Data Lake Storage Gen2 through the Azure CLI

È possibile [scaricare un file modello](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) di esempio e scaricare un file di parametri di [esempio.](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json) Prima di usare il modello e il frammento di codice dell'interfaccia della riga di comando di Azure seguente, sostituire i segnaposto seguenti con i valori corretti:

| Segnaposto | Descrizione |
|---|---|
| `<SUBSCRIPTION_ID>` | ID della sottoscrizione di Azure |
| `<RESOURCEGROUPNAME>` | Gruppo di risorse in cui si vuole creare il nuovo cluster e l'account di archiviazione. |
| `<MANAGEDIDENTITYNAME>` | Nome dell'identità gestita a cui verranno assegnate le autorizzazioni per l'account Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Il nuovo account Azure Data Lake Storage Gen2 che verrà creato. |
| `<CLUSTERNAME>` | Il nome del cluster HDInsight. |
| `<PASSWORD>` | La password scelta per l'accesso al cluster tramite SSH e il dashboard di Ambari. |

Il frammento di codice riportato di seguito esegue i passaggi iniziali seguenti:The code snippet below does the following initial steps:

1. Esegue l'accesso all'account Azure.
1. Imposta la sottoscrizione attiva in cui verranno eseguite le operazioni di creazione.
1. Crea un nuovo gruppo di risorse per le nuove attività di distribuzione.
1. Crea un'identità gestita assegnata dall'utente.
1. Aggiunge un'estensione all'interfaccia della riga di comando di Azure per usare le funzionalità per Data Lake Storage Gen2.
1. Crea un nuovo account Data Lake `--hierarchical-namespace true` Storage Gen2 utilizzando il flag.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Accedere quindi al portale. Aggiungere la nuova identità gestita assegnata dall'utente al ruolo **Collaboratore dati BLOB** di archiviazione nell'account di archiviazione, come descritto nel passaggio 3 in [Uso del portale](hdinsight-hadoop-use-data-lake-storage-gen2.md)di Azure .

Dopo aver assegnato il ruolo per l'identità gestita assegnata dall'utente, distribuire il modello utilizzando il frammento di codice seguente.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Creare un cluster con Data Lake Storage Gen2 tramite Azure PowerShellCreate a cluster with Data Lake Storage Gen2 through Azure PowerShell

L'uso di PowerShell per creare un cluster HDInsight con Azure Data Lake Storage Gen2 non è attualmente supportato.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controllo degli accessi per Data Lake Storage Gen2 in HDInsightAccess control for Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quali tipi di autorizzazioni sono supportati da Data Lake Storage Gen2?

Data Lake Storage Gen2 utilizza un modello di controllo di accesso che supporta sia il controllo degli accessi in base al ruolo che gli elenchi di controllo di accesso (ACL) di tipo POSIX. Data Lake Storage Gen1 supporta gli elenchi di controllo di accesso solo per il controllo dell'accesso ai dati.

Il controllo degli accessi in base al ruolo usa le assegnazioni di ruolo per applicare in modo efficace set di autorizzazioni a utenti, gruppi ed entità servizio per le risorse di Azure.RBAC uses role assignments to effectively apply sets of permissions to users, groups, and service principals for Azure resources. Tali risorse di Azure sono in genere vincolate alle risorse di primo livello (ad esempio gli account di archiviazione di Azure). Per Archiviazione di Azure e anche Data Lake Storage Gen2, questo meccanismo è stato esteso alla risorsa del file system.

 Per altre informazioni sulle autorizzazioni per i file con il controllo degli accessi in base al ruolo, vedere Controllo degli accessi in base al ruolo di Azure.For more information about file permissions with RBAC, see [Azure role-based access control (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Per ulteriori informazioni sulle autorizzazioni per i file con ACL, vedere Elenchi di controllo di [accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Come posso controllare l'accesso ai miei dati in Data Lake Storage Gen2?

La capacità del cluster HDInsight di accedere ai file in Data Lake Storage Gen2 è controllata tramite identità gestite. Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure.A managed identity is an identity registered in Azure Active Directory (Azure AD) whose credentials are managed by Azure. Con le identità gestite non è necessario registrare le entità servizio in Azure AD o gestire le credenziali, ad esempio i certificati.

I servizi di Azure hanno due tipi di identità gestite: assegnate al sistema e assegnate dall'utente. HDInsight usa le identità gestite assegnate dall'utente per accedere a Data Lake Storage Gen2. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure.

Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. Per altre informazioni sulle identità gestite, vedere [Come funzionano le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)di Azure? .

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Come si impostano le autorizzazioni per gli utenti di Azure AD per eseguire query sui dati in Data Lake Storage Gen2 usando Hive o altri servizi?

Per impostare le autorizzazioni per gli utenti per eseguire query sui dati, usare i gruppi di sicurezza di Azure AD come entità assegnata negli ACL. Non assegnare direttamente le autorizzazioni di accesso ai file a singoli utenti o entità servizio. Quando si usano i gruppi di sicurezza di Azure AD per controllare il flusso delle autorizzazioni, è possibile aggiungere e rimuovere utenti o entità servizio senza riapplicare gli ACL a un'intera struttura di directory. È sufficiente aggiungere o rimuovere gli utenti dal gruppo di sicurezza di Azure AD appropriato. Gli ACL non vengono ereditati, pertanto la riapplicazione degli ACL richiede l'aggiornamento dell'ACL su ogni file e sottodirectory.

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

Esistono diversi modi per accedere ai file in Data Lake Storage Gen2 da un cluster HDInsight.There are several ways you can access the files in Data Lake Storage Gen2 from an HDInsight cluster.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Uso del formato con percorso abbreviato**. Con questo approccio, si sostituisce il percorso fino alla radice del cluster con:With this approach, you replace the path up to the cluster root with:

    ```
    abfs:///<file.path>/
    ```

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono utilizzati tutti e tre gli schemi URI. Sostituire `CONTAINERNAME` `STORAGEACCOUNT` e con i valori pertinenti

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi hdfs

1. Creare un file nell'archivio locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archiviazione cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dall'archiviazione locale all'archiviazione cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archiviazione cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Creazione di una tabella HiveCreating a Hive table

Vengono visualizzate tre posizioni di file a scopo illustrativo. Per l'esecuzione effettiva, `LOCATION` utilizzare solo una delle voci.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Passaggi successivi

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integrazione di Azure HDInsight con Data Lake Storage Gen2 (anteprima) - Aggiornamento di ACL e sicurezza)
* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Esercitazione: Estrarre, trasformare e caricare dati usando Query interattiva in Azure HDInsightTutorial: Extract, transform, and load data using Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
