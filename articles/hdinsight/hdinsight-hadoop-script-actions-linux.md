---
title: Sviluppo di azioni script con HDInsight basato su Linux - Azure | Documentazione Microsoft
description: "Informazioni su come usare script Bash per personalizzare cluster HDInsight basati su Linux. La funzionalità di azione script in HDInsight consente di eseguire script durante o dopo la creazione del cluster. Gli script possono essere usati per modificare le impostazioni di configurazione del cluster o per installare software aggiuntivo."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 0cef360de3b7a9be01536b0ebe90769c89e7c432
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="script-action-development-with-hdinsight"></a>Sviluppo di azioni script con HDInsight

Informazioni su come personalizzare il cluster HDInsight tramite script Bash. Le azioni script consentono di personalizzare HDInsight durante o dopo la creazione del cluster.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Definizione di azioni script

Le azioni script sono script Bash eseguiti da Azure sui nodi del cluster per apportare modifiche alla configurazione o installare software. Un'azione script viene eseguita come radice e fornisce diritti di accesso completo ai nodi del cluster.

L'azione script può essere applicata usando i metodi seguenti:

| Usare questo metodo per applicare uno script... | Durante la creazione di un cluster... | In un cluster in esecuzione... |
| --- |:---:|:---:|
| Portale di Azure |✓  |✓ |
| Azure PowerShell |✓ |✓ |
| Interfaccia della riga di comando di Azure |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Modello di Azure Resource Manager |✓ |&nbsp; |

