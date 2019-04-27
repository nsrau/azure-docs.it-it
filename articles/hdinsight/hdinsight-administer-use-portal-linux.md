---
title: Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure
description: Informazioni su come creare e gestire cluster HDInsight tramite il portale di Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 003aeadba1f4683af40f390d40dd3bbe32e02a83
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096359"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Il [portale di Azure][azure-portal] consente di gestire cluster [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight. Usare il selettore di schede precedente per informazioni sulla gestione di cluster Hadoop in HDInsight con altri strumenti.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Hadoop esistente in HDInsight.  Vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

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

| Elemento| DESCRIZIONE |
|---|---|
|Spostamento|sposta il cluster in una sottoscrizione o un gruppo di risorse diverso.|
|Delete|elimina il cluster. |
|Aggiorna|aggiorna la visualizzazione.|

**Menu a sinistra:**  
  - **Menu in alto a sinistra**

    | Elemento| DESCRIZIONE |
    |---|---|
    |Panoramica|presenta informazioni generali relative al cluster.|
    |Log attività|visualizza i log attività e consente di eseguire query.|
    |Controllo di accesso (IAM)|usa le assegnazioni di ruolo.  Vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).|
    |Tag|consente di impostare coppie chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, è possibile creare una chiave denominata **progetto**e usare un valore comune per tutti i servizi associati a un progetto specifico.|
    |Diagnostica e risoluzione dei problemi|visualizza informazioni sulla risoluzione dei problemi.|
    |Guida introduttiva|visualizza informazioni utili per iniziare a usare HDInsight.|
    |Strumenti|informazioni della Guida per gli strumenti correlati a HDInsight.|

  - **Menu Impostazioni**  

    | Elemento| DESCRIZIONE |
    |---|---|
    |Dimensioni del cluster|consente di verificare, aumentare e ridurre il numero di nodi di ruolo di lavoro del cluster. Vedere [Ridimensionare i cluster](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limiti di quota|visualizza i core usati e disponibili per la sottoscrizione.|
    |SSH + Account di accesso del cluster|visualizza le istruzioni per la connessione al cluster tramite la connessione Secure Shell (SSH). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|consente di configurare l'accesso a Data Lake Storage Gen1.  Vedere [Avvio rapido: Impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)|
    |Account di archiviazione|visualizza gli account di archiviazione e le chiavi. Gli account di archiviazione vengono configurati durante il processo di creazione dei cluster.|
    |APPLICAZIONI|consente di aggiungere/rimuovere applicazioni HDInsight.  Vedere [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).|
    |Azioni script|esegue script Bash nel cluster. Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).|
    |Metastore esterni|visualizza i metastore [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). I metastore possono essere configurati solo durante il processo di creazione dei cluster.|
    |Partner HDInsight|consente di aggiungere/rimuovere il partner HDInsight corrente.|
    |Properties|visualizza le [proprietà del cluster](#properties).|
    |Blocchi|aggiunge un blocco per impedire la modifica o l'eliminazione del cluster.|
    |Script di automazione|visualizza ed esporta il modello di Azure Resource Manager per il cluster. Attualmente è possibile esportare solo l'account di archiviazione di Azure dipendente. Vedere [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu Monitoraggio**

    | Elemento| DESCRIZIONE |
    |---|---|
    |Avvisi|gestisce gli avvisi e le azioni.|
    |Metriche|Monitorare le metriche del cluster nei log di monitoraggio di Azure.|
    |Impostazioni di diagnostica|impostazioni per la posizione di archiviazione delle metriche di diagnostica.|
    |Operations Management Suite|Monitoraggio del cluster nei log di Azure Operations Management Suite (OMS) e monitoraggio di Azure.|

  - **Menu Supporto e risoluzione dei problemi**

    | Elemento| DESCRIZIONE |
    |---|---|
    |Resource Health|vedere [Panoramica di Integrità risorse di Azure](../service-health/resource-health-overview.md).|
    |Nuova richiesta di supporto|consente di creare un ticket di supporto con il supporto Microsoft.|

## <a name="properties"></a> Proprietà del cluster

Dalla [home page del cluster](#homePage), selezionare **Proprietà** in **Impostazioni**.

|Elemento | DESCRIZIONE |
|---|---|
|Nome host|Nome del cluster.|
|URL del cluster|URL per l'interfaccia Web Ambari.|
|Secure shell (SSH)|nome utente e nome host da usare per l'accesso al cluster tramite SSH.|
|Stato|uno dei valori seguenti: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued o ClusterCustomization.|
|Region|località di Azure. Per informazioni sulle località di Azure supportate, vedere l'elenco a discesa **Area** in [Prezzi per HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|Dati creati|data di distribuzione del cluster.|
|Sistema operativo|può essere **Windows** o **Linux**.|
|Type|Hadoop, HBase, Storm, Spark.|
|Version|Vedere [Versioni di HDInsight](hdinsight-component-versioning.md).|
|Sottoscrizione|nome della sottoscrizione.|
|Origine dati predefinita|file system predefinito del cluster.|
|Dimensioni di nodi di lavoro|dimensioni selezionate dei nodi di lavoro nella macchina virtuale.|
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

> [!NOTE]  
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere Elencare e visualizzare i cluster.

Dalla [home page del cluster](#homePage):

1. In **Impostazioni** selezionare **Dimensione del cluster**.
2. Immettere il **numero di nodi di ruolo di lavoro** nella casella di testo per valori numerici. Il limite relativo al numero di nodi del cluster varia in base alla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.  Le informazioni sui costi riflettono le modifiche apportate al numero di nodi.
3. Selezionare **Salva**.

    ![Scalabilità di HDInsight Hadoop, HBase, Storm, Spark](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Apache Hadoop

    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. A causa di questo comportamento, tutti i processi in esecuzione e in sospeso avranno esito negativo al completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.
* Apache HBase

    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Per altre informazioni sull'uso della shell HBase, vedere [Iniziare a usare un esempio di Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

    A tale scopo, è possibile scegliere tra due opzioni:

  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)

    Per altri dettagli, vedere la [documentazione di Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

    ![Ribilanciamento di HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Ecco un esempio di comando dell'interfaccia della riga di comando per bilanciare di nuovo la topologia di Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Sospendere/Arrestare i cluster

La maggior parte dei processi Hadoop sono processi batch che vengono eseguito solo occasionalmente. La maggior parte dei cluster Hadoop non viene usata per l'elaborazione per lunghi periodi di tempo. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato.
Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Questo processo può essere programmato in molti modi:

* Usare Data factory di Azure. Per la creazione di servizi collegati di HDInsight, vedere [Creare cluster Apache Hadoop on demand basati su Linux in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md).
* Usare Azure PowerShell.  Vedere [Analizzare i dati relativi ai ritardi dei voli](hdinsight-analyze-flight-delay-data-linux.md).
* Usare l'interfaccia della riga di comando classica di Azure. Vedere [Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando classica di Azure](hdinsight-administer-use-command-line.md).
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
2. Immettere il nome utente e la password del cluster.  Il nome utente predefinito del cluster è _admin_. Ecco che aspetto ha l'interfaccia utente Web di Ambari:

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
4. Nella pannello **Azioni script** selezionare **Invia nuova**. 
5. Dal pannello **Invia azione script** immettere le informazioni seguenti:

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

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso tramite l'[interfaccia della riga di comando classica di Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Trovare l'ID sottoscrizione
Ogni cluster è associato a una sottoscrizione di Azure.  L'ID della sottoscrizione di Azure è visibile dalla [home page del cluster](#homePage).

## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
In modalità Azure Resource Manager ogni cluster HDInsight viene creato con un gruppo di Azure Resource Manager. Il gruppo di Resource Manager è visibile dalla [home page del cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Trovare gli account di archiviazione
Per archiviare i dati, i cluster HDInsight usano un account di archiviazione di Azure o Azure Data Lake Storage. Ogni cluster HDInsight può avere un account di archiviazione predefinito e un numero di account di archiviazione collegati. Per elencare gli account di archiviazione, dalla [home page del cluster](#homePage) selezionare **Account di archiviazione** in **Impostazioni**.


## <a name="monitor-jobs"></a>Monitorare i processi
Vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Monitorare l'utilizzo del cluster
La sezione **Utilizzo** del pannello relativo al cluster HDInsight contiene informazioni sul numero di memorie centrali disponibili per la sottoscrizione da usare con HDInsight, il numero di memorie centrali allocate al cluster e il modo in cui vengono allocate per i nodi nel cluster. Vedere Elencare e visualizzare i cluster.

> [!IMPORTANT]  
> Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare  Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster

* [Usare Apache Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si sono apprese alcune funzioni amministrative di base. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l'interfaccia della riga di comando classica di Azure](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Uso dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Uso dell'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usare Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Apache Pig in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare Apache Sqoop in HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Versione di Apache Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Riga di comando di Hadoop"
