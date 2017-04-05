---
title: Eseguire la migrazione da HDInsight basato su Windows a HDInsight basato su Linux | Documentazione Microsoft
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
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9a2e9b7809b00ae2b60e152bc240ab2ef775c7bf
ms.lasthandoff: 03/25/2017


---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrare da un cluster HDInsight basato su Windows a un cluster basato su Linux
Anche se HDInsight basato su Windows fornisce un modo semplice per usare Hadoop nel cloud, potrebbe essere necessario eseguire la migrazione a un cluster basato su Linux. Ad esempio, per sfruttare i vantaggi di strumenti e tecnologie basati su Linux che sono necessari per la soluzione. Molti elementi nell'ecosistema Hadoop vengono sviluppati in sistemi basati su Linux e potrebbero non essere compatibili con HDInsight basato su Windows. Inoltre, molti libri, video e altre forme di materiale didattico prevedono che si usi un sistema Linux quando si lavora con Hadoop.

Questo documento contiene informazioni dettagliate sulle differenze tra HDInsight in Windows e Linux e indicazioni su come eseguire la migrazione dei carichi di lavoro esistenti verso un cluster basato su Linux.

> [!NOTE]
> I cluster HDInsight usano il supporto a lungo termine Ubuntu (LTS) come sistema operativo per i nodi del cluster. Per informazioni sulla versione di Ubuntu disponibile con HDInsight e sulle versioni degli altri componenti, vedere le [versioni dei componenti HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Attività di migrazione
Il flusso di lavoro generale per la migrazione è il seguente:

![Diagramma del flusso di lavoro della migrazione](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leggere interamente questo documento per comprendere le modifiche che potrebbero essere necessarie durante la migrazione del flusso di lavoro esistente, dei processi e così via in un cluster basato su Linux.
2. Creare un cluster basato su Linux come ambiente di test/controllo qualità. Per altre informazioni sulla creazione di un cluster basato su Linux, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiare processi esistenti, origini dati e sink nel nuovo ambiente.
4. Eseguire il test di convalida per assicurarsi che i processi funzionino come previsto nel nuovo cluster.

Dopo avere verificato che tutto funzioni come previsto, pianificare i tempi di inattività per la migrazione. Durante questo periodo di inattività, eseguire le operazioni seguenti.

1. Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster, ad esempio se i dati sono archiviati direttamente in un nodo head.
2. Eliminare il cluster basato su Windows.
3. Creare un cluster basato su Linux con lo stesso archivio dati predefinito usato dal cluster basato su Windows. Il nuovo cluster può continuare a lavorare con i dati di produzione esistenti.
4. Importare i dati temporanei di cui è stata eseguita una copia di backup.
5. Avviare processi/continuare l'elaborazione con il nuovo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copiare i dati nell'ambiente di test
Esistono molti metodi per copiare dati e processi, ma i due metodi illustrati in questa sezione sono i più semplici per spostare direttamente i file in un cluster di prova.

#### <a name="hdfs-dfs-copy"></a>Copia HDFS DFS

Usare la procedura seguente per copiare i dati dal cluster di produzione al cluster di test. In queste procedure viene usata l'utilità `hdfs dfs` inclusa in HDInsight.

1. Individuare le informazioni dell'account di archiviazione e del contenitore predefinito per il cluster esistente. Nell'esempio seguente viene usato PowerShell per recuperare queste informazioni:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Per creare un ambiente di test, seguire la procedura riportata nel documento Creare cluster basati su Linux in HDInsight. Prima di creare il cluster, interrompersi e selezionare **Configurazione facoltativa**.
3. Nel pannello Configurazione facoltativa selezionare **Account di archiviazione collegati**.
4. Selezionare **Aggiungi una chiave di archiviazione**e, quando richiesto, selezionare l'account di archiviazione restituito dallo script PowerShell nel passaggio 1. Fare clic su **Seleziona** in ogni pannello. Al termine, creare il cluster.
5. Dopo aver creato il cluster, connettersi tramite **SSH** Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Dalla sessione SSH usare il comando seguente per copiare i file dall'account di archiviazione collegato al nuovo account di archiviazione predefinito. Sostituire CONTAINER con le informazioni sul contenitore restituite da PowerShell. Sostituire __ACCOUNT__ con il nome dell'account. Sostituire il percorso dei dati con il percorso di un file di dati.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    > [!NOTE]
    > Se la struttura della directory che contiene i dati non esiste nell'ambiente di test, è possibile crearla usando il comando seguente:

        hdfs dfs -mkdir -p /new/path/to/create

    L'opzione `-p` consente di creare tutte le directory nel percorso.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copia diretta tra i BLOB di Archiviazione di Azure
In alternativa si può usare il cmdlet `Start-AzureStorageBlobCopy` di Azure PowerShell per copiare i BLOB tra gli account di archiviazione all'esterno di HDInsight. Per altre informazioni, vedere la sezione Come gestire i BLOB di Azure del documento Uso di Azure PowerShell con Archiviazione di Azure.

## <a name="client-side-technologies"></a>Tecnologie lato client
In generale le tecnologie lato client, ad esempio i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md) o [.NET SDK per Hadoop](https://hadoopsdk.codeplex.com/) continuano a funzionare nello stesso modo con i cluster basati su Linux, perché si basano su API REST che sono uguali in entrambi i tipi di sistema operativo del cluster.

## <a name="server-side-technologies"></a>Tecnologie lato server
Nella tabella seguente vengono fornite indicazioni sulla migrazione dei componenti lato server specifici di Windows.

| Se si usa questa tecnologia... | Eseguire questa operazione... |
| --- | --- |
| **PowerShell** (script sul lato server, incluse le Azioni script usate durante la creazione del cluster) |Riscriverli come script di Bash. Per le azioni script vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md) e [Sviluppo di azioni script con HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md). |
| **Interfaccia della riga di comando di Azure** (script lato server) |Anche se l'interfaccia della riga di comando di Azure è disponibile in Linux, non è preinstallata nei nodi head del cluster HDInsight. Se occorre per gli script lato server, vedere [Installare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md) per informazioni sull'installazione su piattaforme basate su Linux. |
| **Componenti .NET** |.NET non è completamente supportato in tutti i tipi di cluster HDInsight basati su Linux. Storm basato su Linux nei cluster HDInsight creati dopo il 28/10/2016 supporta topologie Storm C# con il framework SCP.NET. Il supporto aggiuntivo per .NET verrà aggiunto negli aggiornamenti futuri. |
| **Componenti di Win32 o altre tecnologie esclusive di Windows** |La procedura dipende dal componente o dalla tecnologia. Si potrebbe trovare una versione compatibile con Linux o potrebbe essere necessario trovare una soluzione alternativa o riscrivere il componente. |

## <a name="cluster-creation"></a>Creazione del cluster
Questa sezione illustra le differenze nella creazione del cluster.

### <a name="ssh-user"></a>Utente SSH
I cluster HDInsight basati su Linux usano il protocollo **Secure Shell (SSH)** per fornire l'accesso remoto ai nodi del cluster. A differenza dei cluster basati su Desktop remoto per Windows, la maggior parte dei client SSH non offre esperienza utente basata sull'interfaccia grafica, bensì un'interfaccia della riga di comando che consente di eseguire comandi nel cluster. Alcuni client, ad esempio [MobaXterm](http://mobaxterm.mobatek.net/), offrono un browser grafico per il file system oltre a una riga di comando remota.

Durante la creazione del cluster è necessario specificare un utente SSH e una **password** oppure un **certificato di chiave pubblica** per l'autenticazione.

È consigliabile usare il certificato di chiave pubblica perché è più sicuro rispetto alla password. L'autenticazione del certificato genera una coppia di chiavi pubblica/privata firmata e fornisce la chiave pubblica durante la creazione del cluster. Durante la connessione al server tramite SSH, la chiave privata del client consente l'autenticazione per la connessione.

Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalizzazione cluster
**Azioni script** usate con i cluster basati su Linux devono essere scritte nello script Bash. Sebbene le Azioni script possano essere usate durante la creazione del cluster, per i cluster basati su Linux possono anche essere usate per eseguire la personalizzazione dopo che il cluster entra in funzione. Per altre informazioni vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md) e [Sviluppo di azioni script con HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md).

