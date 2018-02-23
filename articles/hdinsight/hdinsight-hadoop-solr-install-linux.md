---
title: Usare un'azione script per installare Solr in HDInsight basato su Linux | Documentazione Microsoft
description: Informazioni su come installare Solr nei cluster HDInsight Hadoop basati su Linux utilizzando azioni di Script.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: f642a1f8060f566ec95b23995d0f82191b0c5315
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installare e usare Solr nei cluster Hadoop di HDInsight

Informazioni su come installare Solr in Azure HDInsight usando un'azione script. Solr è una piattaforma di ricerca avanzata e offre funzionalità di ricerca di livello aziendale per i dati gestiti da Hadoop.

> [!IMPORTANT]
    > I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> Lo script di esempio usato in questo documento installa Solr 4.9 con una configurazione specifica. Per configurare il cluster Solr con raccolte, partizioni, schemi, repliche diverse e così via, sarà necessario modificare lo script e i file binari di Solr.

## <a name="whatis"></a>Che cos'è Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) è una piattaforma di ricerca aziendale che permette di eseguire ricerche full-text avanzate sui dati. Mentre Hadoop consente di archiviare e gestire quantità elevate di dati, Apache Solr offre le funzionalità di ricerca necessarie per recuperare rapidamente i dati.

> [!WARNING]
> I componenti forniti con il cluster HDInsight sono completamente supportati da Microsoft.
>
> I componenti personalizzati, ad esempio Solr, ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe non essere in grado di risolvere i problemi con componenti personalizzati. Potrebbe essere necessario coinvolgere le community open source per ricevere assistenza. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Funzionalità dello script

Questo script apporta le modifiche seguenti al cluster HDInsight:

