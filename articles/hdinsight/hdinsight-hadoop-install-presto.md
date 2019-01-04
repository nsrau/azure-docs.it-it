---
title: Installare Presto nei cluster Azure HDInsight Linux
description: Informazioni su come installare Presto e Airpal nei cluster HDInsight Hadoop basati su Linux usando azioni di script.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 4285c633062386657cbea478f327c9a1b088f16a
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383817"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Installare e usare Presto nei cluster HDInsight Hadoop

Questo documento illustra come installare Presto nei cluster HDInsight Hadoop usando le azioni di script. Si apprenderà anche come installare Airpal in un cluster HDInsight Presto esistente.

HDInsight offre anche l'applicazione Starburst Presto per i cluster Apache Hadoop. Per altre informazioni, vedere [Installare applicazioni Hadoop di terze parti in Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)

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

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Assicurarsi di creare il cluster usando il flusso di creazione del cluster **personalizzato**. Il cluster deve soddisfare i requisiti seguenti.

    * Deve essere un cluster Hadoop con HDInsight versione 3.6.

    * Deve usare Archiviazione di Azure come archivio dati. L'uso di Presto in un cluster che usa Azure Data Lake Storage come opzione di archiviazione non è ancora supportato.

    ![Creazione del cluster HDInsight con opzioni personalizzate](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Nell'area **Impostazioni avanzate** selezionare **Azioni script** e specificare le informazioni riportate di seguito. È anche possibile scegliere l'opzione "Installa Presto" per il tipo di script.
   
   * **NOME**: immettere un nome descrittivo per l'azione script.
   * **URI script Bash**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: selezionare questa opzione.
   * **RUOLO DI LAVORO**: selezionare questa opzione.
   * **ZOOKEEPER**: Lasciare vuota questa casella di controllo.
   * **PARAMETRI**: lasciare vuoto questo campo.


3. Nella parte inferiore dell'area **Azioni script** fare clic sul pulsante **Seleziona** per salvare la configurazione. Infine fare clic sul pulsante **Seleziona** nella parte inferiore dell'area **Impostazioni avanzate** per salvare le informazioni di configurazione.

4. Continuare il provisioning del cluster come descritto nell'argomento relativo all' [esecuzione del provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando classica di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Usare Presto con HDInsight

Per lavorare con Presto in un cluster HDInsight, procedere come segue:

1. Connettersi al cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Avviare la shell di Presto usando il comando seguente.
   
        presto --schema default

3. Eseguire una query sulla tabella di esempio, **hivesampletable**, che è disponibile in tutti i cluster HDInsight per impostazione predefinita.
   
        select count (*) from hivesampletable;
   
    Per impostazione predefinita, i connettori [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) per Presto sono già configurati. Il connettore Hive è configurato per usare l'installazione Hive predefinita, in modo che tutte le tabelle provenienti da Hive diventino automaticamente visibili in Presto.

    Per altre informazioni, vedere la [documentazione su Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Usare Airpal con Presto

[Airpal](https://github.com/airbnb/airpal#airpal) è un'interfaccia per query basate su Web open source per Presto. Per altre informazioni su Airpal, vedere la [documentazione su Airpal](https://github.com/airbnb/airpal#airpal).

Seguire questa procedura per installare Airpal nel nodo perimetrale:

1. Connettersi tramite SSH al nodo head del cluster HDInsight in cui è installato Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una volta eseguita la connessione, eseguire il comando seguente.

        sudo slider registry  --name presto1 --getexp presto 
   
    Verrà visualizzato un output simile al codice JSON seguente:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Nell'output prendere nota del valore per la proprietà **value**. Questo valore sarà necessario durante l'installazione di Airpal nel nodo perimetrale del cluster. Dall'output precedente il valore che sarà necessario è **10.0.0.12:9090**.

4. Usare il modello **[qui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** per creare un nodo perimetrale del cluster HDInsight e fornire i valori come illustrato nello screenshot seguente.

    ![Installazione di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Fare clic su **Acquista**.

6. Dopo le modifiche vengono applicate alla configurazione del cluster, è possibile accedere all'interfaccia web Airpal usando la procedura seguente.

    1. Fare clic su **Applicazioni** nella finestra di dialogo del cluster.

        ![Avvio di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Nell'area **App installate** fare clic su **Portale** per Airpal.

        ![Avvio di HDInsight Airpal nel cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Quando richiesto, immettere le credenziali admin specificate durante la creazione del cluster HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizzare un'installazione Presto nel cluster HDInsight

Per personalizzare l'installazione, procedere come segue:

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

TPC-DS è lo standard del settore per la misurazione delle prestazioni di molti sistemi di supporto decisionale, compresi i sistemi di Big Data. È possibile usare Presto per generare i dati e confrontarli con i dati di benchmark di HDInsight. Per altre informazioni, vedere [qui](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Vedere anche 
* [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente Web che semplifica la creazione, l'esecuzione e il salvataggio di processi Apache Pig e Hive.

* [Installare Apache Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

* [Installare Apache Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