Per altre informazioni sull'uso di questi metodi per l'applicazione di azioni script, vedere [Personalizzare cluster HDInsight tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster HDInsight, è opportuno seguire le procedure consigliate indicate di seguito:

* [Usare la versione di Hadoop](#bPS1)
* [Usare la versione del sistema operativo](#bps10)
* [Fornire collegamenti stabili alle risorse di script](#bPS2)
* [Usare risorse precompilate](#bPS4)
* [Assicurarsi che lo script di personalizzazione del cluster sia idempotente](#bPS3)
* [Verificare la disponibilità elevata dell'architettura del cluster](#bPS5)
* [Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure](#bPS6)
* [Scrivere informazioni in STDOUT e STDERR](#bPS7)
* [Salvare i file in formato ASCII con terminazioni di riga LF](#bps8)
* [Usare la logica di ripetizione dei tentativi per il ripristino da errori temporanei](#bps9)

> [!IMPORTANT]
> Le azioni di script devono essere completate entro 60 minuti; in caso contrario il processo avrà esito negativo. Durante il provisioning dei nodi, lo script viene eseguito contemporaneamente ad altri processi di installazione e configurazione. In caso di concorrenza per risorse come il tempo di CPU o la larghezza di banda di rete, lo script può richiedere più tempo per completare l'operazione rispetto al tempo che impiegherebbe in un ambiente di sviluppo.

### <a name="bPS1"></a>Usare la versione di Hadoop

Nelle diverse versioni di HDInsight sono installate versioni diverse di servizi e componenti di Hadoop. Se lo script prevede una versione specifica di un servizio o un componente, si dovrà usare lo script solo con la versione di HDInsight che include i componenti richiesti. Per trovare informazioni sulle versioni dei componenti incluse in HDInsight, usare il documento relativo al [controllo delle versioni dei componenti di HDInsight](hdinsight-component-versioning.md) .

### <a name="bps10"></a> Usare la versione del sistema operativo

HDInsight basato su Linux si basa sulla distribuzione di Ubuntu Linux. Versioni diverse di HDInsight si basano su versioni differenti di Ubuntu e questo può influire sul comportamento dello script. HDInsight 3.4 e versioni precedenti si basano ad esempio su versioni di Ubuntu che usano Upstart. La versione 3.5 e le versioni superiori si basano su Ubuntu 16.04 che usa Systemd. Systemd e Upstart si basano su comandi diversi, quindi lo script deve essere scritto in modo da funzionare con entrambi.

Un'altra differenza importante tra HDInsight 3.4 e 3.5 è che `JAVA_HOME` punta ora a Java 8.

È possibile verificare la versione del sistema operativo con `lsb_release`. Il codice seguente illustra come determinare se lo script è in esecuzione su Ubuntu 14 o 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Lo script completo contenente questi frammenti è disponibile all'indirizzo https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Per la versione di Ubuntu usata da HDInsight, vedere il documento sulle [versioni dei componenti HDInsight](hdinsight-component-versioning.md).

Per comprendere le differenze tra Systemd e Upstart, vedere [Systemd per gli utenti di Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornire collegamenti stabili alle risorse di script

Lo script e le risorse associate devono rimanere disponibili per tutta la durata del cluster. Queste risorse sono necessarie se vengono aggiunti nuovi nodi al cluster durante operazioni di ridimensionamento.

È consigliabile scaricare e archiviare tutti gli elementi in un account di archiviazione di Azure nella propria sottoscrizione.

> [!IMPORTANT]
> L'account di archiviazione usato deve essere quello predefinito per il cluster o un contenitore pubblico di sola lettura per qualsiasi altro account di archiviazione.

Gli esempi forniti da Microsoft, ad esempio, vengono archiviati nell'account di archiviazione [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/). Si tratta di un contenitore pubblico e di sola lettura, gestito dal team di HDInsight.

### <a name="bPS4"></a>Usare risorse precompilate

Per ridurre il tempo necessario per eseguire lo script, evitare operazioni di compilazione delle risorse dal codice sorgente. Ad esempio, precompilare le risorse e archiviarle in un BLOB dell'account di archiviazione di Azure nello stesso data center di HDInsight.

### <a name="bPS3"></a>Assicurarsi che lo script di personalizzazione del cluster sia idempotente

Gli script devono essere idempotenti. Se lo script viene eseguito più volte, ogni volta deve riportare il cluster allo stato iniziale.

Uno script che modifica i file di configurazione, ad esempio, non deve aggiungere voci duplicate se viene eseguito più volte.

### <a name="bPS5"></a>Verificare la disponibilità elevata dell'architettura del cluster

I cluster HDInsight basati su Linux forniscono due nodi head attivi all'interno del cluster e le azioni script vengono eseguite per entrambi i nodi. Se i componenti da installare prevedono un solo nodo head, non installare i componenti in entrambi i nodi head.

> [!IMPORTANT]
> I servizi forniti nell'ambito di HDInsight sono progettati per supportare il failover tra i due nodi head, se necessario. Questa funzionalità non è estesa ai componenti personalizzati installati tramite azioni script. Se i componenti personalizzati richiedono una disponibilità elevata, è necessario implementare un meccanismo di failover personalizzato.

### <a name="bPS6"></a>Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure

I componenti installati nel cluster possono avere una configurazione predefinita che usa l'archiviazione di Hadoop Distributed File System (HDFS). HDInsight usa l'archiviazione di Azure o Azure Data Lake Store come risorsa di archiviazione predefinita, poiché entrambi forniscono un file system compatibile con HDFS che rende permanenti i dati anche se il cluster viene eliminato. In alcuni casi, è possibile che sia necessario configurare i componenti installati in modo da usare WASB o ADL anziché HDFS.

Per la maggior parte delle operazioni, tuttavia, non è necessario specificare il file system. Il codice seguente, ad esempio, copia il file giraph-examples.jar dal file system locale alla risorsa di archiviazione del cluster:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

In questo esempio, il comando `hdfs` usa in modo trasparente la risorsa di archiviazione del cluster predefinita. Per alcune operazioni, è necessario specificare l'URI, ad esempio `adl:///example/jars` per Data Lake Store o `wasb:///example/jars` per l'archiviazione di Azure.

### <a name="bPS7"></a>Scrivere informazioni in STDOUT e STDERR

HDInsight registra l'output dello script scritto in STDOUT e STDERR. È possibile visualizzare queste informazioni tramite l'interfaccia utente Web di Ambari.

> [!NOTE]
> Ambari è disponibile solo se il cluster viene creato correttamente. Se si usa un'azione script durante la creazione del cluster e la creazione ha esito negativo, vedere la sezione relativa alla risoluzione dei problemi nell'articolo [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) che illustra altri modi per accedere alle informazioni registrate.

Sebbene la maggior parte delle utilità e dei pacchetti di installazione scriva già le informazioni in STDOUT e STDERR, è possibile aggiungere altre opzioni di registrazione. Per inviare testo a STDOUT, usare `echo`. Ad esempio:

```bash
echo "Getting ready to install Foo"
```

Per impostazione predefinita, `echo` invia la stringa a STDOUT. Per indirizzarla a STDERR, aggiungere `>&2` prima di `echo`. Ad esempio:

```bash
>&2 echo "An error occurred installing Foo"
```

Questo codice reindirizza a STDERR (2) le informazioni scritte in STDOUT. Per altre informazioni sul reindirizzamento I/O, vedere [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Per altre informazioni sulla visualizzazione delle informazioni registrate tramite azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Salvare i file in formato ASCII con terminazioni di riga LF

Gli script Bash devono essere archiviati nel formato ASCII con righe terminate da LF. Se i file vengono archiviati in formato UTF-8 o usano CRLF come terminazione di riga, è possibile che abbiano esito negativo con l'errore seguente:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Usare la logica di ripetizione dei tentativi per il ripristino da errori temporanei

Quando si scaricano file, l'installazione di pacchetti tramite apt-get o altre azioni che trasmettono dati su Internet, l'azione potrebbe non riuscire a causa di errori di rete temporanei. Ad esempio, è possibile che sia in corso il failover a un nodo di backup della risorsa remota con la quale si sta comunicando.

Per rendere lo script resiliente agli errori temporanei, è possibile implementare la logica di ripetizione dei tentativi. La funzione seguente illustra come implementare la logica di ripetizione dei tentativi: prima che venga generato l'errore, ripete per tre volte il tentativo di eseguire l'operazione.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Gli esempi seguenti illustrano come usare questa funzione.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Metodi helper per gli script personalizzati

I metodi helper dell'azione script sono utilità che è possibile usare durante la scrittura di script personalizzati. Questi metodi sono contenuti nello script [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh). Usare il codice seguente per scaricarli e usarli nell'ambito dello script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

In uno script personalizzato possono essere usati gli helper seguenti:

| Utilizzo dell'helper | Descrizione |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Scarica un file dall'URI di origine al percorso file specificato. Per impostazione predefinita, non sovrascrive un file esistente. |
| `untar_file TARFILE DESTDIR` |Estrae un file TAR (usando `-xf`) nella directory di destinazione. |
| `test_is_headnode` |Se viene eseguito su un nodo head del cluster restituisce 1; in caso contrario, 0. |
| `test_is_datanode` |Se il nodo corrente è un nodo dati (di lavoro) restituisce 1; in caso contrario, 0. |
| `test_is_first_datanode` |Se il nodo corrente è il primo nodo dati (di lavoro), denominato workernode0, restituisce 1; in caso contrario, 0. |
| `get_headnodes` |Restituisce il nome di dominio completo dei nodi head nel cluster. I nomi sono delimitati da virgole. In caso di errore, viene restituita una stringa vuota. |
| `get_primary_headnode` |Ottiene il nome di dominio completo del nodo head primario. In caso di errore, viene restituita una stringa vuota. |
| `get_secondary_headnode` |Ottiene il nome di dominio completo del nodo head secondario. In caso di errore, viene restituita una stringa vuota. |
| `get_primary_headnode_number` |Ottiene il suffisso numerico del nodo head primario. In caso di errore, viene restituita una stringa vuota. |
| `get_secondary_headnode_number` |Ottiene il suffisso numerico del nodo head secondario. In caso di errore, viene restituita una stringa vuota. |

## <a name="commonusage"></a>Modelli di utilizzo comuni

Questa sezione fornisce indicazioni sull'implementazione di alcuni dei modelli di utilizzo comuni che si potrebbero riscontrare durante la scrittura dello script personalizzato.

### <a name="passing-parameters-to-a-script"></a>Passaggio di parametri a uno script

In alcuni casi, lo script potrebbe richiedere l'uso di parametri. Quando si usa l'API REST di Ambari, ad esempio, è possibile che sia necessaria la password amministratore per il cluster.

I parametri passati allo script sono noti come *parametri posizionali* e vengono assegnati a `$1` per il primo parametro, a `$2` per il secondo e così via. `$0` contiene il nome dello script stesso.

I valori passati allo script come parametri devono essere racchiusi tra virgolette singole ('), in modo che vengano considerati come valori letterali.

### <a name="setting-environment-variables"></a>Impostazioni delle variabili di ambiente

L'impostazione di una variabile di ambiente viene eseguita con l'istruzione seguente:

    VARIABLENAME=value

Dove VARIABLENAME è il nome della variabile. Per accedere alla variabile, usare `$VARIABLENAME`. Per assegnare un valore fornito da un parametro posizionale come variabile di ambiente denominata PASSWORD, ad esempio, usare l'istruzione seguente:

    PASSWORD=$1

Per il successivo accesso alle informazioni, si potrà quindi usare `$PASSWORD`.

Le variabili di ambiente impostate all'interno dello script sono disponibili solo nell'ambito dello script. In alcuni casi può essere necessario aggiungere variabili di ambiente a livello di sistema, che rimarranno persistenti dopo il completamento dello script. Per aggiungere variabili di ambiente a livello di sistema, aggiungere la variabile a `/etc/environment`. L'istruzione seguente, ad esempio, aggiunge `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accedere alle posizioni in cui sono archiviati gli script personalizzati

Gli script usati per la personalizzazione di un cluster devono essere archiviati in una delle posizioni seguenti:

* __Account di archiviazione di Azure__ associato al cluster.

* __Account di archiviazione aggiuntivo__ associato al cluster.

* __URI leggibile pubblicamente__, ad esempio un URL per accedere ai dati archiviati in OneDrive, Dropbox o altri servizi di hosting di file.

* __Account Azure Data Lake Store__ associato al cluster HDInsight. Per altre informazioni sull'uso di Azure Data Lake Store con HDInsight, vedere [Creare un cluster HDInsight con Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > L'entità servizio usata da HDInsight per accedere a Data Lake Store deve avere accesso in lettura allo script.

Anche le risorse usate dallo script devono essere disponibili pubblicamente.

L'archiviazione dei file in un account di archiviazione di Azure o in Azure Data Lake Store ne consentirà l'accesso in tempi rapidi, poiché si trovano entrambi nella rete di Azure.

> [!NOTE]
> Il formato URI usato per fare riferimento allo script varia a seconda del servizio in uso. Per gli account di archiviazione associati al cluster HDInsight, usare `wasb://` o `wasbs://`. Per gli URI leggibili pubblicamente, usare `http://` o `https://`. Per Data Lake Store, usare `adl://`.

### <a name="checking-the-operating-system-version"></a>Verifica della versione del sistema operativo

Le diverse versioni di HDInsight si basano su versioni specifiche di Ubuntu. Ci possono essere differenze tra le versioni del sistema operativo che è necessario verificare nello script. Può essere ad esempio necessario installare un file binario associato alla versione di Ubuntu.

Per verificare la versione del sistema operativo usare `lsb_release`. Lo script seguente, ad esempio, illustra come fare riferimento a uno specifico file tar, in base alla versione del sistema operativo:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Elenco di controllo per la distribuzione di un'azione script

Di seguito sono indicati i passaggi effettuati durante la preparazione della distribuzione degli script:

* Inserire i file che contengono gli script personalizzati in un percorso accessibile per i nodi del cluster durante la distribuzione, ad esempio la risorsa di archiviazione predefinita per il cluster. I file possono essere archiviati anche in servizi di hosting leggibili pubblicamente.
* Verificare che lo script sia idempotente, in modo che possa essere eseguito più volte nello stesso nodo.
* Usare una directory di file temporanei /tmp per conservare i file scaricati usati dagli script e quindi eliminarli dopo aver eseguito gli script.
* Nel caso in cui vengano modificate le impostazioni a livello di sistema operativo o i file di configurazione del servizio Hadoop, può essere opportuno riavviare i servizi HDInsight.

## <a name="runScriptAction"></a>Come eseguire un'azione script

È possibile usare azioni script per personalizzare i cluster HDInsight adottando uno dei metodi seguenti:

* Portale di Azure
* Azure PowerShell
* Modelli di Gestione risorse di Azure
* HDInsight .NET SDK

Per altre informazioni sull'utilizzo di ogni metodo, vedere [Come usare azioni script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Esempi di script personalizzati

Microsoft fornisce script di esempio per installare i componenti in un cluster HDInsight. Vedere i collegamenti seguenti per altre azioni di script di esempio.

* [Installare e usare Hue nei cluster HDInsight.](hdinsight-hadoop-hue-linux.md)
* [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Installare o aggiornare Mono nei cluster HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito sono elencati gli errori che potrebbero essere visualizzati quando si usano script personalizzati:

**Errore**: `$'\r': command not found`. A volte seguito da `syntax error: unexpected end of file`.

*Causa*: questo errore si verifica quando le righe di uno script terminano con CRLF. I sistemi Unix prevedono unicamente LF come terminazione di riga.

Questo problema si verifica più spesso quando lo script viene creato in un ambiente Windows, perché CRLF è una terminazione di riga comune per molti editor di testo in Windows.

*Risoluzione*: se nell'editor di testo è disponibile come opzione, selezionare il formato Unix o LF come terminazione di riga. È anche possibile usare i comandi seguenti in un sistema Unix per cambiare CRLF in LF:

> [!NOTE]
> I comandi seguenti sono all'incirca equivalenti nel senso che cambiano le terminazioni di riga CRLF in LF. Selezionarne uno in base alle utilità disponibili nel proprio sistema.

| Comando | Note |
| --- | --- |
| `unix2dos -b INFILE` |Viene creata una copia di backup del file originale con estensione BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contiene una versione solo con le terminazioni LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica direttamente il file |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contiene una versione solo con le terminazioni LF. |

**Errore**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: questo errore si verifica quando lo script è stato salvato in formato UTF-8 con un byte order mark (BOM).

*Risoluzione*: salvare il file in formato ASCII o UTF-8 senza un carattere BOM. È anche possibile usare il comando seguente in un sistema Linux o Unix per creare un file senza il carattere BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Sostituire `INFILE` con il file contenente il carattere BOM. `OUTFILE` deve essere un nuovo nome di file, contenente lo script senza il carattere BOM.

## <a name="seeAlso"></a>Passaggi successivi

* Informazioni su come [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md)
* Per informazioni sulla creazione di applicazioni .NET che gestiscono HDInsight, vedere [Riferimento .NET per HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* Per informazioni su come usare REST per eseguire azioni di gestione nei cluster HDInsight, vedere l' [API REST del provider di risorse HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) .
