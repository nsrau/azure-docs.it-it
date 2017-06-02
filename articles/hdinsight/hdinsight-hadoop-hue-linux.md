---
title: Usare Hue con Hadoop nei cluster HDInsight Linux | Documentazione Microsoft
description: Informazioni su come installare e usare Hue con i cluster Hadoop in HDInsight Linux.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 66884a73a8ea1cbf72a48f9a776fa45ae1976591
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installare e usare Hue nei cluster Hadoop di HDInsight

Informazioni su come installare Hue nei cluster HDInsight e usare il tunneling per instradare le richieste a Hue.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="what-is-hue"></a>Informazioni su Hue
Hue è un insieme di applicazioni Web che consente di interagire con un cluster Hadoop. È possibile usare Hue per esplorare lo spazio di archiviazione associato a un cluster Hadoop (WASB nel caso di cluster HDInsight), eseguire processi Hive e script Pig e così via. I componenti seguenti sono disponibili con l'installazione di Hue in un cluster Hadoop di HDInsight.

* Editor Hive Beeswax
* Pig
* Metastore Manager
* Oozie
* FileBrowser (che interagisce con il contenitore predefinito di WASB)
* Job Browser

> [!WARNING]
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Installare Hue mediante azioni script

Lo script https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh consente di installare Hue in un cluster HDInsight basato su Linux. Usare questo script per installare Hue nei cluster con Archiviazione BLOB di Azure (WASB) o Azure Data Lake Store come risorsa di archiviazione predefinita.

Questa sezione fornisce istruzioni su come usare lo script quando si effettua il provisioning del cluster usando il portale di Azure.

> [!NOTE]
> Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md) senza completarlo.

   > [!NOTE]
   > Per installare Hue nei cluster HDInsight, la dimensione consigliata del nodo head è minimo A4 (8 core, 14 GB di memoria).
   >
   >
2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni come mostrato di seguito:

    ![Specificare i parametri di azione script per Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "Specificare i parametri di azione script per Hue")

   * **NOME**: immettere un nome descrittivo per l'azione script.
   * **URI SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: selezionare questa opzione
   * **LAVORO**: lasciare vuoto questo campo.
   * **ZOOKEEPER**: lasciare vuoto questo campo.
   * **PARAMETRI**: lasciare vuoto questo campo.
