<properties 
	pageTitle="Sviluppo di azioni script con HDInsight| Microsoft Azure" 
	description="Informazioni su come personalizzare i cluster Hadoop con Azione script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Sviluppo di azioni script con HDInsight 

L'azione script fornisce la funzionalità Azure HDInsight usata per installare software aggiuntivi in esecuzione in un cluster Hadoop o per modificare la configurazione delle applicazioni installate in un cluster. Le azioni script sono script eseguiti sui nodi del cluster quando si distribuiscono cluster HDInsight. Tali script vengono eseguiti dopo che i nodi del cluster hanno completato la configurazione di HDInsight. Un'azione script viene eseguita con i privilegi dell'account amministratore di sistema e fornisce diritti di accesso completo ai nodi del cluster. È possibile fornire a ciascun cluster un elenco di azioni di script da eseguire nell'ordine in cui sono state specificate.

È possibile distribuire l'azione script da Azure PowerShell o tramite HDInsight .NET SDK. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-cluster-customize].



## <a name="bestPracticeScripting"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster HDInsight, è opportuno seguire le procedure consigliate indicate di seguito:

* [Controllare la versione di Hadoop](#bPS1)
* [Fornire collegamenti stabili alle risorse di script](#bPS2)
* [Assicurarsi che lo script di personalizzazione del cluster sia idempotente](#bPS3)
* [Installare i componenti personalizzati nella posizione ottimale ](#bPS4)
* [Verificare la disponibilità elevata dell'architettura del cluster](#bPS5)
* [Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure](#bPS6)

### <a name="bPS1"></a>Controllare la versione di Hadoop
Solo HDInsight versione 3.1 (Hadoop 2.4) e versioni successive supportano l'uso dell'azione script per installare i componenti personalizzati in un cluster. Nello script personalizzato è necessario usare il metodo helper **Get-HDIHadoopVersion** per controllare la versione di Hadoop prima di procedere con le altre attività nello script.


### <a name="bPS2"></a>Fornire collegamenti stabili alle risorse di script 
Gli utenti devono assicurarsi che tutti gli script e gli altri elementi usati per la personalizzazione di un cluster rimangano disponibili per l'intero ciclo di vita del cluster stesso e che durante tale periodo le versioni di questi file non cambino. Queste risorse sono richieste in caso di ricreazione dell'immagine dei nodi nel cluster. Si consiglia di scaricare e archiviare tutti gli elementi in un account di archiviazione controllato dall'utente. L'account di archiviazione può essere quello predefinito o uno degli account aggiuntivi specificati durante la distribuzione di un cluster personalizzato. Negli esempi di cluster personalizzati Spark e R forniti nella documentazione, ad esempio, è stata eseguita una copia locale delle risorse in questo account di archiviazione: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Assicurarsi che lo script di personalizzazione del cluster sia idempotente
È necessario tenere presente che, durante il ciclo di vita di un cluster HDInsight, verrà ricreata l'immagine dei nodi di quest'ultimo. Lo script di personalizzazione del cluster viene eseguito ad ogni nuova creazione dell'immagine del cluster. Questo script deve essere progettato in modo da essere idempotente. In altri termini, quando viene ricreata l'immagine, lo script deve assicurare che il cluster venga riportato allo stesso stato personalizzato in cui si trovava dopo la prima esecuzione dello script, al momento della creazione originaria del cluster. Se, ad esempio, al momento della prima esecuzione uno script personalizzato ha installato un'applicazione in D:\\AppLocation, a ogni esecuzione successiva, quando viene ricreata l'immagine, lo script deve verificare la presenza dell'applicazione nel percorso D:\\AppLocation prima di procedere con altri passaggi.


### <a name="bPS4"></a>Installare i componenti personalizzati nella posizione ottimale 
Quando vengono ricreate le immagini dei nodi del cluster, l'unità delle risorse C:\\ e l'unità di sistema D:\\ potrebbero essere riformattate, causando una perdita dei dati e delle applicazioni installate nei driver. Questo problema può verificarsi anche se un nodo della macchina virtuale (VM) di Azure che fa parte del cluster si arresta e viene sostituito da un nuovo nodo. È possibile installare componenti nell'unità D:\\ o nel percorso C:\\apps del cluster. Tutti gli altri percorsi nell'unità C:\\ sono riservati. Specificare il percorso in cui installare le applicazioni o le librerie nello script di personalizzazione del cluster.


### <a name="bPS5"></a>Verificare la disponibilità elevata dell'architettura del cluster

HDInsight ha un'architettura attivo/passivo per la disponibilità elevata, in cui un nodo head è in modalità attiva (dove sono in esecuzione i servizi HDInsight) e l'altro è in modalità standby (dove non sono in esecuzione i servizi HDInsight). I nodi passano dalla modalità attiva a quella passiva e viceversa quando i servizi HDInsight vengono interrotti. Se un'azione script viene usata per installare i servizi in entrambi i nodi head per la disponibilità elevata, tenere presente che il meccanismo di failover di HDInsight non sarà in grado di eseguire automaticamente il failover di questi servizi installati dall'utente. Quindi, se è richiesta la disponibilità elevata per i servizi installati dall'utente nei nodi head di HDInsight, questi servizi devono avere un proprio meccanismo di failover se sono in modalità attivo/passivo oppure essere in modalità attivo/attivo.

Un comando di azione script di HDInsight viene eseguito in entrambi i nodi head quando il ruolo del nodo head viene specificato come valore nel parametro *ClusterRoleCollection* (documentato di seguito nella sezione [Come eseguire un'azione script](#runScriptAction)). Pertanto, quando si progetta uno script personalizzato, assicurarsi che lo script tenga conto di questa impostazione. Per evitare problemi, è preferibile non installare e avviare gli stessi servizi in entrambi i nodi head, dove è possibile che entrino in competizione l'uno con l'altro. Tenere presente, inoltre, che quando viene ricreata l'immagine sono possibili perdite di dati: è quindi necessario che il software installato mediante l'azione script sia resiliente a eventi di questo tipo. Le applicazioni devono essere progettate per usare dati a disponibilità elevata distribuiti in molti nodi. Si noti che è possibile ricreare allo stesso tempo l'immagine di 1/5 dei nodi di un cluster.


### <a name="bPS6"></a>Configurare i componenti personalizzati per l'uso dell'archivio BLOB di Azure
I componenti personalizzati installati nei nodi del cluster possono avere una configurazione predefinita per l'uso dell'archiviazione in un file system distribuito Hadoop (HDFS). Modificare la configurazione in modo che venga usato l'archivio BLOB di Azure. Quando si ricrea l'immagine di un cluster, il file system HDFS viene formattato e tutti i dati archiviati vengono eliminati. Se invece si usa l'archivio BLOB di Azure, i dati vengono mantenuti.

## <a name="helpermethods"></a>Metodi helper per gli script personalizzati 

L'azione script fornisce i seguenti metodi helper che è possibile usare durante la scrittura di script personalizzati.

Metodo helper | Descrizione
-------------- | -----------
**Save-HDIFile** | Scaricare un file dall'URI (Uniform Resource Identifier) specificato a un percorso nel disco locale associato al nodo della macchina virtuale di Azure assegnato al cluster
**Expand-HDIZippedFile** | Decomprimere un file compresso.
**Invoke-HDICmdScript** | Eseguire uno script da cmd.exe.
**Write-HDILog** | Scrivere l'output dallo script personalizzato usato per un'azione script.
**Get-Services** | Recuperare un elenco di servizi in esecuzione nel computer in cui viene eseguito lo script.
**Get-Service** | Con il nome del servizio specificato come input, ottenere informazioni dettagliate per uno specifico servizio (nome del servizio, ID processo, stato, ecc.) nel computer in cui viene eseguito lo script.
**Get-HDIServices** | Recuperare un elenco di servizi di HDInsight in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIService** | Con il nome del servizio di HDInsight specificato come input, ottenere informazioni dettagliate per uno specifico servizio (nome del servizio, ID processo, stato, ecc.) nel computer in cui viene eseguito lo script.
**Get-ServicesRunning** | Recuperare un elenco di servizi in esecuzione nel computer in cui viene eseguito lo script.
**Get-ServiceRunning** | Controllare se un servizio specifico (individuato tramite nome) è in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIServicesRunning** | Recuperare un elenco di servizi di HDInsight in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIServiceRunning** | Controllare se un servizio specifico di HDInsight (individuato tramite nome) è in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIHadoopVersion** | Recuperare la versione di Hadoop installata nel computer in cui viene eseguito lo script.
**Test-IsHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script è un nodo head.
**Test-IsActiveHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script è un nodo head attivo.
**Test-IsHDIDataNode** | Verificare se il computer in cui viene eseguito lo script è un nodo di dati.
**Edit-HDIConfigFile** | Modificare i file di configurazione hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml o yarn-site.xml.

## <a name="commonusage"></a>Modelli di utilizzo comuni

Questa sezione fornisce indicazioni sull'implementazione di alcuni dei modelli di utilizzo comuni che si potrebbero riscontrare durante la scrittura dello script personalizzato.

### Impostazioni delle variabili di ambiente

Spesso nello sviluppo delle azioni script risulterà necessario impostare alcune variabili di ambiente. Ad esempio, uno scenario molto comune è quello in cui si scarica un file binario da un sito esterno, lo si installa nel cluster e si aggiunge il percorso di installazione alla variabile di ambiente 'PATH'. Il seguente frammento di codice mostra come impostare le variabili di ambiente nello script personalizzato.

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Questa istruzione imposta la variabile di ambiente **MDS_RUNNER_CUSTOM_CLUSTER** sul valore 'true', oltre a impostare l'ambito di questa variabile a livello di computer. In alcuni casi è importante che le variabili di ambiente siano impostate nell'ambito appropriato: computer o utente. Vedere [qui] (https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx "qui") per altre informazioni sull'impostazione delle variabili di ambiente.

### Accedere alle posizioni in cui sono archiviati gli script personalizzati

Gli script usati per personalizzare un cluster devono essere nell'account di archiviazione predefinito per il cluster o in un contenitore di sola lettura pubblico in qualsiasi altro account di archiviazione. Se lo script accede a risorse ubicate altrove, queste devono essere in una posizione accessibile pubblicamente (almeno di sola lettura pubblica). Ad esempio, si potrebbe accedere a un file e salvarlo con il comando SaveFile-HDI.

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

### Generazione di un'eccezione per la distribuzione cluster non riuscita

Per ricevere notifiche precise relative al fatto che la personalizzazione del cluster non è andata come previsto, è importante generare un'eccezione e abbandonare il provisioning del cluster. Ad esempio, potrebbe essere necessario elaborare un file, se esistente, e gestire lo scenario di errore in cui il file non esiste. Ciò garantirà che lo script venga chiuso normalmente e lo stato del cluster noto sia corretto. Il seguente frammento di codice offre un esempio di come procedere:

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

## <a name="deployScript"></a>Elenco di controllo per la distribuzione di un'azione script
Di seguito sono indicati i passaggi effettuati durante la preparazione della distribuzione degli script:

1. Inserire i file che contengono gli script personalizzati in un percorso accessibile per i nodi del cluster durante la distribuzione. L'account di archiviazione può essere uno qualsiasi degli account predefiniti o aggiuntivi specificati durante la distribuzione del cluster oppure qualsiasi altro contenitore di archiviazione accessibile pubblicamente.
2. Aggiungere controlli negli script per garantire che questi vengano eseguiti in modo idempotente, così che sia possibile eseguire più volte lo script nello stesso nodo.
3. Usare il cmdlet di Azure PowerShell **Write-Output** per stampare in STDOUT e in STDERR. Non usare **Write-Host**.
4. Usare una cartella di file temporanea, ad esempio $env:TEMP, per conservare il file scaricato usato dagli script e quindi eliminarla dopo aver eseguito gli script stessi.
5. Installare il software personalizzato solo in D:\\ o C:\\apps. Non usare altri percorsi dell'unità C: perché sono riservati. Si noti che l'installazione dei file nell'unità C: all'esterno della cartella C:\\apps può comportare errori di installazione durante la nuova creazione dell'immagine del nodo.
6. In caso di modifica delle impostazioni o dei file di configurazione del servizio Hadoop a livello di sistema operativo, è possibile riavviare i servizi HDInsight in modo che possano rilevare qualsiasi impostazione a livello di sistema operativo, ad esempio le variabili di ambiente impostate negli script.


## <a name="runScriptAction"></a>Come eseguire un'azione script

È possibile usare le azioni script per personalizzare i cluster HDInsight usando il portale di Azure, Azure PowerShell o HDInsight .NET SDK. Per le istruzioni, vedere [Come usare l'azione script](../hdinsight-hadoop-customize-cluster/#howto).


## <a name="sampleScripts"></a>Esempi di script personalizzati

Microsoft fornisce script di esempio per installare i componenti in un cluster HDInsight. Gli script di esempio e le istruzioni su come usarli sono disponibili nei collegamenti seguenti:

- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster Hadoop HDInsight][hdinsight-r-scripts]
- [Installare e usare Solr nei cluster HDInsight](../hdinsight-hadoop-solr-install)
- [Installare e usare Giraph nei cluster HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE]Gli script di esempio funzionano solo con il cluster HDInsight versione 3.1 o successiva. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](../hdinsight-component-versioning/).

## <a name="testScript"></a>Come testare lo script personalizzato con HDInsight Emulator

Un modo semplice per testare uno script personalizzato prima di usarlo nel comando di azione script di HDInsight Script Action è eseguirlo in HDInsight Emulator. È possibile installare HDInsight Emulator localmente, in un'infrastruttura di Azure come macchina virtuale Windows Server 2012 R2 di servizio (IaaS) o in un computer locale e osservare se il comportamento dello script è corretto. La macchina virtuale Windows Server 2012 R2 corrisponde alla macchina virtuale usata da HDInsight per i propri nodi.

Questa sezione descrive la procedura per usare HDInsight Emulator localmente per i test, che è simile alla procedura che prevede l'uso di una macchina virtuale.

**Installare HDInsight Emulator**: per eseguire localmente l'azione script, è necessario che sia installato HDInsight Emulator. Per istruzioni su come installarlo, vedere [Introduzione a HDInsight Emulator](../hdinsight-get-started-emulator/).

**Impostare i criteri di esecuzione per Azure PowerShell**: aprire Azure PowerShell ed eseguire come amministratore il seguente comando per impostare i criteri di esecuzione su *LocalMachine* e su *Unrestricted*.
 
	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

Questi criteri devono essere senza restrizioni perché gli script non sono firmati.

**Scaricare l'azione script** da eseguire in una destinazione locale. I seguenti script di esempio sono disponibili per il download dai percorsi seguenti:

* **Spark**. https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**. https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**. https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**. https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**Eseguire l'azione script**: aprire una nuova finestra di Azure PowerShell in modalità di amministratore ed eseguire lo script di installazione Spark o R dal percorso locale in cui è stato salvato.

**Esempi di utilizzo**: quando si usano i cluster Spark ed R, è possibile che in HDInsight Emulator non siano presenti file di dati necessari. Quindi potrebbe essere necessario caricare i file txt interessati contenenti i dati in un percorso in HDFS e quindi usare il percorso per accedere ai dati, ad esempio:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


In alcuni casi, uno script personalizzato può dipendere effettivamente dai componenti di HDInsight, ad esempio per rilevare se determinati servizi Hadoop sono attivi. In questo caso, sarà necessario testare gli script personalizzati eseguendone la distribuzione su un cluster HDInsight effettivo.


## <a name="debugScript"></a>Come eseguire il debug dello script personalizzato

I log di errore dello script vengono archiviati insieme ad altri output nell'account di archiviazione predefinito specificato per il cluster al momento della creazione. I log vengono archiviati in una tabella con il nome *u<\\frammento-nome-cluster><\\timestamp>setuplog*. Si tratta di log aggregati contenenti record provenienti da tutti i nodi (nodi head e nodi di lavoro), in cui lo script viene eseguito nel cluster.

È anche possibile accedere in remoto ai nodi cluster per visualizzare sia STDOUT che STDERR per gli script personalizzati. I log in ciascun nodo sono specifici solo per tale nodo e sono accessibili da **C:\\HDInsightLogs\\DeploymentAgent.log**. Questi file di log registrano tutti gli output dello script personalizzato. Un frammento di log di esempio per un'azione script Spark è simile a quanto riportato di seguito:

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
In questo log è evidente che è stata eseguita l'azione script Spark nella macchina virtuale denominata HEADNODE0 e che durante l'esecuzione non sono state generate eccezioni.

Nel caso in cui si verifichi un errore di esecuzione, anche l'output che descrive l'errore sarà contenuto in questo file di log. Le informazioni fornite in questi log saranno utili per il debug di eventuali problemi di script.


## <a name="seeAlso"></a>Vedere anche

[Personalizzare cluster HDInsight mediante le azioni script][hdinsight-cluster-customize]


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--HONumber=54--> 