Un'altra funzionalità di personalizzazione è **bootstrap**, che per i cluster basati su Windows consente di specificare la posizione di librerie aggiuntive da usare con Hive. Dopo la creazione del cluster, queste librerie sono automaticamente disponibili per l'uso con le query Hive, senza la necessità di usare `ADD JAR`.

Bootstrap per i cluster basati su Linux non offre questa funzionalità. Usare invece l'azione script documentata nell'articolo [Aggiungere librerie Hive durante la creazione del cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Reti virtuali
I cluster HDInsight basati su Windows funzionano soltanto con le reti virtuali classiche, mentre i cluster HDInsight basati su Linux richiedono le reti virtuali di gestione risorse. Se nella rete virtuale classica sono presenti risorse a cui si deve connettere il cluster HDInsight basato su Linux, vedere [Connessione di una rete virtuale classica a una rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Per altre informazioni sui requisiti di configurazione per usare le reti virtuali di Azure con HDInsight, vedere [Estendere le funzionalità di HDInsight usando una rete virtuale](hdinsight-extend-hadoop-virtual-network.md).

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

In genere prima di intervenire è opportuno valutare se un avviso si verifica per molto tempo o se indica problemi dell'utente che erano stati segnalati prima di intervenire.

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

I carichi di lavoro di Pig e MapReduce sono molto simili ai cluster basati su Linux. L'unica differenza è la modalità di connessione ai nodi head del cluster. Per altre informazioni, vedere i documenti seguenti:

* [Usare Pig con SSH](hdinsight-hadoop-use-pig-ssh.md)
* [Usare MapReduce con SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive
Il grafico seguente fornisce indicazioni sulla migrazione dei carichi di lavoro di Hive.

| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| **Editor Hive** |[vista Hive in Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` per abilitare Tez |Tez è il motore di esecuzione predefinito per i cluster basati su Linux, pertanto l'istruzione set non è più necessaria. |
| script o file CMD nel server richiamati nell'ambito di un processo Hive |gli script Bash |
| `hive` dal desktop remoto |Usare [Beeline](hdinsight-hadoop-use-hive-beeline.md) o [Hive da una sessione SSH](hdinsight-hadoop-use-hive-ssh.md) |

## <a name="storm"></a>Storm
| Nel sistema basato su Windows si usa... | Nel sistema basato su Linux si usa... |
| --- | --- |
| Storm Dashboard |Storm Dashboard non è disponibile. Vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md) per le modalità di invio delle topologie |
| Interfaccia utente di Storm |L'interfaccia utente di Storm è disponibile all'indirizzo https://NOMECLUSTER.azurehdinsight.net/stormui |
| Visual Studio per creare, distribuire e gestire le topologie C# o ibride |È possibile usare Visual Studio per creare, distribuire e gestire topologie C# (SCP.NET) o ibride in Storm basato su Linux in cluster HDInsight creati dopo il 28/10/2016. |

## <a name="hbase"></a>HBase
Nei cluster basati su Linux, l'elemento padre znode per HBase è `/hbase-unsecure`. Impostare questo valore nella configurazione di qualsiasi applicazione client Java che usi un'API Java HBase nativa.

Vedere [Compilare un'applicazione HBase basata su Java](hdinsight-hbase-build-java-maven.md) per un client di esempio che imposta questo valore.

## <a name="spark"></a>Spark
I cluster Spark non erano disponibili nei cluster Windows durante l'anteprima. GA Spark è disponibile solo con i cluster basati su Linux. Non esiste un percorso di migrazione da un cluster di anteprima Spark basato su Windows a un cluster di rilascio Spark basato su Linux.

## <a name="known-issues"></a>Problemi noti
### <a name="azure-data-factory-custom-net-activities"></a>Attività .NET personalizzate in Azure Data Factory
Le attività .NET personalizzate in Azure Data Factory non sono attualmente supportate nei cluster HDInsight basati su Linux. Conviene invece usare uno dei metodi seguenti per implementare attività personalizzate nell'ambito della pipeline di ADF.

* Eseguire le attività .NET nel pool di Azure Batch. Vedere la sezione relativa all'uso del servizio collegato a Azure Batch dell'articolo su come [usare attività personalizzate in una pipeline di Azure Data Factory](../data-factory/data-factory-use-custom-activities.md)
* Implementare l'attività come attività di MapReduce. Per altre informazioni vedere [Richiamare i programmi MapReduce da Data Factory](../data-factory/data-factory-map-reduce.md).

### <a name="line-endings"></a>Terminazioni riga
In genere le terminazioni riga nei sistemi basati su Windows usano CRLF, mentre nei sistemi basati su Linux usano LF. Se si producono o si attendono dati con terminazioni riga CRLF, potrebbe essere necessario modificare i producer o i consumer in modo che risultino compatibili con la terminazione riga LF.

Ad esempio, se si usa Azure PowerShell per eseguire una query in HDInsight in un cluster basato su Windows, verranno restituiti dati con terminazione riga CRLF. La stessa query in un cluster basato su Linux restituisce dati con terminazione riga LF. Prima di eseguire la migrazione a un cluster basato su Linux, è consigliabile verificare se ciò causa un problema con la soluzione.

Se si dispone di script che vengono eseguiti direttamente nei nodi cluster Linux, è necessario usare sempre LF come terminazione di riga. Se si usa CRLF, è possibile riscontrare errori durante l'esecuzione di script in un cluster basato su Linux.

Se si è certi che gli script non contengono stringhe con caratteri CR incorporati, è possibile modificare in blocco le terminazioni riga tramite uno dei metodi seguenti:

* **Se sono presenti script che si prevede di caricare sul cluster**, usare le istruzioni di PowerShell seguenti per modificare le terminazioni riga da CRLF a LF prima di caricare lo script sul cluster.

      $original_file ='c:\path\to\script.py'
      $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
      [IO.File]::WriteAllText($original_file, $text)
* **Se nella risorsa di archiviazione usata dal cluster sono già presenti script**, è possibile usare il comando seguente da una sessione SSH nel cluster basato su Linux per modificare lo script.

      hdfs dfs -get wasbs:///path/to/script.py oldscript.py
      tr -d '\r' < oldscript.py > script.py
      hdfs dfs -put -f script.py wasbs:///path/to/script.py

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Usare SSHper connettersi a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Ambari](hdinsight-hadoop-manage-ambari.md)

