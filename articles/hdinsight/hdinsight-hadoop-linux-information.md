---
title: Suggerimenti per l&quot;uso di Hadoop in HDInsight basato su Linux - Azure | Documentazione Microsoft
description: "Ottenere suggerimenti di implementazione per l’uso di cluster HDInsight (Hadoop) basati su Linux in un ambiente Linux familiare, in esecuzione nel cloud di Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 79e122beb0f31c46bbb9951a2dee223de4a77e1f
ms.lasthandoff: 04/12/2017


---
# <a name="information-about-using-hdinsight-on-linux"></a>Informazioni sull'uso di HDInsight in Linux

I cluster Azure HDInsight mettono a disposizione Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

In molti passaggi di questo documento vengono usate le utilità seguenti che devono essere installate nel sistema.

* [cURL](https://curl.haxx.se/) : consente di comunicare con servizi basati su Web
* [jq](https://stedolan.github.io/jq/) : consente di analizzare i documenti JSON
* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (anteprima): consente di gestire in remoto i servizi di Azure

## <a name="users"></a>Utenti

A meno che non sia [aggiunto al dominio](hdinsight-domain-joined-introduction.md), HDInsight deve essere considerato un sistema a **utente singolo**. Con il cluster viene creato un singolo account utente SSH, con autorizzazioni a livello di amministratore. Possono essere creati altri account SSH, che avranno sempre l'accesso di amministratore al cluster.

HDInsight aggiunto al dominio offre il supporto per più utenti e impostazioni di autorizzazioni e ruoli più granulari. Per altre informazioni, vedere [Manage Domain-joined HDInsight clusters](hdinsight-domain-joined-manage.md) (Gestire cluster HDInsight aggiunti al dominio).

## <a name="domain-names"></a>Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster da Internet è **&lt;nome cluster>.azurehdinsight.net** o (solo per SSH) **&lt;nome cluster-ssh>.azurehdinsight.net**.

Internamente, ogni nodo del cluster ha un nome assegnato durante la configurazione del cluster. Per individuare i nomi del cluster, vedere la pagina **Host** nell'interfaccia utente Web Ambari. È anche possibile usare il codice seguente per restituire un elenco di host dall'API REST Ambari:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Sostituire **PASSWORD** con la password dell'account amministratore e **CLUSTERNAME** con il nome del cluster. Questo comando restituisce un documento JSON che contiene un elenco degli host nel cluster, quindi jq estrae il valore dell'elemento `host_name` per ogni host del cluster.

Se è necessario trovare il nome del nodo per un servizio specifico, è possibile eseguire una query in Ambari per tale componente. Ad esempio, per trovare gli host per il nodo con nome HDFS, usare il comando seguente:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Questo comando restituisce un documento JSON che descrive il servizio e quindi jq estrae solo il valore `host_name` per gli host.

## <a name="remote-access-to-services"></a>Accesso remoto ai servizi

* **Ambari (Web)**: https://&lt;nomecluster>.azurehdinsight.net

    Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster, quindi accedere ad Ambari. È necessario eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.

    L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

    > [!IMPORTANT]
    > Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non accessibile pubblicamente, è possibile ricevere errori di "server non trovato" se si tenta di accedere ad alcune funzionalità tramite Internet.
    >
    > Per usare le funzionalità complete dell'interfaccia utente Web di Ambari, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**: https://&lt;nomecluster>.azurehdinsight.net/ambari

    > [!NOTE]
    > Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
    >
    > L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)**: https://&lt;nomecluster>.azurehdinsight.net/templeton

    > [!NOTE]
    > Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
    >
    > L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** - &lt;nome cluster>-ssh.azurehdinsight.net sulla porta 22 o 23. La porta 22 viene utilizzata per connettersi al nodo head primario, mentre la porta 23 viene utilizzata per connettersi a quello secondario. Per maggiori informazioni sui nodi head, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH da un nodo head.

## <a name="file-locations"></a>Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in `/usr/hdp`. La directory contiene le sottodirectory seguenti:

* **2.2.4.9-1**: questa directory è denominata per la versione di Hortonworks Data Platform usata da HDInsight, quindi il numero il cluster potrebbe essere diverso da quelle elencato qui.
* **main**: questa directory contiene collegamenti alle sottodirectory sotto la directory **2.2.4.9-1**. Questa directory esiste per consentire di non dover digitare un numero di versione (che potrebbe cambiare) ogni volta che si vuole accedere a un file.

I dati di esempio e i file con estensione jar sono disponibili nel file system Hadoop Distributed File System (HDFS) in `/example` e `/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, Archiviazione di Azure e Data Lake Store

Nella maggior parte delle distribuzioni di Hadoop, il file system HDFS è supportato dall'archiviazione locale nei computer del cluster. Sebbene l'uso di sistema locale sia efficiente, può essere costoso per una soluzione basata su cloud dove viene addebitata una tariffa oraria o al minuto per le risorse di calcolo.

HDInsight usa i BLOB in Archiviazione di Azure o Azure Data Lake Store come archivio predefinito. Questo servizio offre i seguenti vantaggi:

* Archiviazione a lungo termine economica
* Accessibilità da servizi esterni, ad esempio siti Web, utilità di caricamento e download di file, SDK di linguaggi diversi e Web browser

> [!WARNING]
> HDInsight supporta solo account di archiviazione di Azure __per utilizzo generico__. Non supporta attualmente il tipo di account di __archiviazione BLOB__.

Un account di Archiviazione di Azure può contenere fino a 4,75 TB, anche se ogni BLOB (o file, da una prospettiva HDInsight) può arrivare fino a 195 GB. Azure Data Lake Store è scalabile in modo dinamico fino a contenere miliardi di file, con singoli file di dimensioni superiori a petabyte. Per altre informazioni, leggere gli articoli di approfondimento sui [BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/understanding-block-blobs--append-blobs--and-page-blobs) e su [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Quando si usa Archiviazione di Azure o Data Lake Store, non è necessario eseguire alcuna operazione speciale da HDInsight per accedere ai dati. Ad esempio, il comando seguente elenca i file della cartella `/example/data`, indipendentemente dal fatto che sia disponibile in Archiviazione di Azure o in Data Lake Store:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI e schema

Alcuni comandi richiedono di specificare lo schema come parte dell'URI quando si accede a un file. Ad esempio, il componente Storm-HDFS richiede di specificare lo schema. Quando si usa un archivio non predefinito (aggiunto al cluster come spazio di archiviazione "aggiuntivo"), è sempre necessario usare lo schema come parte dell'URI.

Quando si usa __Archiviazione di Azure__, usare uno degli schemi URI seguenti:

* `wasb:///`: per accedere allo spazio di archiviazione predefinito usando la comunicazione non crittografata.

* `wasbs:///`: per accedere allo spazio di archiviazione predefinito usando la comunicazione crittografata.

* `wasbs://<container-name>@<account-name>.blob.core.windows.net/`: usato durante la comunicazione con un account di archiviazione non predefinito, ad esempio quando si dispone di un account di archiviazione aggiuntivo o quando si accede a dati archiviati in un account di archiviazione pubblicamente accessibile.

Quando si usa __Data Lake Store__, usare uno degli schemi URI seguenti:

* `adl:///`: per accedere all' archivio Data Lake predefinito per il cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: usato durante la comunicazione con un archivio Data Lake non predefinito o per accedere a dati esterni alla directory radice del cluster HDInsight.

> [!IMPORTANT]
> Quando si usa Data Lake Store come archivio predefinito per HDInsight, è necessario specificare un percorso all'interno dell'archivio da usare come radice per l'archiviazione HDInsight. Il percorso predefinito è `/clusters/<cluster-name>/`.
>
> Quando si usa `/` o `adl:///` per accedere ai dati, è possibile accedere solo ai dati memorizzati nella directory radice del cluster, ad esempio `/clusters/<cluster-name>/`. Per accedere ai dati in un punto qualsiasi dell'archivio, usare il formato `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Archivio usato dal cluster

Ambari consente di recuperare le informazioni relative alla configurazione di archiviazione predefinita per il cluster. Usare il comando seguente per recuperare le informazioni di configurazione HDFS tramite curl e filtrarle tramite [jq](https://stedolan.github.io/jq/):

```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Viene restituita la prima configurazione applicata al server (`service_config_version=1`) che contiene queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Il comando restituisce un valore simile al seguente:

* `wasbs://<container-name>@<account-name>.blob.core.windows.net` se si usa un account di archiviazione di Azure.

    Il nome dell'account è il nome dell'account di archiviazione di Azure, mentre il nome del contenitore è il contenitore BLOB che è la radice del cluster di archiviazione.

* `adl://home` se si usa Azure Data Lake Store. Per recuperare il nome dell'archivio Data Lake, usare la chiamata REST seguente:

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Questo comando restituisce il nome host seguente: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Per recuperare la directory che nell'archivio è la radice di HDInsight, usare la chiamata REST seguente:

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Il comando restituisce un percorso simile al seguente: `/clusters/<hdinsight-cluster-name>/`.

Per individuare le informazioni di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/)selezionare il cluster HDInsight.

2. Nella sezione **Proprietà** selezionare **Account di archiviazione**. Vengono visualizzate le informazioni di archiviazione del cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Come accedere ai file dall'esterno di HDInsight

Esistono vari modi per accedere ai dati dall'esterno del cluster HDInsight. Di seguito sono indicati alcuni collegamenti a utilità e SDK da usare per lavorare con i dati:

Se si usa __Archiviazione di Azure__, vedere i collegamenti seguenti per informazioni sulle modalità di accesso ai dati:

* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): comandi dell'interfaccia della riga di comando per l'uso con Azure. Dopo l'installazione, usare il comando `az storage` per informazioni sull'uso dell'archiviazione o `az storage blob` per i comandi specifici dei BLOB.
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): uno script Python per l'uso con i BLOB in Archiviazione di Azure.
* Vari SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se si usa __Azure Data Lake Store__, vedere i collegamenti seguenti per informazioni sulle modalità di accesso ai dati:

