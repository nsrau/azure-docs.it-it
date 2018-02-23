---
title: Gestire cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure | Microsoft Docs
description: Informazioni su come amministrare il servizio HDInsight. Creare un cluster HDInsight, aprire la console interattiva JavaScript e aprire la console dei comandi di Hadoop.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 77c47809a01e9ff2bb851163f9adf987738f98f6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure

Il [portale di Azure][azure-portal] consente di creare cluster Hadoop basati su Windows in Azure HDInsight, modificare la password utente di Hadoop e abilitare RDP (Remote Desktop Protocol) per accedere alla console dei comandi di Hadoop nel cluster.

Le informazioni contenute in questo articolo si applicano solo ai cluster HDInsight basati su Windows. Per informazioni sulla gestione dei cluster basati su Linux, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Account di archiviazione di Azure** : cluster HDInsight usano un contenitore di archiviazione BLOB di Azure come file system predefinito. Per altre informazioni sull'esperienza lineare offerta dall'archivio BLOB con cluster HDInsight, vedere [Usare l'archivio BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Aprire il portale
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Dopo avere aperto il portale, è possibile:

   * Scegliere **Crea una risorsa** dal menu di sinistra per creare un nuovo cluster:

       ![Pulsante Nuovo cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Scegliere **Cluster HDInsight** dal menu di sinistra.

       ![Pulsante Cluster HDInsight del portale di Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Se **HDInsight** non è visualizzato nel menu a sinistra, fare clic su **Sfoglia**.

     ![Pulsante Sfoglia del portale di Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Creare i cluster
Per le istruzioni di creazione con il Portale, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md). Per personalizzare HDInsight, è possibile usare una delle opzioni seguenti:

* Usare azioni script per eseguire script personalizzati in grado di personalizzare un cluster per modificare la configurazione del cluster o installare componenti personalizzati, ad esempio Giraph o Solr. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).
* Usare i parametri di personalizzazione del cluster in HDInsight .NET SDK o di Azure PowerShell durante la creazione del cluster. Le modifiche alla configurazione vengono quindi mantenute per tutta la durata del cluster e non vengono interessate dalla ricreazione delle immagini dei nodi del cluster che la piattaforma Azure esegue periodicamente per la manutenzione. Per altre informazioni sull'uso dei parametri di personalizzazione dei cluster, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR. Tali file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).
  >
  > Cascading non è supportato da HDInsight, pertanto in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).
  >
  >

L'installazione di software personalizzato nel cluster tramite Connessione Desktop remoto non è supportata. È consigliabile evitare di archiviare file nelle unità del nodo head in quanto andranno perse qualora fosse necessario ricreare i cluster. È consigliabile archiviare i file nell'archivio BLOB di Azure. L'archivio BLOB è persistente.

## <a name="list-and-show-clusters"></a>Elencare e visualizzare i cluster
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Scegliere **Cluster HDInsight** dal menu di sinistra.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare il filtro nella parte superiore della pagina.
4. Fare doppio clic su un cluster nell'elenco per visualizzare i dettagli.

    **Menu e informazioni di base**:

    ![Informazioni di base sul cluster HDInsight del Portale di Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Per personalizzare il menu fare clic con il pulsante destro del mouse su un punto qualsiasi del menu e scegliere **Personalizza**.
   * **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello **Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.
   * **Dashboard**, **Dashboard cluster** e **URL: sono modi per accedere al dashboard del cluster, che per i cluster basati su Linux è Ambari Web. -**Secure Shell**: mostra le istruzioni per la connessione al cluster tramite connessione Secure Shell (SSH).
   * **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
   * **Elimina**: elimina il cluster.
   * **Avvio rapido**: visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
   * **Utenti: consente di impostare le autorizzazioni per la *gestione del portale* di questo cluster per altri utenti nella sottoscrizione Azure.

     > [!IMPORTANT]
     > Questo influisce *solo* sull'accesso e sulle autorizzazioni per tale cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.
     >
     >
   * **Tag**: consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata **progetto**e usare un valore comune per tutti i servizi associati a un progetto specifico.
   * **Visualizzazioni di Ambari**: collegamenti ad Ambari Web.

     > [!IMPORTANT]
     > Per gestire i servizi forniti dal cluster HDInsight, è necessario utilizzare  Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Utilizzo**:

     ![Utilizzo dei cluster HDInsight del portale di Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Fare clic su **Impostazioni**.

    ![Utilizzo dei cluster HDInsight del portale di Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Proprietà**: visualizza le proprietà del cluster.
   * **Identità AAD cluster**:
   * **Chiavi di archiviazione di Azure**: visualizza l'account di archiviazione predefinito con la chiave. L'account di archiviazione viene configurato durante il processo di creazione dei cluster.
   * **Account di accesso del cluster**: modifica il nome utente e la password HTTP del cluster.
   * **Metastore esterni**: visualizza i metastore Hive e Oozie. I metastore possono essere configurati solo durante il processo di creazione dei cluster.
   * **Ridimensiona cluster**: aumenta e diminuisce il numero di nodi di lavoro del cluster.
   * **Desktop remoto**: abilita e disabilita l'accesso desktop remoto (RDP) e configura il nome utente RDP.  Il nome utente RDP deve essere diverso dal nome utente HTTP.
   * **Partner of Record**:

     > [!NOTE]
     > Si tratta di un elenco generico di impostazioni disponibili, non tutte saranno presenti per tutti i tipi di cluster.
     >
     >
6. Fare clic su **Proprietà**:

    Le proprietà elencate sono le seguenti:

   * **Nome host**: nome del cluster.
   * **URL cluster**.
   * **Stato**: include Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Area**: località di Azure. Per un elenco di località di Azure supportate, vedere l'elenco a discesa **Area** in [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data di creazione**.
   * **Sistema operativo**: **Windows** o **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versione**. Vedere [Versioni di HDInsight](hdinsight-component-versioning.md)
   * **Sottoscrizione**: nome della sottoscrizione.
   * **ID sottoscrizione**.
   * **Origine dati primaria**. Account di archiviazione BLOB di Azure usato come file system Hadoop predefinito.
   * **Piano tariffario nodi del ruolo di lavoro**.
   * **Piano tariffario per il nodo head**.

## <a name="delete-clusters"></a>Eliminare cluster
L'eliminazione di un cluster non determinerà l'eliminazione dell'account di archiviazione predefinito o di qualsiasi account di archiviazione collegato. È possibile ricreare il cluster usando gli stessi account di archiviazione e gli stessi metastore.

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Scegliere **Elimina** dal menu in alto e quindi seguire le istruzioni.

Vedere anche [Sospendere/Arrestare i cluster](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster.

> [!NOTE]
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).
>
>

Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Hadoop

    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.
* hbase

    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Per altre informazioni sull'uso della shell HBase, vedere []
* Storm

    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

    A tale scopo, è possibile scegliere tra due opzioni:

  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)

    Per altre informazioni, fare riferimento alla [documentazione su Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

    ![Ribilanciamento scala di HDInsight Storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Per ridimensionare i cluster**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu in alto, quindi fare clic su **Ridimensiona cluster**.
4. Immettere il **numero di nodi del ruolo di lavoro**. Il limite al numero dei nodi del cluster varia tra le diverse sottoscrizioni di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.  Le informazioni sui costi rifletteranno le modifiche apportate al numero di nodi.

    ![Ridimensionamento HDInsight Hadoop HBase Storm Spark](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Sospendere/Arrestare i cluster
La maggior parte dei processi Hadoop sono processi batch che vengono eseguito solo occasionalmente. La maggior parte dei cluster Hadoop non viene usata per l'elaborazione per lunghi periodi di tempo. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato.
Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Questo processo può essere programmato in molti modi:

* Usare Data factory di Azure. Per i servizi collegati di HDInsight su richiesta e autodefiniti, vedere [Servizio collegato Azure HDInsight](../data-factory/compute-linked-services.md) e [Trasformare e analizzare tramite Azure Data Factory](../data-factory/transform-data.md).
* Usare Azure PowerShell.  Vedere [Analizzare i dati relativi ai ritardi dei voli](hdinsight-analyze-flight-delay-data.md).
* Usare l'interfaccia della riga di comando di Azure. Vedere [Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md).
* Usare HDInsight .NET SDK. Vedere [Inviare processi di Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [Eliminare cluster](#delete-clusters)

## <a name="change-cluster-username"></a>Modificare il nome utente del cluster
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight viene creato durante il processo di creazione. È anche possibile creare un account utente RDP per accedere al cluster tramite RDP. Vedere la sezione relativa all' [abilitazione di Desktop remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Per modificare il nome utente e la password di un cluster HDInsight**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Scegliere **Impostazioni** dal menu in alto, quindi fare clic su **Account di accesso del cluster**.
4. Se **Account di accesso del cluster** è stato abilitato, è necessario fare clic su **Disabilita** e quindi su **Abilita** prima di poter modificare il nome utente e la password.
5. Modificare i valori in **Cluster Login Name** (Nome utente dell'account di accesso del cluster) e/o **Password dell'account di accesso del cluster**, quindi fare clic su **Salva**.

    ![Modifica di nome utente cluster, password e utente http in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Concedere/Revocare l'accesso
Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso dal portale di Azure.

> [!NOTE]
> La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.
>
>

**Per concedere/revocare l'accesso ai servizi Web HTTP**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Scegliere **Impostazioni** dal menu in alto, quindi fare clic su **Account di accesso del cluster**.
4. Se **Account di accesso del cluster** è stato abilitato, è necessario fare clic su **Disabilita** e quindi su **Abilita** prima di poter modificare il nome utente e la password.
5. Per **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster** immettere (rispettivamente) il nuovo nome utente e la nuova password per il cluster.
6. Fare clic su **SAVE**.

    ![Rimozione complessiva dell'accesso al servizio web http in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito
Ogni cluster HDInsight ha un account di archiviazione predefinito. L'account di archiviazione predefinito e le relative chiavi per un cluster vengono visualizzati in **Impostazioni**/**Proprietà**/**Chiavi di archiviazione di Azure**. Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
In modalità Azure Resource Manager ogni cluster HDInsight viene creato con un gruppo di risorse di Azure. Il gruppo di risorse di Azure a cui appartiene un cluster viene visualizzato in:

* Colonna **Gruppo di risorse** dell'elenco di cluster.
* Riquadro **Informazioni di base** del cluster.  

Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Aprire la console query HDInsight
La console query HDInsight include le funzionalità seguenti:

* **Editor Hive**: interfaccia Web GUI per l'invio di processi Hive.  Vedere [Eseguire query Hive usando Console query](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Portale editor hive in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Cronologia processo**: monitora i processi Hadoop.  

    ![Portale cronologia processi in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Fare clic su **Nome query** per visualizzare i dettagli, incluse le proprietà del processo, **Job Query** (Query processo) e **Output processo. È anche possibile scaricare sia la query che l'output nella workstation.
* **Browser file**: sfoglia l'account di archiviazione predefinito e gli account di archiviazione collegati.

    ![Esplorazione del browser file portale in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Nello screenshot il tipo **<Account>** indica che l'elemento è un account di archiviazione di Azure.  Fare clic sul nome account per cercare i file.
* **Interfaccia utente Hadoop**.

    ![Interfaccia utente Hadoop portale in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Dall'*interfaccia utente di Hadoop*è possibile cercare i file e controllare i log.
* **Interfaccia utente Yarn**.

    ![Interfaccia utente YARN portale in HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Eseguire query Hive
Per eseguire i processi Hive dal portale, fare clic su **Editor Hive** nella console query HDInsight. Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorare i processi
Per monitorare i processi dal portale, fare clic su **Cronologia processo** nella console query HDInsight. Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Ricerca dei file
Per sfogliare i file archiviati nell'account di archiviazione predefinito e negli account di archiviazione collegati, fare clic su **Browser file** nella console query HDInsight. Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

È anche possibile usare l'utilità **Browse the file system** dell'**interfaccia utente di Hadoop** nella console HDInsight.  Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorare l'utilizzo del cluster
La sezione **Utilizzo** del pannello relativo al cluster HDInsight contiene informazioni sul numero di memorie centrali disponibili per la sottoscrizione da usare con HDInsight, il numero di memorie centrali allocate al cluster e il modo in cui vengono allocate per i nodi nel cluster. Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

> [!IMPORTANT]
> Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare  Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Aprire l'interfaccia utente di Hadoop
Per monitorare il cluster, esplorare il file system e controllare i log, fare clic su **Interfaccia utente Hadoop** nella console query HDInsight. Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Aprire l'interfaccia utente di Yarn
Per usare l'interfaccia utente di Yarn, fare clic su **Interfaccia utente di Yarn** nella console query HDInsight. Vedere [Aprire la console query HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Connettersi a cluster con RDP
Le credenziali del cluster fornite durante la creazione consentono di accedere ai servizi nel cluster, ma non al cluster stesso tramite Desktop remoto. È possibile attivare l'accesso Desktop remoto durante o dopo il provisioning di un cluster. Per istruzioni sull'abilitazione di Desktop remoto al momento della creazione, vedere [Creazione del cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

**Per abilitare Desktop remoto**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Scegliere **Impostazioni** dal menu in alto, quindi fare clic su **Desktop remoto**.
4. Immettere **Expires On** (Scadenza), **Nome utente Desktop remoto** e **Password Desktop remoto**, quindi fare clic su **Abilita**.

    ![HDInsight - abilitazione/disabilitazione/configurazione di Desktop remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    La Scadenza predefinita è una settimana.

   > [!NOTE]
   > Per abilitare Desktop remoto in un cluster è anche possibile usare HDInsight .NET SDK. Usare il metodo **EnableRdp** sull'oggetto client HDInsight nel modo seguente: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Analogamente, per disabilitare Desktop remoto nel cluster è possibile usare **client.DisableRdp(clustername, location)**. Per altre informazioni su questi metodi, vedere [Documentazione di riferimento relativa a HDInsight .NET SDK](http://go.microsoft.com/fwlink/?LinkId=529017). È applicabile solo per i cluster HDInsight in esecuzione in Windows.
   >
   >

**Per connettersi a un cluster tramite RDP**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Browse All** (Sfoglia tutto) dal menu di sinistra, fare clic su **Cluster HDInsight** e quindi sul nome del cluster.
3. Scegliere **Impostazioni** dal menu in alto, quindi fare clic su **Desktop remoto**.
4. Fare clic su **Connetti** e seguire le istruzioni. Se Connetti è disabilitato, è necessario prima abilitarlo. Assicurarsi di utilizzare il nome utente e la password del Desktop remoto.  Non è possibile utilizzare le credenziali dell'utente del Cluster.

## <a name="open-hadoop-command-line"></a>Aprire la riga di comando di Hadoop.
Per connettersi al cluster tramite Desktop remoto e usare la riga di comando di Hadoop, è prima necessario aver abilitato l'accesso Desktop remoto al cluster come descritto nella sezione precedente.

**Per aprire la riga di comando di Hadoop**

1. Connettersi al cluster tramite Desktop remoto.
2. Dal desktop fare doppio clic su **Hadoop Command Line**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Per altre informazioni sui comandi Hadoop, vedere la [documentazione di riferimento sui comandi Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Nella schermata precedente il numero di versione di Hadoop è incorporato nel nome della cartella. Il numero di versione cambia in base alla versione dei componenti Hadoop installati nel cluster. È possibile usare le variabili d'ambiente di Hadoop per fare riferimento a tali cartelle. Ad esempio: 

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale, e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Inviare processi Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Riga di comando di Hadoop"
