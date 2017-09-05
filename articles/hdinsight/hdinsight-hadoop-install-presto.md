---
title: Installare Presto nei cluster Azure HDInsight Linux | Microsoft Docs
description: Informazioni su come installare Presto e Airpal nei cluster HDInsight Hadoop basati su Linux usando azioni di script.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: fb5e500b922522e4cdfa7dd07b5233688b916b3e
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Installare e usare Presto nei cluster HDInsight Hadoop

In questo argomento si apprenderà come installare Presto nei cluster HDInsight Hadoop usando le azioni di script. Si apprenderà anche come installare Airpal in un cluster HDInsight Presto esistente.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster **HDInsight 3.5 Hadoop** che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Che cos'è Presto?
[Presto](https://prestodb.io/overview.html) è un motore di query SQL distribuito veloce per Big Data. Presto è adatto per l'esecuzione di query interattive di petabyte di dati. Per altre informazioni sui componenti di Presto e il relativo funzionamento, vedere [Presto concepts](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst) (Concetti di Presto).

> [!WARNING]
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
> 
> I componenti personalizzati, ad esempio Presto, ricevono un supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Installare Presto mediante l'azione di script

Questa sezione fornisce istruzioni su come usare lo script di esempio quando si crea un nuovo cluster usando il portale di Azure. 

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Assicurarsi di creare il cluster usando il flusso di creazione del cluster **personalizzato**. È necessario assicurarsi che il cluster creato soddisfi i requisiti seguenti.

    a. Deve essere un cluster Hadoop con HDInsight versione 3.5.

    b. Deve usare Archiviazione di Azure come archivio dati. L'uso di Presto in un cluster che usa Azure Data Lake Store come opzione di archiviazione non è ancora supportato. 

    ![Creazione del cluster HDInsight con opzioni personalizzate](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Nel pannello **Impostazioni avanzate** selezionare **Azioni script** e specificare le informazioni seguenti:
   
   * **NOME**: immettere un nome descrittivo per l'azione script.
   * **URI script Bash**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: selezionare questa opzione
   * **RUOLO DI LAVORO**: selezionare questa opzione
   * **ZOOKEEPER**: deselezionare questa casella di controllo
   * **PARAMETRI**: lasciare questo campo vuoto


3. Nella parte inferiore del pannello **Azioni script** fare clic sul pulsante **Seleziona** per salvare la configurazione. Infine fare clic sul pulsante **Seleziona** nella parte inferiore del pannello **Impostazioni avanzate** per salvare le informazioni relative alla configurazione.

4. Continuare il provisioning del cluster come descritto nell'argomento relativo all' [esecuzione del provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Usare Presto con HDInsight

Eseguire i passaggi seguenti per usare Presto in un cluster HDInsight dopo averlo installato con la procedura descritta in precedenza.

1. Connettersi al cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Avviare la shell di Presto usando il comando seguente.
   
        presto --schema default

3. Eseguire una query sulla tabella di esempio, **hivesampletable**, che è disponibile in tutti i cluster HDInsight per impostazione predefinita.
   
        select count (*) from hivesampletable;
   
    Per impostazione predefinita sono già configurati i connettori [Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) per Presto. Il connettore Hive è configurato per usare l'installazione Hive predefinita, in modo che tutte le tabelle provenienti da Hive diventino automaticamente visibili in Presto.

    Per informazioni dettagliate su come è possibile usate Presto, vedere la [documentazione su Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Usare Airpal con Presto

[Airpal](https://github.com/airbnb/airpal#airpal) è un'interfaccia per query basate su Web open source per Presto. Per altre informazioni su Airpal, vedere la [documentazione su Airpal](https://github.com/airbnb/airpal#airpal).

In questa sezione verrà esaminata la procedura per **installare Airpal sul nodo perimetrale** di un cluster HDInsight Hadoop in cui Presto è già installato. Ciò garantisce che l'interfaccia per query Web Airpal sia disponibile su Internet.

1. Connettersi tramite SSH al nodo head del cluster HDInsight in cui è installato Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una volta eseguita la connessione, eseguire il comando seguente.

        sudo slider registry  --name presto1 --getexp presto 
   
    Verrà visualizzato un output simile al seguente:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Nell'output prendere nota del valore per la proprietà **value**. Sarà necessario durante l'installazione di Airpal nel nodo perimetrale del cluster. Dall'output precedente il valore che sarà necessario è **10.0.0.12:9090**.

4. Usare il modello  **[qui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)**  per creare un nodo perimetrale del cluster HDInsight e fornire i valori come illustrato nello screenshot seguente.

    ![Installazione di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Fare clic su **Acquista**.

6. Dopo le modifiche vengono applicate alla configurazione del cluster, è possibile accedere all'interfaccia web Airpal usando la procedura seguente.

    a. Fare clic su **Applicazioni** nel pannello del cluster.

    ![Avvio di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    b. Dal pannello **App installate** fare clic su **Portale** per Airpal.

    ![Avvio di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    c. Quando richiesto, immettere le credenziali admin specificate durante la creazione del cluster HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizzare un'installazione Presto nel cluster HDInsight

Dopo avere installato Presto in un cluster HDInsight Hadoop, è possibile personalizzare l'installazione per apportare modifiche, ad esempio aggiornare le impostazioni della memoria, modificare i connettori e così via. Seguire quindi questa procedura.

1. Connettersi tramite SSH al nodo head del cluster HDInsight in cui è installato Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Apportare le modifiche di configurazione nel file `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Per altre informazioni sulla configurazione di Presto, vedere [Configurazione di Presto per i cluster basati su YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Arrestare e terminare l'istanza in esecuzione corrente di Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Avviare una nuova istanza di Presto con la personalizzazione.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Attendere che la nuova istanza sia pronta e annotare l'indirizzo del coordinatore di Presto.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generare dati di benchmark per i cluster HDInsight in cui è in esecuzione Presto

TPC-DS è lo standard del settore per la misurazione delle prestazioni di molti sistemi di supporto decisionale, compresi i sistemi di Big Data. È possibile usare Presto nei cluster HDInsight per generare i dati e confrontarli con i dati di benchmark di HDInsight. Per altre informazioni, vedere [qui](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Vedere anche
* [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.

* [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

* [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

