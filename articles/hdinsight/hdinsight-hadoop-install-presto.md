---
title: Installare Presto nei cluster Azure HDInsight Linux
description: Informazioni su come installare Presto e Airpal nei cluster HDInsight Hadoop basati su Linux usando azioni script.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 435c041bb5fb0a398f92914f943166108cc20080
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258344"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Installare e usare Presto nei cluster HDInsight basati su Hadoop

Questo articolo illustra come installare Presto nei cluster HDInsight basato su Hadoop di Azure tramite le azioni di script. Si apprenderà anche come installare Airpal in un cluster HDInsight Presto esistente.

HDInsight offre anche l'applicazione Starburst Presto per i cluster Apache Hadoop. Per altre informazioni, vedere [Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> I passaggi descritti in questo articolo richiedono un cluster HDInsight 3.5 Hadoop che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Che cos'è Presto?
[Presto](https://prestodb.io/overview.html) è un motore di query SQL a distribuzione veloce per Big Data. Presto è adatto per l'esecuzione di query interattive di petabyte di dati. Per altre informazioni sui componenti di Presto e il relativo funzionamento, vedere [Presto concepts](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst) (Concetti di Presto).

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuterà a isolare e risolvere i problemi correlati a questi componenti.
> 
> I componenti personalizzati, come Presto, ricevono un supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Questo supporto potrebbe risolvere il problema oppure è possibile che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È possibile ricorrere a molti siti di community. Ad esempio, il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com). 
>
> Anche per i progetti Apache sono disponibili siti specifici nel [sito Web di Apache](https://apache.org). Ad esempio, [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Installare Presto mediante azioni script

Questa sezione illustra come usare lo script di esempio quando si crea un nuovo cluster usando il portale di Azure: 

1. Iniziare a eseguire il provisioning di un cluster seguendo la procedura in [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Assicurarsi di creare il cluster usando il flusso di creazione del cluster **personalizzato**. Il cluster deve soddisfare i requisiti seguenti:

   * Deve essere un cluster Hadoop con HDInsight versione 3.6.

   * Deve usare Archiviazione di Azure come archivio dati. L'uso di Presto in un cluster con Azure Data Lake Storage come opzione di archiviazione non è ancora supportato.

     ![HDInsight, Personalizzate (dimensioni, impostazioni, app)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Nell'area **Impostazioni avanzate** selezionare **Azioni script**. Fornire le informazioni seguenti. È anche possibile scegliere l'opzione **Installa Presto** per il tipo di script:
   
   * **NAME**. Immettere un nome descrittivo per l'azione script.
   * **URI script Bash**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HEAD**. Selezionare questa opzione.
   * **LAVORO**. Selezionare questa opzione.
   * **ZOOKEEPER**. Lasciare vuota questa casella di controllo.
   * **PARAMETRI**. lasciare vuoto questo campo.


3. Nella parte inferiore dell'area **Azioni script** scegliere il pulsante **Seleziona** per salvare la configurazione. Infine, scegliere il pulsante **Seleziona** nella parte inferiore dell'area **Impostazioni avanzate** per salvare le informazioni di configurazione.

4. Continuare a eseguire il provisioning del cluster come descritto in [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando classica di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Usare Presto con HDInsight

Per usare Presto in un cluster HDInsight, procedere come segue:

1. Connettersi al cluster HDInsight usando SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Avviare la shell di Presto eseguendo il comando seguente:
   
    `presto --schema default`

3. Eseguire una query sulla tabella di esempio, **hivesampletable**, disponibile in tutti i cluster HDInsight per impostazione predefinita:
   
    `select count (*) from hivesampletable;`
   
    Per impostazione predefinita, i connettori [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) per Presto sono già configurati. Il connettore Hive è configurato per usare l'installazione Hive predefinita. Tutte le tabelle da Hive sono pertanto visibili automaticamente in Presto.

    Per altre informazioni, vedere la [documentazione su Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Usare Airpal con Presto

[Airpal](https://github.com/airbnb/airpal#airpal) è un'interfaccia per query basate su Web open source per Presto. Per altre informazioni su Airpal, vedere la [documentazione su Airpal](https://github.com/airbnb/airpal#airpal).

Seguire questa procedura per installare Airpal nel nodo perimetrale:

1. Connettersi tramite SSH al nodo head del cluster HDInsight in cui è installato Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una volta eseguita la connessione, eseguire il comando seguente:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Verrà visualizzato un output simile al codice JSON seguente:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Nell'output prendere nota del valore per la proprietà **value**. Questo valore è necessario durante l'installazione di Airpal nel nodo perimetrale del cluster. Dall'output precedente, il valore necessario è **10.0.0.12:9090**.

4. Usare [questo modello](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) per creare un nodo perimetrale del cluster HDInsight. Specificare i valori mostrati nello screenshot seguente.

    ![Distribuzione personalizzata](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Selezionare **Acquisto**.

6. Dopo l'applicazione delle modifiche alla configurazione del cluster, accedere all'interfaccia Web Airpal seguendo questa procedura nel [portale di Azure](https://portal.azure.com):

    1. Nel menu a sinistra selezionare **Tutti i servizi**.

    1. In **ANALISI** selezionare **Cluster HDInsight**.

    1. Selezionare il cluster nell'elenco, che aprirà la visualizzazione predefinita.

    1. Nella visualizzazione predefinita, in **Impostazioni**, selezionare **Applicazioni**.

        ![HDInsight, Applicazioni](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Nella pagina **App installate** individuare la voce della tabella per **airpal**. Selezionare **Portale**.

        ![App installate](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Quando richiesto, immettere le credenziali di amministratore specificate durante la creazione del cluster HDInsight basato su Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizzare un'installazione Presto nel cluster HDInsight

Per personalizzare l'installazione, seguire questa procedura:

1. Connettersi tramite SSH al nodo head del cluster HDInsight in cui è installato Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Apportare le modifiche di configurazione nel file `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Per altre informazioni sulla configurazione di Presto, vedere [Presto configuration options for YARN-based clusters](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) (Opzioni di configurazione di Presto per i cluster basati su YARN).

3. Arrestare e terminare l'istanza in esecuzione corrente di Presto:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Avviare una nuova istanza di Presto con la personalizzazione:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Attendere che la nuova istanza sia pronta. Prendere nota dell'indirizzo del coordinatore di Presto:

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generare dati di benchmark per i cluster HDInsight in cui è in esecuzione Presto

TPC-DS è lo standard del settore per la misurazione delle prestazioni di molti sistemi di supporto decisionale, compresi i sistemi di Big Data. È possibile usare Presto per generare i dati e confrontarli con i dati di benchmark di HDInsight. Per altre informazioni, vedere [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Passaggi successivi
* [Installare e usare Hue nei cluster Hadoop di HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente Web che semplifica la creazione, l'esecuzione e il salvataggio di processi Apache Pig e Hive.

* [Installare Apache Giraph nei cluster HDInsight Hadoop e usarlo per elaborare grafici su vasta scala](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Con Giraph è possibile eseguire l'elaborazione di grafi con Hadoop. Può essere usato anche con Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
