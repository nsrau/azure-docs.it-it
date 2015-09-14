<properties
    pageTitle="Sviluppo di azioni script con HDInsight| Microsoft Azure"
	description="Informazioni su come personalizzare i cluster Hadoop con Azione script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
    ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="larryfr"/>

# Sviluppo di azioni script con HDInsight

Le azioni script consentono di personalizzare i cluster Azure HDInsight specificando le impostazioni di configurazione del cluster durante l'installazione oppure installando servizi, strumenti o altri componenti software nel cluster.

> [AZURE.NOTE]Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Linux. Per informazioni sull'uso di azioni script con cluster basati su Windows, vedere [Sviluppo di azioni script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## Definizione di azioni script

Le azioni script sono script Bash eseguiti sui nodi del cluster durante il provisioning. Un'azione script viene eseguita come radice e fornisce diritti di accesso completo ai nodi del cluster.

È possibile usare l'azione script quando si effettua il provisioning di un cluster tramite il __portale di anteprima di Azure__, __Azure PowerShell__ o __HDInsight .NET SDK__.

Per una procedura dettagliata sulla personalizzazione di un cluster con azioni script, vedere [Personalizzare i cluster HDInsight con azioni script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster HDInsight, è opportuno seguire le procedure consigliate indicate di seguito:

- [Usare la versione di Hadoop](#bPS1)
- [Fornire collegamenti stabili alle risorse di script](#bPS2)
- [Assicurarsi che lo script di personalizzazione del cluster sia idempotente](#bPS3)
- [Verificare la disponibilità elevata dell'architettura del cluster](#bPS5)
- [Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure](#bPS6)
- [Scrivere informazioni in STDOUT e STDERR](#bPS7)
- [Salvare i file in formato ASCII con terminazioni di riga LF](#bps8)

### <a name="bPS1"></a>Usare la versione di Hadoop

Nelle diverse versioni di HDInsight sono installate versioni diverse di servizi e componenti di Hadoop. Se lo script prevede una versione specifica di un servizio o un componente, si dovrà usare lo script solo con la versione di HDInsight che include i componenti richiesti. Per trovare le informazioni sulle versioni dei componenti incluse in HDInsight, usare il documento relativo al [controllo delle versioni dei componenti di HDInsight](hdinsight-component-versioning.md).

### <a name="bPS2"></a>Fornire collegamenti stabili alle risorse di script

Gli utenti devono assicurarsi che tutti gli script e tutte le risorse usati dallo script rimangano disponibili per l'intero ciclo di vita del cluster stesso e che durante tale periodo le versioni di questi file non cambino. Queste risorse sono richieste in caso si debba ricreare l'immagine dei nodi nel cluster.

È consigliabile scaricare e archiviare tutti gli elementi in un account di archiviazione di Azure nella propria sottoscrizione.

> [AZURE.IMPORTANT]L'account di archiviazione usato deve essere quello predefinito per il cluster o un contenitore pubblico di sola lettura per qualsiasi altro account di archiviazione.

Gli esempi forniti da Microsoft, ad esempio, sono archiviati nell'account di archiviazione [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/), un contenitore pubblico di sola lettura gestito dal team di HDInsight.

### <a name="bPS3"></a>Assicurarsi che lo script di personalizzazione del cluster sia idempotente

È necessario tenere presente che l'immagine dei nodi di un cluster HDInsight verrà ricreata nel corso del ciclo di vita del cluster e che in quel caso verranno usati gli script di personalizzazione del cluster. Lo script deve essere progettato in modo che sia idempotente. In altri termini, quando viene ricreata l'immagine lo script deve assicurare che, a ogni esecuzione, il cluster venga riportato allo stesso stato.

Se, ad esempio, al momento della prima esecuzione uno script personalizzato installa un'applicazione in /usr/local/bin, a ogni esecuzione successiva dovrà verificare se l'applicazione esiste già nel percorso /usr/local/bin prima di procedere con altri passaggi.

### <a name="bPS5"></a>Verificare la disponibilità elevata dell'architettura del cluster

I cluster HDInsight basati su Linux forniscono due nodi head attivi all'interno del cluster e le azioni script vengono eseguite per entrambi i nodi. Se i componenti installati prevedono un solo nodo head, è necessario progettare uno script che installi il componente solo in uno dei due nodi del cluster. I nodi head sono denominati **headnode0** e **headnode1**.

> [AZURE.IMPORTANT]I servizi predefiniti installati come parte di HDInsight sono progettati per il failover tra i due nodi head, se necessario. Questa funzionalità non è tuttavia estesa ai componenti personalizzati installati tramite le azioni script. Se i componenti installati tramite un'azione script richiedono la disponibilità elevata, è necessario implementare un meccanismo di failover personalizzato per l'uso dei due nodi head disponibili.

### <a name="bPS6"></a>Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure

I componenti installati nel cluster possono avere una configurazione predefinita che usa l'archiviazione di Hadoop Distributed File System (HDFS). Quando si ricrea l'immagine di un cluster, il file system HDFS viene formattato e tutti i dati archiviati vengono persi. Modificare la configurazione usando invece l'archivio BLOB di Azure (WASB), la risorsa di archiviazione predefinita per il cluster che viene mantenuta anche quando il cluster viene eliminato.

Ad esempio, il codice seguente copia il file giraph-examples.jar dal file system locale in WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Scrivere informazioni in STDOUT e STDERR

Le informazioni scritte in STDOUT e STDERR vengono registrate e possono essere visualizzate dopo il provisioning del cluster tramite l'interfaccia utente Web di Ambari.

La maggior parte delle utilità e dei pacchetti di installazione scrivono già le informazioni in STDOUT e STDERR, tuttavia è possibile aggiungere altre opzioni di registrazione. Per inviare testo a STDOUT, usare `echo`. Ad esempio:

        echo "Getting ready to install Foo"

Per impostazione predefinita, `echo` invia la stinga a STDOUT. Per indirizzarla a STDERR, aggiungere `>&2` prima di `echo`. Ad esempio:

        >&2 echo "An error occured installing Foo"

Questo codice reindirizza le informazioni inviate a STDOUT (1 è il valore predefinito, quindi non indicato qui) a STDERR (2). Per altre informazioni sul reindirizzamento I/O, vedere [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Per altre informazioni sulla visualizzazione delle informazioni registrate tramite azioni script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

###<a name="bps8"></a> Salvare i file in formato ASCII con terminazioni di riga LF

Gli script Bash devono essere archiviati nel formato ASCII con righe terminate da LF. Se i file vengono archiviati nel formato UTF-8, che può includere un byte order mark all'inizio del file, oppure con terminazioni di riga CRLF, comuni negli editor di Windows, lo script non riuscirà con errori simili al seguente:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

## <a name="helpermethods"></a>Metodi helper per gli script personalizzati

I metodi di supporto degli script azione sono utilità che è possibile utilizzare durante la scrittura di script personalizzati. Questi metodi sono definiti nella pagina all'indirizzo [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) e possono essere inclusi negli script personalizzati nel modo seguente:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Questo codice rende disponibili gli helper seguenti per l'uso nello script personalizzato:

| Utilizzo dell'helper | Descrizione |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Scarica un file dall'URL di origine nel percorso file specificato. Per impostazione predefinita, non sovrascriverà un file esistente. |
| `untar_file TARFILE DESTDIR` | Estrae un file TAR (usando `-xf`) nella directory di destinazione. |
| `test_is_headnode` | Se viene eseguito su un nodo head del cluster restituisce 1; in caso contrario, 0. |
| `test_is_datanode` | Se il nodo corrente è un nodo dati (di lavoro) restituisce 1; in caso contrario, 0. |
| `test_is_first_datanode` | Se il nodo corrente è il primo nodo dati (di lavoro) (denominato workernode0) restituisce 1; in caso contrario, 0. |

## <a name="commonusage"></a>Modelli di utilizzo comuni

Questa sezione fornisce indicazioni sull'implementazione di alcuni dei modelli di utilizzo comuni che si potrebbero riscontrare durante la scrittura dello script personalizzato.

### Passaggio di parametri a uno script

In alcuni casi, lo script potrebbe richiedere l'uso di parametri. Ad esempio, per recuperare informazioni dall'API REST di Ambari, potrebbe essere necessaria la password amministratore per il cluster.

I parametri passati allo script sono noti come _parametri posizionali_ e sono assegnati a `$1` per il primo parametro, a `$2` per il secondo e così via. `$0` contiene il nome dello script stesso.

I valori passati allo script come parametri devono essere racchiusi tra virgolette singole ('), in modo che il valore passato sia considerato un valore letterale e il carattere tra virgolette, ad esempio '!', non riceva un trattamento speciale.

### Impostazioni delle variabili di ambiente

L'impostazione di una variabile di ambiente viene eseguita nel modo seguente:

    VARIABLENAME=value

Dove VARIABLENAME è il nome della variabile. Per accedere quindi alla variabile, usare `$VARIABLENAME`. Ad esempio, per assegnare un valore fornito da un parametro posizionale come variabile di ambiente denominata PASSWORD, usare la stringa seguente:

    PASSWORD=$1

Per il successivo accesso alle informazioni, si potrà quindi usare `$PASSWORD`.

Le variabili di ambiente impostate all'interno dello script sono disponibili solo nell'ambito dello script. In alcuni casi potrebbe essere necessario aggiungere variabili di ambiente a livello di sistema, che rimarranno persistenti dopo il completamento dello script. Di solito questo permette agli utenti che si connettono al cluster tramite SSH di usare i componenti installati dallo script. È possibile eseguire questa operazione aggiungendo la variabile di ambiente a `/etc/environment`. Ad esempio, il codice seguente aggiunge __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### Accedere alle posizioni in cui sono archiviati gli script personalizzati

Gli script usati per personalizzare un cluster devono trovarsi nell'account di archiviazione predefinito per il cluster oppure, se si trovano in un altro account di archiviazione, in un contenitore pubblico di sola lettura. Se lo script accede a risorse che si trovano altrove, queste devono essere in una posizione accessibile pubblicamente (almeno pubblica e di sola lettura). Ad esempio, è possibile scaricare un file nel cluster usando `download_file`.

L'archiviazione di un file in un account di archiviazione di Azure accessibile al cluster (ad esempio l'account di archiviazione predefinito) consentirà un accesso rapido, perché questo account di archiviazione si trova nella rete di Azure.

## <a name="deployScript"></a>Elenco di controllo per la distribuzione di un'azione script

Di seguito sono indicati i passaggi effettuati durante la preparazione della distribuzione degli script:

- Inserire i file che contengono gli script personalizzati in un percorso accessibile per i nodi del cluster durante la distribuzione. L'account di archiviazione può essere uno qualsiasi degli account predefiniti o aggiuntivi specificati durante la distribuzione del cluster oppure qualsiasi altro contenitore di archiviazione accessibile pubblicamente.

- Aggiungere controlli negli script per garantire che questi vengano eseguiti in modo idempotente per poter eseguire più volte lo script sullo stesso nodo.

- Usare una directory di file temporanei /tmp per conservare i file scaricati usati dagli script e quindi eliminarli dopo aver eseguito gli script.

- In caso di modifica delle impostazioni o dei file di configurazione del servizio Hadoop a livello di sistema operativo, è possibile riavviare i servizi HDInsight in modo che possano rilevare qualsiasi impostazione a livello di sistema operativo, ad esempio le variabili di ambiente impostate negli script.

## <a name="runScriptAction"></a>Come eseguire un'azione script

È possibile usare le azioni script per personalizzare i cluster HDInsight usando il portale di Azure, Azure PowerShell o HDInsight .NET SDK. Per le istruzioni, vedere [Come usare l'azione script](hdinsight-hadoop-customize-cluster-linux.md#howto).

## <a name="sampleScripts"></a>Esempi di script personalizzati

Microsoft fornisce script di esempio per installare i componenti in un cluster HDInsight. Gli script di esempio e le istruzioni su come usarli sono disponibili nei collegamenti seguenti:

- [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md).
- [Installare e usare Spark nei cluster HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Installare e usare R nei cluster Hadoop HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE]I documenti indicati nei collegamenti precedenti sono specifici per i cluster HDInsight basati su Linux. Per gli script che funzionano con HDInsight basato su Windows, vedere [Sviluppo di azioni script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md) o usare i collegamenti disponibili all'inizio di ogni articolo.

##Risoluzione dei problemi

Di seguito sono elencati gli errori che potrebbero essere visualizzati quando si usano script personalizzati:

__Errore__: `$'\r': command not found`. A volte seguito da `syntax error: unexpected end of file`.

_Causa_: questo errore si verifica quando le righe in uno script terminano con CRLF. I sistemi Unix prevedono unicamente LF come terminazione di riga.

Questo problema si verifica più spesso quando lo script viene creato in un ambiente Windows, perché CRLF è una terminazione di riga comune per molti editor di testo in Windows.

_Risoluzione_: se nell'editor di testo è disponibile come opzione, selezionare il formato Unix o LF come terminazione di riga. È anche possibile usare i comandi seguenti in un sistema Unix per cambiare CRLF in LF:

> [AZURE.NOTE]I comandi seguenti sono all'incirca equivalenti nel senso che cambiano le terminazioni di riga CRLF in LF. Selezionarne uno in base alle utilità disponibili nel proprio sistema.

| Comando | Note |
| ------- | ----- |
| `unix2dos -b INFILE` | Verrà creata una copia di backup del file originale con estensione BAK |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE conterrà una versione solo con le terminazioni LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Il file verrà modificato direttamente senza crearne uno nuovo |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE conterrà una versione solo con le terminazioni LF.

__Errore__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: questo errore si verifica quando lo script è stato salvato nel formato UTF-8 con un byte order mark (BOM).

_Risoluzione_: salvare il file in formato ASCII o UTF-8 senza un carattere BOM. È anche possibile usare il comando seguente in un sistema Linux o Unix per creare un nuovo file senza il carattere BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Per il comando precedente sostituire __INFILE__ con il file contenente il carattere BOM. __OUTFILE__ deve essere un nuovo nome file, che conterrà lo script senza il carattere BOM.

## <a name="seeAlso"></a>Vedere anche

[Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md)

<!---HONumber=September15_HO1-->