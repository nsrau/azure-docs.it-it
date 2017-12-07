---
title: Eseguire la migrazione da HDInsight basato su Windows a HDInsight basato su Linux - Azure| Microsoft Docs
description: Informazioni sulla migrazione da un cluster HDInsight basato su Windows a un cluster HDInsight basato su Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: 764a41dc9e890de85c3bfab3d2f78d5a07b39dff
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrare da un cluster HDInsight basato su Windows a un cluster basato su Linux

Questo documento illustra in dettaglio le differenze tra HDInsight in Windows e Linux. Fornisce inoltre materiale sussidiario su come eseguire la migrazione dei carichi di lavoro esistenti in un cluster basato su Linux.

Anche se HDInsight basato su Windows fornisce un modo semplice per usare Hadoop nel cloud, potrebbe essere necessario eseguire la migrazione a un cluster basato su Linux. Ad esempio, per sfruttare i vantaggi di strumenti e tecnologie basati su Linux che sono necessari per la soluzione. Molti elementi nell'ecosistema Hadoop vengono sviluppati in sistemi basati su Linux e potrebbero non essere compatibili con HDInsight basato su Windows. Molti libri, video e altre forme di materiale didattico prevedono che si usi un sistema Linux quando si lavora con Hadoop.

> [!NOTE]
> I cluster HDInsight usano il supporto a lungo termine Ubuntu (LTS) come sistema operativo per i nodi del cluster. Per informazioni sulla versione di Ubuntu disponibile con HDInsight e sulle versioni degli altri componenti, vedere le [versioni dei componenti HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Attività di migrazione

Il flusso di lavoro generale per la migrazione è il seguente:

![Diagramma del flusso di lavoro della migrazione](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leggere interamente questo documento per comprendere le modifiche che potrebbero essere necessarie durante la migrazione.

2. Creare un cluster basato su Linux come ambiente di test/controllo qualità. Per altre informazioni sulla creazione di un cluster basato su Linux, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copiare processi esistenti, origini dati e sink nel nuovo ambiente.

4. Eseguire il test di convalida per assicurarsi che i processi funzionino come previsto nel nuovo cluster.

Dopo avere verificato che tutto funzioni come previsto, pianificare i tempi di inattività per la migrazione. Durante questo periodo di inattività, eseguire le operazioni seguenti:

1. Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster, ad esempio se i dati sono archiviati direttamente in un nodo head.

2. Eliminare il cluster basato su Windows.

3. Creare un cluster basato su Linux con lo stesso archivio dati predefinito usato dal cluster basato su Windows. Il nuovo cluster basato su Linux può continuare a lavorare con i dati di produzione esistenti.

4. Importare i dati temporanei di cui è stata eseguita una copia di backup.

5. Avviare processi/continuare l'elaborazione con il nuovo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copiare i dati nell'ambiente di test

Esistono molti metodi per copiare dati e processi, ma i due metodi illustrati in questa sezione sono i più semplici per spostare direttamente i file in un cluster di prova.

#### <a name="hdfs-copy"></a>Copia HDFS

Usare la procedura seguente per copiare i dati dal cluster di produzione al cluster di test. In queste procedure viene usata l'utilità `hdfs dfs` inclusa in HDInsight.

1. Individuare le informazioni dell'account di archiviazione e del contenitore predefinito per il cluster esistente. Nell'esempio seguente viene usato PowerShell per recuperare queste informazioni:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Per creare un ambiente di test, seguire la procedura riportata nel documento Creare cluster basati su Linux in HDInsight. Prima di creare il cluster, interrompersi e selezionare **Configurazione facoltativa**.

3. Nella sezione Configurazione facoltativa selezionare **Account di archiviazione collegati**.

4. Selezionare **Aggiungi una chiave di archiviazione**e, quando richiesto, selezionare l'account di archiviazione restituito dallo script PowerShell nel passaggio 1. Fare clic su **Seleziona** in ogni sezione. Al termine, creare il cluster.

5. Dopo aver creato il cluster, connettersi tramite **SSH** Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Dalla sessione SSH usare il comando seguente per copiare i file dall'account di archiviazione collegato al nuovo account di archiviazione predefinito. Sostituire CONTAINER con le informazioni sul contenitore restituite da PowerShell. Sostituire __ACCOUNT__ con il nome dell'account. Sostituire il percorso dei dati con il percorso di un file di dati.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Se la struttura della directory che contiene i dati non esiste nell'ambiente di test, è possibile crearla usando il comando seguente:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    L'opzione `-p` consente di creare tutte le directory nel percorso.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Copia diretta tra i BLOB in Archiviazione di Azure

In alternativa si può usare il cmdlet `Start-AzureStorageBlobCopy` di Azure PowerShell per copiare i BLOB tra gli account di archiviazione all'esterno di HDInsight. Per altre informazioni, vedere la sezione Come gestire i BLOB di Azure del documento Uso di Azure PowerShell con Archiviazione di Azure.

## <a name="client-side-technologies"></a>Tecnologie lato client

Le tecnologie lato client, ad esempio i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md) o [.NET SDK per Hadoop](https://hadoopsdk.codeplex.com/) continuano a usare i cluster basati su Linux. Queste tecnologie si basano sulle stesse API REST dei tipi di cluster del sistema operativo.

## <a name="server-side-technologies"></a>Tecnologie lato server

La tabella seguente contiene indicazioni sulla migrazione dei componenti lato server specifici di Windows.

| Se si usa questa tecnologia... | Eseguire questa operazione... |
| --- | --- |
| **PowerShell** (script sul lato server, incluse le Azioni script usate durante la creazione del cluster) |Riscriverli come script di Bash. Per le azioni script vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md) e [Sviluppo di azioni script con HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md). |
| **Interfaccia della riga di comando di Azure** (script lato server) |Anche se l'interfaccia della riga di comando di Azure è disponibile in Linux, non è preinstallata nei nodi head del cluster HDInsight. Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0). |
| **Componenti .NET** |.NET è supportato nei cluster HDInsight basati su Linux tramite [Mono](https://mono-project.com). Per ulteriori informazioni, vedere [Eseguire la migrazione per le soluzioni .NET per HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Componenti di Win32 o altre tecnologie esclusive di Windows** |La procedura dipende dal componente o dalla tecnologia. È possibile trovare una versione compatibile con Linux. In caso contrario, è necessario trovare una soluzione alternativa o riscrivere questo componente. |

> [!IMPORTANT]
> L'SDK di gestione di HDInsight non è completamente compatibile con Mono. Non usarlo come parte delle soluzioni distribuite nel cluster HDInsight.

## <a name="cluster-creation"></a>Creazione del cluster

Questa sezione illustra le differenze nella creazione del cluster.

### <a name="ssh-user"></a>Utente SSH

I cluster HDInsight basati su Linux usano il protocollo **Secure Shell (SSH)** per fornire l'accesso remoto ai nodi del cluster. A differenza dei desktop remoti per i cluster basati Windows, la maggior parte dei client SSH non offrono un'esperienza utente con interfaccia grafica. Al contrario, i client SSH offrono una riga di comando che consente di eseguire comandi nel cluster. Alcuni client, ad esempio [MobaXterm](http://mobaxterm.mobatek.net/), offrono un browser grafico per il file system oltre a una riga di comando remota.

Durante la creazione del cluster è necessario specificare un utente SSH e una **password** oppure un **certificato di chiave pubblica** per l'autenticazione.

È consigliabile usare il certificato di chiave pubblica perché è più sicuro rispetto alla password. L'autenticazione del certificato genera una coppia di chiavi pubblica/privata firmata e fornisce la chiave pubblica durante la creazione del cluster. Durante la connessione al server tramite SSH, la chiave privata del client consente l'autenticazione per la connessione.

Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalizzazione cluster

**Azioni script** usate con i cluster basati su Linux devono essere scritte nello script Bash. I cluster basati su Linux possono usare azioni script durante o dopo la creazione del cluster. Per altre informazioni vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md) e [Sviluppo di azioni script con HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md).

Un'altra funzionalità di personalizzazione è **bootstrap**, che per i cluster basati su Windows consente di specificare la posizione di librerie aggiuntive da usare con Hive. Dopo la creazione del cluster, queste librerie sono automaticamente disponibili per l'uso con le query Hive, senza la necessità di usare `ADD JAR`.

Bootstrap per i cluster basati su Linux non offre questa funzionalità. Usare invece l'azione script documentata nell'articolo [Aggiungere librerie Hive durante la creazione del cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Reti virtuali

I cluster HDInsight basati su Windows funzionano soltanto con le reti virtuali classiche, mentre i cluster HDInsight basati su Linux richiedono le reti virtuali di gestione risorse. Se nella rete virtuale classica sono presenti risorse a cui si deve connettere il cluster HDInsight basato su Linux, vedere [Connessione di una rete virtuale classica a una rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Per altre informazioni sui requisiti di configurazione, vedere il documento [Estendere le funzionalità di HDInsight usando una rete virtuale](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Gestione e monitoraggio

Molte interfacce utente Web usate con HDInsight basato su Windows, ad esempio l'interfaccia di Cronologia processo o YARN, sono disponibili tramite Ambari. La vista Hive di Ambari rappresenta inoltre un modo per eseguire query Hive usando il browser Web. L'interfaccia utente Web di Ambari è disponibile in ogni cluster basato su Linux all'indirizzo https://NOMECLUSTER.azurehdinsight.net.

Per altre informazioni sull'uso di Ambari, vedere i documenti seguenti:

* [Web Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Avvisi di Ambari

Ambari offre un sistema di avvisi in grado di indicare i potenziali problemi con il cluster. Gli avvisi vengono visualizzati come voci rosse o gialle nell'interfaccia utente Web Ambari, ma possono anche essere recuperati tramite l'API REST.

> [!IMPORTANT]
> Gli avvisi Ambari indicano che *potrebbe* esserci un problema e non che *è* presente un problema. Ad esempio, un avviso potrebbe indicare che HiveServer2 non è accessibile anche se è possibile accedervi normalmente.
>
> Molti avvisi vengono implementati come query basate su intervalli di tempo nell'ambito di un servizio e attendono una risposta entro un intervallo di tempo specifico. L'avviso pertanto non significa necessariamente che il servizio è inattivo, bensì che non ha restituito risultati entro l'intervallo di tempo previsto.

## <a name="file-system-locations"></a>Percorsi del file system

Il file system del cluster Linux è strutturato diversamente rispetto ai cluster HDInsight basati su Windows. Usare la tabella seguente per individuare i file usati comunemente.

| Devo cercare... | Si trova... |
| --- | --- |
| Configurazione |`/etc`. Ad esempio, `/etc/hadoop/conf/core-site.xml` |
| File di log |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Qui sono presenti due directory: una è la versione HDP corrente, l'altra è `current`. La directory `current` contiene i collegamenti simbolici a file e directory all'interno della directory dei numeri di versione. La directory `current` è un modo pratico per accedere ai file HDP, poiché il numero di versione cambia non appena viene aggiornata la versione di HDP. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

In genere, se si conosce il nome del file, è possibile usare il comando seguente da una sessione SSH per trovare il percorso del file:

    find / -name FILENAME 2>/dev/null

È possibile usare anche caratteri jolly con il nome del file. Ad esempio, `find / -name *streaming*.jar 2>/dev/null` restituisce il percorso dei file con estensione jar contenenti la parola 'streaming' nel nome del file.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig e MapReduce

I carichi di lavoro di Pig e MapReduce sono simili ai cluster basati su Linux. Tuttavia, i cluster HDInsight basati su Linux possono essere creati usando versioni più recenti di Hadoop, Hive e Pig. Queste differenze di versione potrebbero introdurre modifiche nel funzionamento delle soluzioni esistenti. Per altre informazioni sulle versioni dei componenti compresi in HDInsight, vedere [Controllo delle versioni dei componenti di HDInsight](hdinsight-component-versioning.md).

HDInsight basato su Linux non offre la funzionalità di desktop remoto. In alternativa, è possibile usare SSH per connettersi in remoto ai nodi head del cluster. Per altre informazioni, vedere i documenti seguenti:

* [Usare Hive con SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Usare Pig con SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Usare MapReduce con SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Se si usa un metastore di Hive esterno, è necessario eseguire il backup del metastore prima di usarlo con HDInsight basato su Linux. HDInsight basato su Linux è disponibile nelle versioni più recenti di Hive, che possono presentare problemi di incompatibilità con i metastore creati nelle versioni precedenti.

Il grafico seguente fornisce indicazioni sulla migrazione dei carichi di lavoro di Hive.

| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| **Editor Hive** |[vista Hive in Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` per abilitare Tez |Tez è il motore di esecuzione predefinito per i cluster basati su Linux, pertanto l'istruzione set non è più necessaria. |
| Funzioni definite dall'utente C# | Per informazioni sulla convalida dei componenti di C# con HDInsight basato su Linux, vedere [Eseguire la migrazione delle soluzioni .NET a HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| script o file CMD nel server richiamati nell'ambito di un processo Hive |gli script Bash |
| `hive` dal desktop remoto |Usare [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) o [Hive da una sessione SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| Funzioni definite dall'utente C# | Per informazioni sulla convalida dei componenti di C# con HDInsight basato su Linux, vedere [Eseguire la migrazione delle soluzioni .NET a HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Script o file CMD nel server richiamati nell'ambito di un processo Pig |gli script Bash |

### <a name="mapreduce"></a>MapReduce

| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| Componenti di mapping e riduttore C# | Per informazioni sulla convalida dei componenti di C# con HDInsight basato su Linux, vedere [Eseguire la migrazione delle soluzioni .NET a HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| script o file CMD nel server richiamati nell'ambito di un processo Hive |gli script Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Se si usa un metastore di Oozie esterno, è necessario eseguire il backup del metastore prima di usarlo con HDInsight basato su Linux. HDInsight basato su Linux è disponibile nelle versioni più recenti di Oozie, che possono presentare problemi di incompatibilità con i metastore creati nelle versioni precedenti.

I flussi di lavoro di Oozie consentono le azioni shell. Le azioni shell usano la shell predefinita per il sistema operativo per poter eseguire i comandi della riga di comando. Se si dispone di flussi di lavoro di Oozie che si basano su shell di Windows, è necessario riscrivere i flussi di lavoro affinché si basino su un ambiente della shell di Linux (Bash). Per ulteriori informazioni sull'uso di azioni shell con Oozie, vedere [Oozie shell action extension](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html) (Estensioni dell'azione shell di Oozie).

Se si dispone di un flusso di lavoro che usa un'applicazione C#, convalidare queste applicazioni in un ambiente Linux. Per ulteriori informazioni, vedere [Eseguire la migrazione per le soluzioni .NET per HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| Storm Dashboard |Storm Dashboard non è disponibile. Vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux](storm/apache-storm-deploy-monitor-topology-linux.md) per le modalità di invio delle topologie |
| Interfaccia utente di Storm |L'interfaccia utente di Storm è disponibile all'indirizzo https://NOMECLUSTER.azurehdinsight.net/stormui |
| Visual Studio per creare, distribuire e gestire le topologie C# o ibride |È possibile usare Visual Studio per creare, distribuire e gestire topologie C# (SCP.NET) o ibride in Storm basato su Linux in HDInsight. Può essere usato solo con i cluster creati dopo il 28/10/2016. |

## <a name="hbase"></a>HBase

Nei cluster basati su Linux, l'elemento padre znode per HBase è `/hbase-unsecure`. Impostare questo valore nella configurazione di qualsiasi applicazione client Java che usi un'API Java HBase nativa.

Vedere [Compilare un'applicazione HBase basata su Java](hdinsight-hbase-build-java-maven.md) per un client di esempio che imposta questo valore.

## <a name="spark"></a>Spark

I cluster Spark non erano disponibili nei cluster Windows durante l'anteprima. GA Spark è disponibile solo con i cluster basati su Linux. Non esiste un percorso di migrazione da un cluster di anteprima Spark basato su Windows a un cluster di rilascio Spark basato su Linux.

## <a name="known-issues"></a>Problemi noti

### <a name="azure-data-factory-custom-net-activities"></a>Attività .NET personalizzate in Azure Data Factory

Le attività .NET personalizzate in Azure Data Factory non sono attualmente supportate nei cluster HDInsight basati su Linux. Conviene invece usare uno dei metodi seguenti per implementare attività personalizzate nell'ambito della pipeline di ADF.

* Eseguire le attività .NET nel pool di Azure Batch. Vedere la sezione relativa all'uso del servizio collegato a Azure Batch dell'articolo su come [usare attività personalizzate in una pipeline di Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementare l'attività come attività di MapReduce. Per altre informazioni vedere [Richiamare i programmi MapReduce da Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Terminazioni riga

In genere le terminazioni riga nei sistemi basati su Windows usano CRLF, mentre nei sistemi basati su Linux usano LF. Potrebbe essere necessario modificare i consumer e produttori di dati per usare LF.

Ad esempio, se si usa Azure PowerShell per eseguire una query in HDInsight in un cluster basato su Windows, verranno restituiti dati con terminazione riga CRLF. La stessa query in un cluster basato su Linux restituisce dati con terminazione riga LF. Prima di eseguire la migrazione a un cluster basato su Linux, verificare se la terminazione riga causa un problema con la soluzione.

Usare sempre LF come terminazione riga per gli script eseguiti sui nodi del cluster. Se si usa CRLF, è possibile riscontrare errori durante l'esecuzione di script in un cluster basato su Linux.

Se gli script non contengono stringhe con caratteri CR incorporati, è possibile modificare in blocco le terminazioni riga tramite uno dei metodi seguenti:

* **Prima di caricare sul cluster**: usare le istruzioni di PowerShell seguenti per modificare le terminazioni della riga da CRLF a LF prima di caricare lo script sul cluster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Dopo il caricamento sul cluster**: usare il comando seguente da una sessione SSH a un cluster basato su Linux per modificare lo script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Usare SSHper connettersi a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Ambari](hdinsight-hadoop-manage-ambari.md)
