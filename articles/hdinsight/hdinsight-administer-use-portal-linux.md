---
title: Gestire cluster Hadoop in HDInsight tramite il portale di Azure | Microsoft Docs
description: Informazioni su come creare e gestire cluster HDInsight tramite il portale di Azure.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jgao
ms.openlocfilehash: 347af14d342751fd9d03cd5d0e9cedf05f91a2e1
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire cluster Hadoop in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Il [portale di Azure][azure-portal] consente di gestire cluster Hadoop in Azure HDInsight. Usare il selettore di schede precedente per informazioni sulla gestione di cluster Hadoop in HDInsight con altri strumenti.

**Prerequisito**

Per seguire la procedura descritta in questo articolo, è necessaria una **sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Aprire il portale di Azure
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Dopo avere aperto il portale, è possibile:

   * Scegliere **Crea una risorsa** dal menu di sinistra per creare un nuovo cluster:

       ![Pulsante Nuovo cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Immettere **HDInsight** in **Cerca nel Marketplace**, fare clic su **HDInsight** e quindi su **Crea**.

   * Fare clic su **Cluster HDInsight** nel menu a sinistra per visualizzare un elenco dei cluster esistenti:

       ![Pulsante Cluster HDInsight del portale di Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Se non viene visualizzato il pulsante **Cluster HDInsight**, fare clic su **Cluster HDInsight** nella sezione **Intelligence e analisi**.


## <a name="create-clusters"></a>Creare i cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versioni di Hadoop disponibili in Azure HDInsight](hdinsight-component-versioning.md). Per informazioni generali sulla creazione dei cluster, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso

Quando si crea un cluster HDInsight, è necessario specificare una sottoscrizione di Azure. È possibile creare il cluster in un nuovo gruppo di risorse di Azure o in un gruppo di risorse esistente. È possibile usare la procedura seguente per verificare le autorizzazioni per la creazione di cluster HDInsight:

- Per creare un nuovo gruppo di risorse:

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Fare clic su **Sottoscrizione** dal menu a sinistra. Viene visualizzata un'icona gialla a forma di chiave. Verrà visualizzato un elenco di sottoscrizioni.
    3. Fare clic sulla sottoscrizione che verrà usate per creare l cluster. 
    4. Fare clic su **Autorizzazioni personali**.  Viene visualizzato il [ruolo](../active-directory/role-based-access-control-what-is.md#built-in-roles) dell'utente nella sottoscrizione. Per creare cluster HDInsight è necessario almeno l'accesso come collaboratore.

- Per usare un gruppo di risorse esistente:

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Fare clic su **Gruppi di risorse** dal menu a sinistra per elencare i gruppi di risorse.
    3. Fare clic sul gruppo di risorse da usare per la creazione del cluster HDInsight.
    4. Fare clic su **Controllo di accesso (IAM)** e verificare che sia assegnato, a se stessi o a un gruppo a cui si appartiene, l'accesso come collaboratore al gruppo di risorse.

Se viene visualizzato l'errore NoRegisteredProviderFound o l'errore MissingSubscriptionRegistration, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Elencare e visualizzare i cluster
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** nel menu a sinistra per elencare i cluster esistenti. Se il pulsante **Cluster HDInsight** non è visualizzato, prima fare clic su **Tutti i servizi**.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare il filtro nella parte superiore della pagina.
4. Fare clic su un cluster nell'elenco per visualizzare la pagina della panoramica:

    ![Informazioni di base sul cluster HDInsight nel portale di Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **Menu Panoramica:**
    * **Dashboard**: apre l'interfaccia utente Web di Ambari per il cluster.
    * **Secure Shell**: mostra le istruzioni per la connessione al cluster tramite connessione Secure Shell (SSH).
    * **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
    * **Sposta**: sposta il cluster in una sottoscrizione o un gruppo di risorse diverso.
    * **Elimina**: elimina il cluster.

    **Menu a sinistra:**
    * **Log attività**: visualizza ed effettua una query dei log attività.
    * **Controllo di accesso (IAM)**: usa le assegnazioni di ruolo.  Vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md).
    * **Tag**: consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata **progetto**e usare un valore comune per tutti i servizi associati a un progetto specifico.
    * **Diagnostica e risoluzione dei problemi**: visualizza informazioni per la risoluzione dei problemi.
    * **Blocchi**: aggiunge un blocco per impedire la modifica o l'eliminazione del cluster.
    * **Script di automazione**: visualizza ed esporta il modello di Azure Resource Manager per il cluster. Attualmente è possibile esportare solo l'account di archiviazione di Azure dipendente. Vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Avvio rapido**: visualizza informazioni utili per iniziare a usare HDInsight.
    * **Strumenti per HDInsight**: informazioni della Guida per gli strumenti correlati a HDInsight.
    * **Utilizzo di core della sottoscrizione**: visualizza i core usati e disponibili per la sottoscrizione.
    * **Ridimensiona cluster**: aumenta e diminuisce il numero di nodi di lavoro del cluster. Vedere [Ridimensionare i cluster](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **SSH + Account di accesso del cluster**: mostra le istruzioni per la connessione al cluster tramite connessione Secure Shell (SSH). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Partner HDInsight**: aggiunge/rimuove il Partner HDInsight corrente.
    * **Metastore esterni**: visualizza i metastore Hive e Oozie. I metastore possono essere configurati solo durante il processo di creazione dei cluster. Vedere [Usare metastore Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Azioni script**: esegue script Bash nel cluster. Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
    * **Applicazioni**: aggiungere/rimuovere applicazioni HDInsight.  Vedere [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).
    * **Monitoraggio**: consente di monitorare il cluster in Azure Operations Management Suite e Azure Log Analytics.
    * **Proprietà**: visualizza le proprietà del cluster.
    * **Account di archiviazione**: visualizza gli account di archiviazione e le chiavi. Gli account di archiviazione vengono configurati durante il processo di creazione dei cluster.
    * **Accesso a Data Lake Store**: consente di configurare l'accesso a Data Lake Store.  Vedere [Creare cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    * **Integrità risorse** : vedere [Panoramica su Integrità risorse di Azure](../service-health/resource-health-overview.md).
    * **Nuova richiesta di supporto**: consente di creare un ticket di supporto con il supporto Microsoft.
    
6. Fare clic su **Proprietà**:

    Le proprietà sono:

   * **Nome host**: nome del cluster.
   * **URL del cluster**: URL per l'interfaccia Web Ambari.
   * **Secure Shell (SSH)**: nome utente e nome host da usare per l'accesso al cluster tramite SSH.
   * **Stato**: uno dei valori seguenti: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued o ClusterCustomization.
   * **Area**: località di Azure. Per un elenco di località di Azure supportate, vedere l'elenco a discesa **Area** in [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data di creazione**: data di distribuzione del cluster.
   * **Sistema operativo**: **Windows** o **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versione**. Vedere [Versioni di HDInsight](hdinsight-component-versioning.md).
   * **Sottoscrizione**: nome della sottoscrizione.
   * **Origine dati predefinita**: file system predefinito del cluster.
   * **Worker nodes size** (Dimensioni nodi del ruolo di lavoro): dimensioni della macchina virtuale selezionata dei nodi di lavoro.
   * **Dimensioni nodo head**: dimensioni della macchina virtuale selezionata dei nodi head.
   * **Rete virtuale**: nome della rete virtuale e della subnet in cui viene distribuito il cluster, se ne è stato selezionato uno al momento della distribuzione.

## <a name="delete-clusters"></a>Eliminare cluster
L'eliminazione di un cluster non determina l'eliminazione dell'account di archiviazione predefinito né di eventuali account di archiviazione collegati. È possibile ricreare il cluster usando gli stessi account di archiviazione e gli stessi metastore. È consigliabile usare un nuovo contenitore BLOB predefinito quando si ricrea il cluster.

1. Accedere al [portale][azure-portal].
2. Scegliere **Cluster HDInsight** dal menu di sinistra. Se il pulsante **Cluster HDInsight** non è visualizzato, prima fare clic su **Tutti i servizi**.
3. Fare clic sul cluster che si vuole eliminare.
4. Scegliere **Elimina** dal menu in alto e quindi seguire le istruzioni.

Vedere anche [Sospendere/Arrestare i cluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Aggiungere altri account di archiviazione

Dopo la creazione di un cluster, è possibile aggiungere altri account di archiviazione di Azure e account Azure Data Lake Store. Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster Azure HDInsight senza dover ricreare il cluster.

> [!NOTE]
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).
>
>

Impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Hadoop

    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. A causa di questo comportamento, tutti i processi in esecuzione e in sospeso avranno esito negativo al completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.
* hbase

    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Per altre informazioni sull'uso della shell HBase, vedere [Iniziare a usare un esempio di Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

    A tale scopo, è possibile scegliere tra due opzioni:

  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)

    Per altri dettagli, vedere la [documentazione di Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

    ![Ribilanciamento di HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Ecco un esempio di comando dell'interfaccia della riga di comando per bilanciare di nuovo la topologia di Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Per ridimensionare i cluster**

1. Accedere al [portale][azure-portal].
2. Scegliere **Cluster HDInsight** dal menu di sinistra.
3. Fare clic sul cluster che si vuole ridimensionare.
3. Fare clic su **Ridimensiona cluster**.
4. Immettere il **numero di nodi del ruolo di lavoro**. Il limite relativo al numero di nodi del cluster varia in base alla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.  Le informazioni sui costi riflettono le modifiche apportate al numero di nodi.

    ![Scalabilità di HDInsight Hadoop, HBase, Storm, Spark](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Sospendere/Arrestare i cluster

La maggior parte dei processi Hadoop sono processi batch che vengono eseguito solo occasionalmente. La maggior parte dei cluster Hadoop non viene usata per l'elaborazione per lunghi periodi di tempo. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato.
Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Questo processo può essere programmato in molti modi:

* Usare Data factory di Azure. Per la creazione di servizi collegati di HDInsight, vedere [Creare cluster Hadoop on demand basati su Linux in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) .
* Usare Azure PowerShell.  Vedere [Analizzare i dati relativi ai ritardi dei voli](hdinsight-analyze-flight-delay-data.md).
* Usare l'interfaccia della riga di comando di Azure. Vedere [Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md).
* Usare HDInsight .NET SDK. Vedere [Inviare processi di Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [Eliminare cluster](#delete-clusters)

## <a name="move-cluster"></a>Spostare il cluster

È possibile spostare un cluster HDInsight in un altro gruppo di risorse di Azure o in un'altra sottoscrizione.  Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Aggiornare i cluster

Vedere [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Aprire l'interfaccia utente Web di Ambari

Ambari offre un'interfaccia utente Web di gestione di Hadoop intuitiva e semplice da usare supportata dalle API RESTful. Con Ambari agli amministratori di sistema possono gestire e monitorare i cluster Hadoop.

1. Aprire un cluster HDInsight nel portale di Azure.  Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).
2. Fare clic su **Dashboard cluster**.

    ![Menu del cluster Hadoop di HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Immettere il nome utente e la password del cluster.  Il nome utente predefinito del cluster è _admin_. Ecco che aspetto ha l'interfaccia utente Web di Ambari:

    ![Interfaccia utente Web di Ambari per il cluster Hadoop di HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Modificare le password
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight (anche noto come account utente HTTP) e l'account utente SSH vengono creati durante il processo di creazione. È possibile usare l'interfaccia utente Web di Ambari per modificare il nome utente e la password dell'account utente del cluster e azioni script per modificare l'account utente SSH.

### <a name="change-the-cluster-user-password"></a>Modificare la password utente del cluster
Per modificare la password dell'utente del cluster, è possibile usare l'interfaccia utente Web di Ambari. Per accedere ad Ambari, è necessario usare il nome utente e la password esistenti del cluster.

> [!NOTE]
> Se si modifica la password utente del cluster (amministratore), le azioni script eseguite sul cluster potrebbero avere esito negativo. Se sono presenti azioni script persistenti che hanno come destinazione nodi di lavoro, questi script potrebbero avere esito negativo se si aggiungono nodi al cluster tramite operazioni di ridimensionamento. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Accedere all'interfaccia utente Web di Ambari usando le credenziali utente del cluster HDInsight. Il nome utente predefinito è **admin**. L'URL è **https://&lt;Nome cluster HDInsight>azurehdinsight.net**.
2. Scegliere **Admin** (Amministratore) dal menu in alto e quindi fare clic su "Manage Ambari" (Gestisci Ambari).
3. Scegliere **Users**(Utenti) dal menu a sinistra.
4. Fare clic su **Admin**.
5. Fare clic su **Change Password**(Modifica password).

Ambari modifica la password in tutti i nodi del cluster.

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
3. Nel portale di Azure fare clic su **Cluster HDInsight**.
4. Fare clic sul cluster HDInsight.
4. Fare clic su **Azioni script**.
4. Nella pannello **Azioni script** selezionare **Invia nuova**. Quando viene visualizzato il pannello **Invia azione script**, immettere le informazioni seguenti:

   | Campo | Valore |
   | --- | --- |
   | NOME |Modifica password SSH |
   | URI script Bash |L'URI per il file changepassword.sh |
   | Nodi (head, lavoro, Nimbus, Supervisor, Zookeeper, e così via) |✓ per tutti i tipi di nodo elencati |
   | Parametri |Immettere il nome utente SSH e la nuova password. Deve essere presente uno spazio tra il nome utente e la password. |
   | Salvare questa azione script... |Lasciare questo campo vuoto. |
5. Selezionare **Crea** per applicare lo script. Al termine dell'esecuzione dello script, è possibile connettersi al cluster usando SSH con la nuova password.

## <a name="grantrevoke-access"></a>Concedere/Revocare l'accesso
Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso tramite l'[interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Trovare l'ID sottoscrizione

**Per trovare gli ID sottoscrizione di Azure**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Sottoscrizioni**. Ogni sottoscrizione ha un nome e un ID.

Ogni cluster è associato a una sottoscrizione di Azure. L'ID sottoscrizione viene visualizzato nel riquadro **Informazioni di base** del cluster. Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
In modalità Azure Resource Manager ogni cluster HDInsight viene creato con un gruppo di Azure Resource Manager. Il gruppo di Resource Manager a cui appartiene un cluster viene visualizzato in:

* Colonna **Gruppo di risorse** dell'elenco di cluster.
* Riquadro **Informazioni di base** del cluster.  

Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Trovare gli account di archiviazione

Per archiviare i dati i cluster HDInsight usano un account di Archiviazione di Azure oppure Azure Data Lake Store. Ogni cluster HDInsight può avere un account di archiviazione predefinito e un numero di account di archiviazione collegati. Per elencare gli account di archiviazione, aprire innanzitutto il cluster dal portale e quindi fare clic su **Account di archiviazione**:

![Account di archiviazione del cluster HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Nella schermata precedente c'è una colonna __Predefinito__ che indica se l'account è l'account di archiviazione predefinito.

Per elencare gli account di Data Lake Store, fare clic su **Accesso a Data Lake Store** nella schermata precedente.

## <a name="run-hive-queries"></a>Eseguire query Hive
Non è possibile eseguire il processo Hive direttamente dal portale di Azure, ma è possibile usare la vista Hive nell'interfaccia utente Web di Ambari.

**Per eseguire query Hive usando la vista Hive di Ambari**

1. Accedere all'interfaccia utente Web di Ambari usando le credenziali utente del cluster HDInsight. Il nome utente predefinito è **admin**. L'URL è **https://&lt;Nome cluster HDInsight>azurehdinsight.net**.
2. Aprire Hive View (vista Hive) come illustrato nella schermata seguente:  

    ![Vista Hive di HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Scegliere **Query** dal menu in alto.
4. Immettere una query Hive in **Editor query** e quindi fare clic su **Esegui**.

## <a name="monitor-jobs"></a>Monitorare i processi
Vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Ricerca dei file
Tramite il portale di Azure è possibile esplorare il contenuto del contenitore predefinito.

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** nel menu a sinistra per elencare i cluster esistenti.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare il filtro nella parte superiore della pagina.
4. Scegliere **Account di archiviazione** dal menu a sinistra del cluster.
5. Fare clic su un account di archiviazione.
7. Fare clic sul riquadro **BLOB** .
8. Fare clic sul nome del contenitore predefinito.

## <a name="monitor-cluster-usage"></a>Monitorare l'utilizzo del cluster
La sezione **Utilizzo** del pannello relativo al cluster HDInsight contiene informazioni sul numero di memorie centrali disponibili per la sottoscrizione da usare con HDInsight, il numero di memorie centrali allocate al cluster e il modo in cui vengono allocate per i nodi nel cluster. Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

> [!IMPORTANT]
> Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare  Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster

* [Usare Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si sono apprese alcune funzioni amministrative di base. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Uso dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)
* [Uso dell'API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usare Hive in HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare Sqoop in HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Riga di comando di Hadoop"