* [Web browser](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfaccia della riga di comando di Azure](../data-lake-store/data-lake-store-get-started-cli.md)
* [API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Strumenti di Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Ridimensionamento del cluster

La funzionalità di ridimensionamento del cluster consente di modificare il numero di nodi dati usati da un cluster senza doverlo eliminare e ricreare. È possibile eseguire operazioni di ridimensionamento mentre altri processi sono in esecuzione nel cluster.

L'operazione di ridimensionamento può influire sui tipi di cluster come indicato di seguito:

* **Hadoop**: durante la riduzione del numero di nodi in un cluster, alcuni servizi nel cluster vengono riavviati. È quindi possibile che al termine dell'operazione di ridimensionamento, i processi in esecuzione o in sospeso abbiano esito negativo. In questo caso, inviare nuovamente i processi una volta completata l'operazione.
* **HBase**: i server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. Per bilanciare manualmente i server a livello di area, seguire questa procedura:

    1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Usare il codice seguente per avviare la shell HBase:

            hbase shell

    3. Una volta caricata la shell HBase, usare il codice seguente per bilanciare manualmente i server a livello di area:

            balancer

* **Storm**: al termine dell'operazione di ridimensionamento, ribilanciare qualsiasi topologia Storm in esecuzione. In questo modo, la topologia può rettificare le impostazioni di parallelismo in base al nuovo numero di nodi nel cluster. Per ribilanciare le topologie in esecuzione, usare una delle opzioni seguenti:

    * **SSH**: connettersi al server e usare il comando seguente per ribilanciare una topologia:

            storm rebalance TOPOLOGYNAME

        È anche possibile specificare parametri per eseguire l'override degli hint di parallelismo forniti in origine dalla topologia. Ad esempio, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` riconfigura la topologia con 5 processi di lavoro, 3 esecutori per il componente blue-spout e 10 esecutori per il componente yellow-bolt.

    * **Interfaccia utente Storm**: usare la procedura seguente per ribilanciare una topologia usando l'interfaccia utente Storm.

        1. Aprire **https://CLUSTERNAME.azurehdinsight.net/stormui** nel Web browser, dove CLUSTERNAME corrisponde al nome del cluster Storm. Se richiesto, immettere il nome amministratore (admin) del cluster HDInsight e la password specificata durante la creazione del cluster.
        2. Selezionare la topologia da ribilanciare e quindi fare clic sul pulsante **Rebalance** (Ribilancia). Specificare il ritardo prima dell'esecuzione dell'operazione di ribilanciamento.

Per informazioni specifiche sul ridimensionamento del cluster HDInsight, vedere:

* [Gestire cluster Hadoop in HDInsight con il portale di Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestire cluster Hadoop in HDInsight usando Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Come si installa Hue (o un altro componente Hadoop)?

HDInsight è un servizio gestito. Se Azure rileva un problema con il cluster, è possibile eliminare il nodo con l'errore e creare un nodo per sostituirlo. Se si esegue manualmente l'installazione degli elementi nel cluster, questi non vengono salvati in modo permanente quando si esegue questa operazione... Usare invece le [azioni script di HDInsight](hdinsight-hadoop-customize-cluster.md). Un'azione script può essere usata per apportare le modifiche seguenti:

* Installare e configurare un servizio o un sito Web, ad esempio Spark o Hue.
* Installare o configurare un componente che richiede modifiche di configurazione in più nodi del cluster, ad esempio una variabile di ambiente necessaria, la creazione di una directory di registrazione o la creazione di un file di configurazione.

Le azioni script sono script Bash eseguiti durante il provisioning del cluster e che possono essere usati per installare e configurare componenti aggiuntivi nel cluster. Sono disponibili script di esempio per installare i componenti seguenti:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph,](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>File con estensione jar

Alcune tecnologie Hadoop vengono fornite in file con estensione jar indipendenti contenenti funzioni usate come parte di un processo MapReduce o dall'interno di Pig o Hive. Sebbene sia possibile installarli usando azioni script, spesso non richiedono alcuna installazione e possono essere caricati nel cluster dopo il provisioning ed essere usati direttamente. Per assicurarsi che il componente venga mantenuto dopo la nuova creazione dell'immagine del cluster, è possibile archiviare il file nella risorsa di archiviazione predefinita per il cluster (WASB o ADL).

Se ad esempio si desidera usare l'ultima versione di [DataFu](http://datafu.incubator.apache.org/), è possibile scaricare un file con estensione jar contenente il progetto e caricarlo nel cluster HDInsight. Seguire quindi la documentazione di DataFu per informazioni sull'uso da Pig o Hive.

> [!IMPORTANT]
> Alcuni componenti che sono file con estensione jar autonomi vengono forniti con HDInsight, ma non sono presenti nel percorso. Se si desidera un componente specifico, è possibile usare il comando seguente per cercarlo nel cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Viene restituito il percorso dei file con estensione jar corrispondenti.

Se si vuole usare una versione differente rispetto a quella fornita con il cluster, è possibile eseguie l'upload di una nuova versione del componente e provare a usarla nei processi.

> [!WARNING]
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Per i progetti Apache sono anche disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la migrazione da HDInsight basato su Windows a HDInsight basato su Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

