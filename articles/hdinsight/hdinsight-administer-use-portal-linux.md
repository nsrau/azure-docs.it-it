---
title: Gestire cluster Hadoop in HDInsight tramite il portale di Azure | Documentazione Microsoft
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
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 82c12f4a2a24e190babfd88ff667e24d6c45ebac
ms.lasthandoff: 03/25/2017


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire cluster Hadoop in HDInsight tramite il portale di Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Il [portale di Azure][azure-portal] consente di gestire cluster Hadoop in Azure HDInsight. Usare il selettore di schede per informazioni sulla gestione di cluster Hadoop in HDInsight con altri strumenti. 

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-portal"></a>Aprire il portale
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Dopo avere aperto il portale, è possibile:
   
   * Scegliere **Nuovo** dal menu di sinistra per creare un nuovo cluster:
     
       ![Pulsante Nuovo cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
   * Fare clic su **Cluster HDInsight** dal menu a sinistra per elencare i cluster esistenti
     
       ![Pulsante Cluster HDInsight del portale di Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Se non viene visualizzato il cluster HDInsight, fare clic su **More services** (Altri servizi) in fondo all'elenco, quindi fare clic su **cluster HDInsight** nella sezione **Intelligence e analisi**.

## <a name="create-clusters"></a>Creare i cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md). Per informazioni generali sulla creazione di cluster, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="list-and-show-clusters"></a>Elencare e visualizzare i cluster
1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** nel menu a sinistra per elencare i cluster esistenti.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare il filtro nella parte superiore della pagina.
4. Fare clic su un cluster nell'elenco per visualizzare la pagina della panoramica:
   
    ![Informazioni di base sul cluster HDInsight del Portale di Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
   
    **Menu della panoramica:**

   * **Dashboard**: consente di aprire il dashboard del cluster, ovvero Ambari Web per i cluster basati su Linux.
   * **Secure Shell**: mostra le istruzioni per la connessione al cluster tramite connessione Secure Shell (SSH).
   * **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
   * **Elimina**: elimina il cluster.

    **Menu a sinistra:**
   
   * **Log attività**: visualizza ed effettua una query dei log attività.
   * **Controllo di accesso (IAM)**: usa le assegnazioni di ruolo.  Vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md).
   * **Tag**: consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata **progetto**e usare un valore comune per tutti i servizi associati a un progetto specifico.
   * **Diagnostica e risoluzione dei problemi**: visualizza informazioni per la risoluzione dei problemi.
   * **Blocchi**: aggiunge un blocco per impedire la modifica o l'eliminazione del cluster.
   * **Script di automazione**: visualizza ed esporta il modello di Azure Resource Manager per il cluster. Attualmente è possibile esportare solo l'account di archiviazione di Azure dipendente. Vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
   * **Avvio rapido**: visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
   * **Strumenti per HDInsight**: informazioni della Guida per gli strumenti correlati a HDInsight.
   * **Account di accesso del cluster**: visualizza le informazioni di accesso del cluster.
   * **Utilizzo di core della sottoscrizione**: visualizza i core usati e disponibili per la sottoscrizione. 
   * **Ridimensiona cluster**: aumenta e diminuisce il numero di nodi di lavoro del cluster. Vedere [Ridimensionare i cluster](hdinsight-administer-use-management-portal.md#scale-clusters).
   * **Secure Shell**: mostra le istruzioni per la connessione al cluster tramite connessione Secure Shell (SSH). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
   * **Partner HDInsight**: aggiunge/rimuove il Partner HDInsight corrente.
   * **Metastore esterni**: visualizza i metastore Hive e Oozie. I metastore possono essere configurati solo durante il processo di creazione dei cluster. Vedere [Usare metastore Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
   * **Azioni script**: esegue script Bash nel cluster. Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
   * **Applicazioni**: aggiungere/rimuovere applicazioni HDInsight.  Vedere [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).
   * **Proprietà**: visualizza le proprietà del cluster.
   * **Account di archiviazione**: visualizza gli account di archiviazione e le chiavi. Gli account di archiviazione vengono configurati durante il processo di creazione dei cluster.
   * **Identità AAD cluster**: 
   * **Nuova richiesta di supporto**: consente di creare un ticket di supporto con il supporto Microsoft.

6. Fare clic su **Proprietà**:
   
    Le proprietà sono:
   
   * **Nome host**: nome del cluster.
   * **URL cluster**. URL per l'interfaccia Web Ambari.
   * **Stato**: include Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Area**: località di Azure. Per un elenco di località di Azure supportate, vedere l'elenco a discesa **Area** in [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Dati creati**.
   * **Sistema operativo**: **Windows** o **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark. 
   * **Versione**. Vedere [Versioni di HDInsight](hdinsight-component-versioning.md)
   * **Sottoscrizione**: nome della sottoscrizione.
   * **Origine dati predefinita**: file system predefinito del cluster.
   * **Worker nodes size** (Dimensioni nodi di lavoro).
   * **Dimensioni nodo head**.

## <a name="delete-clusters"></a>Eliminare cluster
L'eliminazione di un cluster non determinerà l'eliminazione dell'account di archiviazione predefinito o di qualsiasi account di archiviazione collegato. È possibile ricreare il cluster usando gli stessi account di archiviazione e gli stessi metastore. È consigliabile utilizzare un nuovo contenitore BLOB predefinito quando si ricrea il cluster.

1. Accedere al [portale][azure-portal].
2. Scegliere **Cluster HDInsight** dal menu di sinistra. Se **Cluster HDInsight** non è visualizzato, prima fare clic su **Altri servizi**.
3. Fare clic sul cluster che si vuole eliminare.
4. Scegliere **Elimina** dal menu in alto e quindi seguire le istruzioni.

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
* HBase
  
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
    
    ![Ribilanciamento di HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:
    
        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Per ridimensionare i cluster**

1. Accedere al [portale][azure-portal].
2. Scegliere **Cluster HDInsight** dal menu di sinistra.
3. Fare clic sul cluster che si vuole ridimensionare.
3. Fare clic su **Ridimensiona cluster**.
4. Immettere il **numero di nodi del ruolo di lavoro**. Il limite al numero dei nodi del cluster varia tra le diverse sottoscrizioni di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.  Le informazioni sui costi rifletteranno le modifiche apportate al numero di nodi.
   
    ![Scalabilità di HDInsight Hadoop, HBase, Storm, Spark](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Sospendere/Arrestare i cluster

La maggior parte dei processi Hadoop sono processi batch che vengono eseguito solo occasionalmente. La maggior parte dei cluster Hadoop non viene usata per l'elaborazione per lunghi periodi di tempo. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato.
Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Questo processo può essere programmato in molti modi:

* Usare Data factory di Azure. Per la creazione di servizi collegati di HDInsight, vedere [Creare cluster Hadoop on demand basati su Linux in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) .
* Usare Azure PowerShell.  Vedere [Analizzare i dati relativi ai ritardi dei voli](hdinsight-analyze-flight-delay-data.md).
* Usare l'interfaccia della riga di comando di Azure. Vedere [Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md).
* Usare HDInsight .NET SDK. Vedere [Inviare processi di Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [Eliminare cluster](#delete-clusters)

## <a name="change-passwords"></a>Modificare le password
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight (anche noto come account utente HTTP) e l'account utente SSH vengono creati durante il processo di creazione. È possibile usare l'interfaccia utente Web di Ambari per modificare nome utente e password dell'account utente del cluster e le azioni script per modificare l'account utente SSH.

### <a name="change-the-cluster-user-password"></a>Modificare la password utente del cluster
Per modificare la password dell'utente del cluster, è possibile usare l'interfaccia utente Web di Ambari. Per accedere ad Ambari, è necessario usare il nome utente e la password esistenti del cluster.

> [!NOTE]
> Se si modifica la password utente del cluster (admin), è possibile che le azioni script eseguite sul cluster abbiano esito negativo. Nel caso in cui siano presenti azioni script persistenti che hanno come destinazione nodi di lavoro, queste potrebbero avere esito negativo se si aggiungono nodi al cluster mediante operazioni di ridimensionamento. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
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
   > È necessario usare un editor che prevede LF come terminazione di riga. Se l'editor prevede CRLF, lo script non funzionerà.
   > 
   > 
   
        #! /bin/bash
        USER=$1
        PASS=$2
   
        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
2. Caricare il file in un percorso di archiviazione a cui è possibile accedere da HDInsight con un indirizzo HTTP o HTTPS, ad esempio in un archivio di file pubblico come OneDrive o l'archiviazione BLOB di Azure. Salvare l'URI (indirizzo HTTP o HTTPS) nel file, poiché sarà necessario nel passaggio successivo.
3. Nel portale di Azure fare clic su **Cluster HDInsight**.
4. Fare clic sul cluster HDInsight.
4. Fare clic su **Azioni script**.
4. Nella pannello **Azioni script** selezionare **Invia nuova**. Quando viene visualizzato il pannello **Invia azione script**, immettere le informazioni seguenti.
   
   | Campo | Valore |
   | --- | --- |
   | Nome |Modifica password SSH |
   | URI script Bash |L'URI per il file changepassword.sh |
   | Nodi (head, lavoro, Nimbus, Supervisor, Zookeeper, e così via) |✓ per tutti i tipi di nodo elencati |
   | Parametri |Immettere il nome utente SSH e la nuova password. Deve essere presente uno spazio tra il nome utente e la password. |
   | Salvare questa azione script... |Lasciare questo campo vuoto. |
5. Selezionare **Crea** per applicare lo script. Al termine dell'esecuzione dello script, sarà possibile usare la nuova password per connettersi al cluster tramite SSH.

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

## <a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito
Ogni cluster HDInsight ha un account di archiviazione predefinito. L'account di archiviazione predefinito e le relative chiavi per un cluster vengono visualizzati in **Account di archiviazione**. Vedere [Elencare e visualizzare i cluster](#list-and-show-clusters).

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
> 
> 

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster
Vedere [Uso di Hive con Hadoop in HDInsight tramite SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale, e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-provision-clusters.md)
* [Usare Hive in HDInsight](hdinsight-use-hive.md)
* [Usare Pig in HDInsight](hdinsight-use-pig.md)
* [Usare Sqoop in HDInsight](hdinsight-use-sqoop.md)
* [Introduzione ad Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Riga di comando di Hadoop"