3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.
4. Continuare il provisioning del cluster come descritto in [Effettuare il provisioning dei cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Usare Hue con i cluster HDInsight

Il tunneling SSH è il solo modo di accedere a Hue nel cluster una volta che è in esecuzione. Il tunneling tramite SSH consente al traffico di raggiungere direttamente il nodo head del cluster in cui viene eseguito Hue. Al termine del provisioning del cluster, seguire questa procedura per usare Hue in un cluster HDInsight Linux.

> [!NOTE]
> È consigliabile utilizzare il browser web di Firefox per seguire le istruzioni seguenti.
>
>

1. Utilizzare le informazioni contenute in [Utilizzare SSH Tunneling per accedere all'interfaccia utente Web Ambari, a ResourceManager, JobHistory, NameNode, Oozie e ad altre interfacce utente Web](hdinsight-linux-ambari-ssh-tunnel.md) per creare un tunnel SSH dal sistema client al cluster HDInsight e quindi configurare il browser Web per usare il tunnel SSH come proxy.

2. Dopo aver creato un tunnel SSH e configurato il browser per inoltrare il traffico attraverso di esso, è necessario trovare il nome host del nodo head primario. È possibile farlo tramite la connessione al cluster con SSH sulla porta 22. Ad esempio, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` dove **USERNAME** è il nome utente SSH e **CLUSTERNAME** è il nome del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Una volta connessi, utilizzare il comando seguente per ottenere il nome di dominio completo del nodo head primario:

        hostname -f

    Verrà restituito un nome simile al seguente:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Questo è il nome host del nodo head primario in cui si trova il sito Web Hue.
4. Usare il browser per aprire il portale di Hue all'indirizzo http://HOSTNAME:8888. Sostituire HOSTNAME con il nome ottenuto nel passaggio precedente.

   > [!NOTE]
   > Quando si accede per la prima volta, viene richiesto di creare un account per l'accesso al portale Hue. Le credenziali specificate saranno limitate al portale e non sono correlate alle credenziali amministratore o utente SSH specificate durante il provisioning del cluster.
   >
   >

    ![Accedere al portale Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Specificare le credenziali per il portale Hue")

### <a name="run-a-hive-query"></a>Eseguire una query Hive
1. Nel portale di Hue fare clic su **Query Editors** (Editor query) e quindi su **Hive** per aprire l'editor Hive.

    ![Usare Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Usare Hive")
2. Nella scheda **Assist** (Assistenza) in **Database** dovrebbe essere visibile **hivesampletable**. Si tratta di una tabella di esempio inclusa in tutti i cluster Hadoop in HDInsight. Immettere una query di esempio nel riquadro destro e visualizzare l'output nella scheda **Risultati** nel riquadro sottostante, come illustrato nella schermata.

    ![Eseguire query Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Eseguire query Hive")

    È anche possibile usare la scheda **Grafico** per vedere una rappresentazione visiva dei risultati.

### <a name="browse-the-cluster-storage"></a>Esplorare l'archiviazione cluster
1. Nel portale di Hue fare clic su **Esplora file** nell'angolo superiore destro della barra dei menu.
2. Per impostazione predefinita, il browser file viene aperto in corrispondenza della directory **/user/myuser** . Fare clic sulla barra subito prima della directory user nel percorso per passare alla radice del contenitore di archiviazione di Azure associato al cluster.

    ![Usare il browser file](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Usare il browser file")
3. Fare clic son il pulsante destro del mouse su un file o una cartella per visualizzare le operazioni disponibili. Usare il pulsante **Carica** nell'angolo destro per caricare i file nella directory corrente. Usare il pulsante **Nuovo** per creare nuovi file o directory.

> [!NOTE]
> Il browser file Hue può mostrare solo il contenuto del contenitore predefinito associato al cluster HDInsight. Eventuali account di archiviazione o contenitori aggiuntivi associati al cluster non saranno accessibili tramite il browser file. I contenitori aggiuntivi associati al cluster saranno comunque sempre accessibili per i processi Hive. Ad esempio, se si immette il comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` nell'editor Hive, è possibile vedere il contenuto anche dei contenitori aggiuntivi. In questo comando **newcontainer** non è il contenitore predefinito associato a un cluster.
>
>

## <a name="important-considerations"></a>Considerazioni importanti
1. Lo script usato per installare Hue ne consente l'installazione solo nel nodo head del cluster.

2. Durante l'installazione vengono riavviati più servizi Hadoop (HDFS, YARN, MR2, Oozie) per l'aggiornamento della configurazione. Al termine dell'installazione di Hue tramite lo script, è possibile che l'avvio di altri servizi Hadoop richieda qualche istante. Ciò potrebbe influire inizialmente sulle prestazioni di Hue. Una volta avviati tutti i servizi, Hue sarà completamente funzionale.
3. Hue non riconosce i processi di Tez, che attualmente corrisponde all'importazione predefinita per Hive. Se si vuole usare MapReduce come motore di esecuzione di Hive, aggiornare lo script per l'uso dei comandi seguenti:

         set hive.execution.engine=mr;

4. Con i cluster Linux è possibile avere uno scenario in cui i servizi vengono eseguiti sul nodo head primario mentre Resource Manager potrebbe essere in esecuzione su quello secondario. Questo scenario potrebbe causare errori (illustrati di seguito) quando si usa Hue per visualizzare i dettagli dei processi IN ESECUZIONE nel cluster. I dettagli del processo possono tuttavia essere visualizzati dopo il completamento del processo.

   ![Errore nel portale di Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Errore nel portale di Hue")

   Questo è causato da un problema noto. Come soluzione alternativa, modificare Ambari in modo che anche l'istanza di Resource Manager attiva venga eseguita sul nodo head primario.
5. Hue riconosce WebHDFS mentre i cluster HDInsight usano Archiviazione di Azure Storage tramite `wasbs://`. Lo script personalizzato usato con l'azione script installa WebWasb, un servizio compatibile con WebHDFS-per comunicare con WASB. Quindi, anche se in alcuni punti nel portale di Hue è indicato HDFS (come quando si sposta il mouse su **File Browser**), dovrà essere interpretato come WASB.

## <a name="next-steps"></a>Passaggi successivi
* [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.
* [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.
* [Installare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md). Usare la personalizzazione dei cluster per installare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

