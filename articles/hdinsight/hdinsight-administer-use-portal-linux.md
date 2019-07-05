---
title: Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure
description: Informazioni su come creare e gestire cluster HDInsight tramite il portale di Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: c745fceca5efa66b1b23661001d93ddb287fe37b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460638"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Usando il [portale di Azure](https://portal.azure.com), è possibile gestire [Apache Hadoop](https://hadoop.apache.org/) cluster in Azure HDInsight. Usare il selettore di schede precedente per informazioni sulla gestione di cluster Hadoop in HDInsight con altri strumenti.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Hadoop esistente in HDInsight.  Vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introduzione
Accedere a [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a> Elencare e visualizzare i cluster
I cluster esistenti vengono visualizzati nella pagina **Cluster HDInsight**.  Dal portale:
1. Selezionare **Tutti i servizi** dal menu a sinistra.
2. Selezionare **Cluster HDInsight** in **ANALYTICS**.

## <a name="homePage"></a> Home page del cluster 
Selezionare il nome del cluster dal [ **cluster HDInsight** ](#showClusters) pagina.  Verrà aperta la visualizzazione **Panoramica** con un aspetto simile all'immagine seguente:

![Informazioni di base sul cluster HDInsight del Portale di Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu in alto:**  

| Elemento| Descrizione |
|---|---|
|Spostamento|sposta il cluster in una sottoscrizione o un gruppo di risorse diverso.|
|Delete|elimina il cluster. |
|Aggiorna|aggiorna la visualizzazione.|

**Menu a sinistra:**  
  - **Menu in alto a sinistra**

    | Elemento| Descrizione |
    |---|---|
    |Panoramica|presenta informazioni generali relative al cluster.|
    |Log attività|visualizza i log attività e consente di eseguire query.|
    |Controllo di accesso (IAM)|usa le assegnazioni di ruolo.  Vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).|
    |`Tags`|consente di impostare coppie chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, è possibile creare una chiave denominata **progetto**e usare un valore comune per tutti i servizi associati a un progetto specifico.|
    |Diagnostica e risoluzione dei problemi|visualizza informazioni sulla risoluzione dei problemi.|
    |Avvio rapido|visualizza informazioni utili per iniziare a usare HDInsight.|
    |Strumenti|informazioni della Guida per gli strumenti correlati a HDInsight.|

  - **Menu Impostazioni**  

    | Elemento| Descrizione |
    |---|---|
    |Dimensioni del cluster|consente di verificare, aumentare e ridurre il numero di nodi di ruolo di lavoro del cluster. Vedere [Ridimensionare i cluster](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limiti di quota|visualizza i core usati e disponibili per la sottoscrizione.|
    |SSH + account di accesso del Cluster|visualizza le istruzioni per la connessione al cluster tramite la connessione Secure Shell (SSH). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|consente di configurare l'accesso a Data Lake Storage Gen1.  Vedere [Avvio rapido: Impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)|
    |Account di archiviazione|visualizza gli account di archiviazione e le chiavi. Gli account di archiviazione vengono configurati durante il processo di creazione dei cluster.|
    |APPLICAZIONI|consente di aggiungere/rimuovere applicazioni HDInsight.  Vedere [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).|
    |Azioni script|esegue script Bash nel cluster. Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).|
    |Metastore esterni|visualizza i metastore [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). I metastore possono essere configurati solo durante il processo di creazione dei cluster.|
    |Partner HDInsight|consente di aggiungere/rimuovere il partner HDInsight corrente.|
    |Properties|visualizza le [proprietà del cluster](#properties).|
    |Blocchi|aggiunge un blocco per impedire la modifica o l'eliminazione del cluster.|
    |Esportare il modello|visualizza ed esporta il modello di Azure Resource Manager per il cluster. Attualmente è possibile esportare solo l'account di archiviazione di Azure dipendente. Vedere [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu Monitoraggio**

    | Elemento| Descrizione |
    |---|---|
    |Avvisi|gestisce gli avvisi e le azioni.|
    |metrics|Monitorare le metriche del cluster nei log di monitoraggio di Azure.|
    |Impostazioni di diagnostica|impostazioni per la posizione di archiviazione delle metriche di diagnostica.|
    |Operations Management Suite|Monitoraggio del cluster nei log di Azure Operations Management Suite (OMS) e monitoraggio di Azure.|

  - **Menu Supporto e risoluzione dei problemi**

    | Elemento| Descrizione |
    |---|---|
    |Resource Health|vedere [Panoramica di Integrità risorse di Azure](../service-health/resource-health-overview.md).|
    |Nuova richiesta di supporto|consente di creare un ticket di supporto con il supporto Microsoft.|

## <a name="properties"></a> Proprietà del cluster

Dalla [home page del cluster](#homePage), selezionare **Proprietà** in **Impostazioni**.

|Elemento | Descrizione |
|---|---|
|HOSTNAME|Nome del cluster.|
|URL DEL CLUSTER|URL per l'interfaccia Web Ambari.|
|Endpoint privati|L'endpoint privato per il cluster.|
|Secure shell (SSH)|nome utente e nome host da usare per l'accesso al cluster tramite SSH.|
|STATO|uno dei valori seguenti: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued o ClusterCustomization.|
|AREA|località di Azure. Per informazioni sulle località di Azure supportate, vedere l'elenco a discesa **Area** in [Prezzi per HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA CREATA|data di distribuzione del cluster.|
|SISTEMA OPERATIVO|può essere **Windows** o **Linux**.|
|TIPO|Hadoop, HBase, Storm, Spark.|
|Version|Vedere [Versioni di HDInsight](hdinsight-component-versioning.md).|
|SOTTOSCRIZIONE|nome della sottoscrizione.|
|ORIGINE DATI PREDEFINITA|file system predefinito del cluster.|
|Dimensioni dei nodi di lavoro|dimensioni selezionate dei nodi di lavoro nella macchina virtuale.|
|Dimensioni nodo head|dimensioni selezionate dei nodi head nella macchina virtuale.|
|Rete virtuale|Il nome della rete virtuale, che viene distribuito il cluster, se è stato selezionato uno in fase di distribuzione.|

## <a name="move-clusters"></a>Spostare cluster

È possibile spostare un cluster HDInsight in un altro gruppo di risorse di Azure o in un'altra sottoscrizione.

Dalla [home page del cluster](#homePage):

1. Selezionare **Sposta** dal menu in alto.
2. Selezionare **Sposta in un altro gruppo di risorse** o **Sposta in un'altra sottoscrizione**.
3. Seguire le istruzioni della nuova pagina.

## <a name="delete-clusters"></a>Eliminare cluster
L'eliminazione di un cluster non determina l'eliminazione dell'account di archiviazione predefinito né di eventuali account di archiviazione collegati. È possibile ricreare il cluster usando gli stessi account di archiviazione e gli stessi metastore. È consigliabile usare un nuovo contenitore BLOB predefinito quando si ricrea il cluster.

Dalla [home page del cluster](#homePage):

1. Selezionare **Elimina** dal menu in alto.
2. Seguire le istruzioni della nuova pagina.

Vedere anche [Sospendere/Arrestare i cluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Aggiungere altri account di archiviazione

Dopo aver creato un cluster, è possibile aggiungere altri account di archiviazione di Azure e Azure Data Lake Storage. Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Ridimensionare i cluster

La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster Azure HDInsight senza dover ricreare il cluster.

Visualizzare [i cluster HDInsight scalabilità](./hdinsight-scaling-best-practices.md) per informazioni complete.

## <a name="pauseshut-down-clusters"></a>Sospendere/Arrestare i cluster

La maggior parte dei processi Hadoop sono processi batch che vengono eseguito solo occasionalmente. La maggior parte dei cluster Hadoop non viene usata per l'elaborazione per lunghi periodi di tempo. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato.
Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Questo processo può essere programmato in molti modi:

* Usare Data factory di Azure. Per la creazione di servizi collegati di HDInsight, vedere [Creare cluster Apache Hadoop on demand basati su Linux in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md).
* Usare Azure PowerShell.  Vedere [Analizzare i dati relativi ai ritardi dei voli](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
* Usare l'interfaccia della riga di comando di Azure. Visualizzare [gestire Azure HDInsight cluster usando Azure CLI](hdinsight-administer-use-command-line.md).
* Usare HDInsight .NET SDK. Vedere [Inviare processi Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [Eliminare cluster](#delete-clusters)

## <a name="upgrade-clusters"></a>Aggiornare i cluster

Vedere [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Aprire l'interfaccia utente Web di Apache Ambari

Ambari offre un'interfaccia utente Web di gestione di Hadoop intuitiva e semplice da usare supportata dalle API RESTful. Con Ambari agli amministratori di sistema possono gestire e monitorare i cluster Hadoop.

Dalla [home page del cluster](#homePage):

1. Selezionare **Dashboard cluster**.

    ![Menu del cluster Hadoop di HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selezionare **Ambari home** dalla nuova pagina.
1. Immettere il nome utente e la password del cluster.  Il nome utente predefinito del cluster è _admin_.

Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Modificare le password
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight (account utente HTTP) e l'account utente SSH vengono creati durante il processo di creazione. È possibile usare il portale per modificare la password dell'account utente del cluster e azioni script per modificare l'account utente SSH.

### <a name="change-the-cluster-user-password"></a>Modificare la password utente del cluster

> [!NOTE]  
> Se si modifica la password utente del cluster (amministratore), le azioni script eseguite sul cluster potrebbero avere esito negativo. Se sono presenti azioni script persistenti che hanno come destinazione nodi di lavoro, questi script potrebbero avere esito negativo se si aggiungono nodi al cluster tramite operazioni di ridimensionamento. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

Dalla [home page del cluster](#homePage):
1. Selezionare **SSH + Account di accesso del cluster** in **Impostazioni**.
2. Selezionare **Reimposta credenziali**.
3. Immettere e confermare una nuova password nelle caselle di testo.
4. Selezionare **OK**.

La password viene modificata in tutti i nodi del cluster.

### <a name="change-the-ssh-user-password"></a>Modificare la password utente SSH
1. Usando un editor di testo, salvare il testo seguente come file denominato **changepassword.sh**.

    > [!IMPORTANT]  
    > È necessario usare un editor che prevede LF come terminazione di riga. Se l'editor usa CRLF, lo script non funziona.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Caricare il file in un percorso di archiviazione a cui è possibile accedere da HDInsight con un indirizzo HTTP o HTTPS, ad esempio in un archivio di file pubblico come OneDrive o l'archiviazione BLOB di Azure. Salvare l'URI (indirizzo HTTP o HTTPS) nel file, perché sarà necessario nel passaggio successivo.
3. Dal [pagina cluster casa](#homePage), selezionare **azioni Script** sotto **impostazioni**.
4. Dal **azioni Script** pagina, selezionare **Invia nuova**.
5. Dal **invia azione script** pagina, immettere le informazioni seguenti:

   | Campo | Value |
   | --- | --- |
   | Tipo di script | Selezionare **- Custom** dall'elenco a discesa.|
   | NOME |"Change ssh password" |
   | URI script Bash |L'URI per il file changepassword.sh |
   | Tipi di nodo: (Head, Worker, Nimbus, Supervisor, Zookeeper e così via) |✓ per tutti i tipi di nodo elencati |
   | Parametri |Immettere il nome utente SSH e la nuova password. Deve essere presente uno spazio tra il nome utente e la password. |
   | Salvare questa azione script... |Lasciare questo campo vuoto. |

6. Selezionare **Crea** per applicare lo script. Al termine dell'esecuzione dello script, è possibile connettersi al cluster usando SSH con la nuova password.

## <a name="grantrevoke-access"></a>Concedere/Revocare l'accesso
Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso tramite [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Trovare l'ID sottoscrizione
Ogni cluster è associato a una sottoscrizione di Azure.  L'ID della sottoscrizione di Azure è visibile dalla [home page del cluster](#homePage).

## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
In modalità Azure Resource Manager ogni cluster HDInsight viene creato con un gruppo di Azure Resource Manager. Il gruppo di Resource Manager è visibile dalla [home page del cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Trovare gli account di archiviazione
Per archiviare i dati, i cluster HDInsight usano un account di archiviazione di Azure o Azure Data Lake Storage. Ogni cluster HDInsight può avere un account di archiviazione predefinito e un numero di account di archiviazione collegati. Per elencare gli account di archiviazione, dalla [home page del cluster](#homePage) selezionare **Account di archiviazione** in **Impostazioni**.

## <a name="monitor-jobs"></a>Monitorare i processi
Vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Dimensioni del cluster

Il **dimensioni del Cluster** dal riquadro le [cluster casa pagina](#homePage) Visualizza il numero di core allocati al cluster e il modo in cui vengono allocate per i nodi nel cluster.

> [!IMPORTANT]  
> Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare  Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster

* [Usare Apache Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si sono apprese alcune funzioni amministrative di base. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Uso dell'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usare Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Apache Sqoop in HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Usare le funzioni definite dall'utente di Python con Apache Hive e Apache Pig in HDInsight](hadoop/python-udf-hdinsight.md)
* [Versione di Apache Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)