* Installa Solr 4.9 in `/usr/hdp/current/solr`
* Crea un utente **solrusr**, usato per eseguire il servizio Solr
* Imposta **solrusr** come proprietario di `/usr/hdp/current/solr`.
* Aggiunge una configurazione [Upstart](http://upstart.ubuntu.com/) che avvia automaticamente Solr.

## <a name="install"></a>Installare R mediante azioni script

Uno script di esempio per l'installazione di Solr in un cluster HDInsight è disponibile all'indirizzo seguente:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Per creare un cluster con Solr installato, usare la procedura nel documento [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Durante il processo di creazione, usare la procedura seguente per installare Solr:

1. Dal pannello __Riepilogo cluster__ selezionare__Impostazioni avanzate__ e quindi __Azioni script__. Usare le seguenti informazioni per popolare il modulo:

   * **NOME**: immettere un nome descrittivo per l'azione script.
   * **URI SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: selezionare questa opzione
   * **RUOLO DI LAVORO**: selezionare questa opzione
   * **ZOOKEEPER**: selezionare questa opzione per l'installazione nel nodo Zookeeper
   * **PARAMETRI**: lasciare questo campo vuoto

2. Nella parte inferiore della sezione **Azioni script** usare il pulsante **Seleziona** per salvare la configurazione. Infine, usare il pulsante **Avanti** per tornare al __Riepilogo cluster__

3. Dalla pagina __Riepilogo cluster__ selezionare __Crea__ per creare il cluster.

## <a name="usesolr"></a>Come si usa Solr in HDInsight

> [!IMPORTANT]
> I passaggi in questa sezione illustrano le funzionalità di base di Solr. Per altre informazioni sull'uso di Solr, vedere il [sito Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Dati dell'indice

Usare i passaggi seguenti per aggiungere dei dati di esempio a Solr e quindi eseguire una query:

1. Connettersi al cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > I passaggi successivi di questo documento usano un tunnel SSL per la connessione all'interfaccia utente Web di Solr. Per usare questi passaggi, è necessario stabilire un tunnel SSL e quindi configurare il browser per usarlo.
     >
     > Per altre informazioni, vedere il documento [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

2. Usare i comandi seguenti per indicizzare i dati di esempio tramite Solr:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Viene restituito l'output seguente alla console:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L'utilità `post.jar` aggiunge i documenti **solr.xml** e **monitor.xml** all'indice.
  
3. Usare il comando seguente per eseguire query nell'API REST Solr:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Questo comando cerca **collection1** per tutti i documenti corrispondenti **\*:\*** (codificato come \*% 3A\* nella stringa della query). Il documento JSON seguente è un esempio della risposta:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Uso del dashboard di Solr

Il dashboard di Solr è un'interfaccia utente Web che consente di utilizzare Solr con un Web browser. Il dashboard di Solr non viene esposto direttamente su Internet dal cluster HDInsight. È possibile usare un tunnel SSH per accedere al servizio. Per altre informazioni sull'uso del tunnel SSH, vedere il documento [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

Dopo aver stabilito un tunnel SSH, seguire questa procedura per usare il dashboard di Solr:

1. Determinare il nome host per il nodo head primario:

   1. Usare SSH per connettersi al nodo head del cluster. Ad esempio, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Per altre informazioni sull'uso di SSH, vedere [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Utilizzare il comando seguente per ottenere il nome host completo:

        ```bash
        hostname -f
        ```

        Il comando restituisce un valore simile al nome host seguente:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Salvare il valore restituito, poiché verrà usato in un secondo momento.

2. Nel browser connettersi a **http://NOMEHOST:8983/solr/#/**, dove **NOMEHOST** è il nome stabilito nei passaggi precedenti.

    La richiesta viene instradata attraverso il tunnel SSH all'interfaccia utente Web di Solr nel cluster. La pagina appare simile alla seguente immagine:

    ![Immagine del dashboard di Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. Nel riquadro sinistro selezionare **collection1** nell'elenco a discesa **Core Selector** (Selettore base). Sotto **collection1** dovrebbero essere visualizzate diverse voci.

4. Nelle voci elencate sotto **collection1** selezionare **Query**. Usare i valori seguenti per popolare la pagina di ricerca:

   * Nella casella di testo **q** immettere **\*:**\*. Verranno restituiti dalla query tutti i documenti indicizzati in Solr. Per cercare una stringa specifica nei documenti, è possibile immettere qui la stringa.
   * Selezionare il formato di output nella casella di testo **wt** . Il valore predefinito è **json**.

     Selezionare infine il pulsante **Execute Query** in basso nella pagina di ricerca.

     ![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     L'output restituisce i due documenti che sono stati aggiunti all'indice in precedenza. L'output è simile al seguente documento JSON:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Avviare e arrestare Solr

Per arrestare o avviare Solr manualmente, usare i comandi seguenti:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Eseguire il backup dei dati indicizzati

Per eseguire il backup dei dati Solr nell'archivio predefinito per il cluster, seguire questi passaggi:

1. Connettersi al cluster tramite SSH, quindi usare il comando seguente per ottenere il nome host per il nodo head:

    ```bash
    hostname -f
    ```

2. Per creare uno snapshot dei dati indicizzati, usare il comando seguente. Sostituire **HOSTNAME** con il nome restituito dal comando precedente:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    La risposta restituita è simile al codice XML seguente:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Passare le directory a `/usr/hdp/current/solr/example/solr`. Per ogni raccolta è presente una sottodirectory. Ogni directory di una raccolta contiene una directory `data` che contiene lo snapshot per la raccolta.

4. Per creare un archivio compresso della cartella dello snapshot, usare il comando seguente:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Sostituire i valori `snapshot.20150806185338855` con il nome dello snapshot per la raccolta.

    Viene così creato un nuovo archivio denominato **snapshot.20150806185338855.tgz** che include il contenuto della directory **snapshot.20150806185338855**.

5. Si potrà quindi inserire l'archivio nella risorsa di archiviazione primaria del cluster usando il comando seguente:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Per altre informazioni sulle operazioni di backup e ripristino di Solr, vedere [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Passaggi successivi

* [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

* [Installare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Usare la personalizzazione dei cluster per installare Hue nei cluster Hadoop di HDInsight. Hue è un insieme di applicazioni Web che consente di interagire con un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
