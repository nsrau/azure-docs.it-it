<properties 
	pageTitle="Sviluppo di azioni script con HDInsight| Azure" 
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
	ms.date="12/19/2014" 
	ms.author="bradsev"/> 

# Sviluppo di azioni script con HDInsight 

Le azioni script forniscono la funzionalità Azure HDInsight usata per installare software aggiuntivi in esecuzione in un cluster Hadoop o per modificare la configurazione delle applicazioni installate in un cluster. Le azioni script sono script eseguiti sui nodi del cluster quando si distribuiscono cluster HDInsight. Tali script vengono eseguiti dopo che i nodi del cluster hanno completato la configurazione di HDInsight. L'azione script viene eseguita con i privilegi dell'account amministratore di sistema e fornisce diritti di accesso completo ai nodi del cluster. È possibile fornire a ciascun cluster un elenco di azioni script da eseguire. Tali azioni verranno eseguite nell'ordine in cui sono state specificate.

È possibile distribuire l'azione script da Azure PowerShell o tramite .NET SDK per HDInsight.  Per altre informazioni, vedere [Personalizzare il cluster HDInsight tramite le azioni script][hdinsight-cluster-customize].


## Contenuto dell'articolo

- [Procedure consigliate per lo sviluppo di script](#bestPracticeScripting)
- [Metodi helper per gli script personalizzati](#helpermethods)
- [Elenco di controllo per la distribuzione di un'azione script](#deployScript)
- [Come eseguire un'azione script](#runScriptAction)
- [Esempi di script personalizzati](#sampleScripts) 
- [Come testare lo script personalizzato con HDInsight Emulator](#testScript)
- [Come eseguire il debug dello script personalizzato](#debugScript)
- [Vedere anche](#seeAlso)


## <a name="bestPracticeScripting"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster HDInsight, è opportuno seguire le procedure consigliate indicate di seguito:

* [Controllare la versione di Hadoop](#bPS1)
* [Fornire collegamenti stabili alle risorse di script](#bPS2)
* [Lo script di personalizzazione del cluster deve essere idempotente](#bPS3)
* [Percorso in cui sono installati i componenti personalizzati](#bPS4)
* [Architettura del cluster: verificare la disponibilità elevata](#bPS5)
* [Configurare i componenti personalizzati per l'utilizzo di WASB](#bPS6)

### <a name="bPS1"></a>Controllare la versione di Hadoop
Solo HDInsight versione 3.1 (Hadoop 2.4) e versioni successive supportano l'uso dell'azione script per installare i componenti personalizzati in un cluster. Nello script personalizzato è necessario usare il metodo helper **Get-HDIHadoopVersion** per controllare la versione di Hadoop prima di procedere con le altre attività nello script.


### <a name="bPS2"></a>Fornire collegamenti stabili alle risorse di script 
Gli utenti devono assicurarsi che tutti gli script e gli altri elementi usati per la personalizzazione di un cluster rimangano disponibili per l'intero ciclo di vita del cluster stesso e che durante tale periodo le versioni di questi file non cambino. Queste risorse sono richieste in caso di ricreazione dell'immagine dei nodi nel cluster. Si consiglia di scaricare e archiviare tutti gli elementi in un account di archiviazione controllato dall'utente. L'account di archiviazione può essere quello predefinito o uno degli account aggiuntivi specificati durante la distribuzione di un cluster personalizzato.
Negli esempi di cluster personalizzati Spark e R forniti nella documentazione, ad esempio, è stata eseguita una copia locale delle risorse in questo account di archiviazione: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Lo script di personalizzazione del cluster deve essere idempotente
È necessario tenere presente che, durante il ciclo di vita di un cluster HDInsight, verrà ricreata l'immagine dei nodi di quest'ultimo. Lo script di personalizzazione del cluster viene eseguito ad ogni nuova creazione dell'immagine del cluster. Questo script deve essere progettato in modo da essere idempotente. In altri termini, quando viene ricreata l'immagine lo script deve assicurare che il cluster venga riportato allo stesso stato personalizzato in cui si trovava dopo la prima esecuzione dello script, al momento della creazione originaria del cluster. Se, ad esempio, al momento della prima esecuzione uno script personalizzato ha installato un'applicazione in D:\AppLocation, a ogni esecuzione successiva, quando viene ricreata l'immagine, lo script deve verificare la presenza dell'applicazione nel percorso D:\AppLocation prima di procedere con altri passaggi.


### <a name="bPS4"></a>Percorso in cui sono installati i componenti personalizzati 
Quando vengono ricreate le immagini dei nodi del cluster, l'unità delle risorse C:\ e l'unità di sistema D:\ potrebbero essere riformattate causando una perdita dei dati e delle applicazioni installate nei driver. Questo problema può verificarsi anche se un nodo della macchina virtuale di Azure che fa parte del cluster si arresta e viene sostituito da un nuovo nodo. È possibile installare componenti nell'unità D:/ o nel percorso C:\apps del cluster. Tutti gli altri percorsi nell'unità C:\ sono riservati. Specificare il percorso in cui installare le applicazioni o le librerie nello script di personalizzazione del cluster. 


### <a name="bPS5"></a>Architettura del cluster: verificare la disponibilità elevata

HDInsight ha un'architettura attivo/passivo per la disponibilità elevata in cui un nodo head è in modalità attiva (dove sono in esecuzione i servizi HDInsight) e l'altro è in modalità standby (dove non sono in esecuzione i servizi HDInsight). I nodi passano dalla modalità attiva a quella passiva e viceversa quando i servizi HDInsight vengono interrotti. Se un'azione script viene usata per installare i servizi in entrambi i nodi head per la disponibilità elevata, tenere presente che il meccanismo di failover di HDInsight non sarà in grado di eseguire automaticamente il failover di questi servizi installati dall'utente. Quindi, se è richiesta la disponibilità elevata per i servizi installati dall'utente nei nodi head di HDInsight, questi servizi devono avere un proprio meccanismo di failover se sono in modalità attivo/passivo oppure devono essere in modalità attivo/attivo. 

Un comando di azione script di HDInsight viene eseguito in entrambi i nodi head quando il ruolo del nodo head viene specificato come valore nel parametro  *ClusterRoleCollection* (documentato di seguito nella sezione [Come eseguire un'azione script](#runScriptAction)). Pertanto, quando si progetta uno script personalizzato, assicurarsi che lo script tenga conto di questa impostazione. Per evitare problemi, è preferibile non installare e avviare gli stessi servizi in entrambi i nodi head, dove è possibile che entrino in competizione l'uno con l'altro. Tenere presente, inoltre, che quando viene ricreata l'immagine sono possibili perdite di dati: è quindi necessario che il software installato mediante azioni script sia resiliente a eventi di questo tipo. Le applicazioni devono essere progettate per usare dati a disponibilità elevata distribuiti in molti nodi. Si noti che è possibile ricreare allo stesso tempo l'immagine di 1/5 dei nodi di un cluster.


### <a name="bPS6"></a>Configurare i componenti personalizzati per l'utilizzo di WASB
I componenti personalizzati installati nei nodi del cluster possono avere una configurazione predefinita per l'uso dell'archiviazione HDFS. Modificare la configurazione in modo che venga usato il BLOB di archiviazione di Azure (WASB). Quando si ricrea l'immagine di un cluster, il file system HDFS viene formattato e tutti i dati archiviati vengono eliminati. Se invece si usa WASB, i dati vengono mantenuti.

## <a name="helpermethods"></a>Metodi helper per gli script personalizzati 

L'azione script fornisce i seguenti metodi helper che è possibile usare durante la scrittura di script personalizzati.

Metodo helper | Descrizione
-------------- | -----------
**Save-HDIFile** | Scaricare un file dall'URI specificato a un percorso nel disco locale associato al nodo della macchina virtuale di Azure assegnato al cluster
**Expand-HDIZippedFile** | Decomprimere un file compresso
**Invoke-HDICmdScript** | Eseguire uno script da cmd.exe
**Write-HDILog** | Scrivere l'output dallo script personalizzato usato per l'azione script
**Get-Services** | Recuperare un elenco di servizi in esecuzione nel computer in cui viene eseguito lo script
**Get-Service** | Con il nome del servizio specificato come input, restituisce informazioni dettagliate per uno specifico servizio (nome del servizio, ID processo, stato, ecc.) nel computer in cui viene eseguito lo script
**Get-HDIServices** | Recuperare un elenco di servizi di HDInsight in esecuzione nel computer in cui viene eseguito lo script
**Get-HDIService** | Con il nome del servizio di HDInsight specificato come input, restituisce informazioni dettagliate per uno specifico servizio (nome del servizio, ID processo, stato, ecc.) nel computer in cui viene eseguito lo script
**Get-ServicesRunning** | Recuperare un elenco di servizi in esecuzione nel computer in cui viene eseguito lo script
**Get-ServiceRunning** | Controllare se un servizio specifico (individuato tramite nome) è in esecuzione nel computer in cui viene eseguito lo script
**Get-HDIServicesRunning** | Recuperare un elenco di servizi di HDInsight in esecuzione nel computer in cui viene eseguito lo script
**Get-HDIServiceRunning** | Controllare se un servizio specifico di HDInsight (individuato tramite nome) è in esecuzione nel computer in cui viene eseguito lo script
**Get-HDIHadoopVersion** | Recuperare la versione di Hadoop installata nel computer in cui viene eseguito lo script
**Test-IsHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script è un nodo head
**Test-IsActiveHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script è un nodo head attivo
**Test-IsHDIDataNode** | Verificare se il computer in cui viene eseguito lo script è un nodo di dati
**Edit-HDIConfigFile** | Modificare i file di configurazione hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml o yarn-site.xml
 

## <a name="deployScript"></a>Elenco di controllo per la distribuzione di un'azione script
Di seguito sono indicati i passaggi effettuati durante la preparazione della distribuzione degli script:

1. Inserire i file che contengono gli script personalizzati in un percorso accessibile per i nodi del cluster durante la distribuzione. L'account di archiviazione può essere uno qualsiasi degli account predefiniti o aggiuntivi specificati durante la distribuzione del cluster oppure qualsiasi altro contenitore di archiviazione accessibile pubblicamente.
2. Aggiungere controlli negli script per garantire che questi vengano eseguiti in modo idempotente, così che sia possibile eseguire più volte lo script nello stesso nodo.
3. Usare il cmdlet di PowerShell **Write-Output** per stampare in STDOUT e in STDERR. Non usare **Write-Host**.
4. Usare una cartella di file temporanea, ad esempio $env:TEMP, per conservare il file scaricato utilizzato dagli script e quindi eliminarla dopo aver eseguito gli script stessi.
5. Installare il software personalizzato solo nei seguenti percorsi: D:/ o C:/apps. Non usare altri percorsi dell'unità C: perché sono riservati. Si noti che l'installazione dei file nell'unità C: all'esterno della cartella C:/apps può comportare errori di installazione durante la nuova creazione dell'immagine del nodo.
6. In caso di modifica delle impostazioni o dei file di configurazione del servizio Hadoop a livello di sistema operativo, è possibile riavviare i servizi HDInsight in modo che possano rilevare qualsiasi impostazione a livello di sistema operativo, ad esempio le variabili di ambiente impostate negli script.


## <a name="runScriptAction"></a>Come eseguire un'azione script

È possibile usare le azioni script per personalizzare i cluster HDInsight usando il portale di gestione di Azure, PowerShell o .NET SDK per HDInsight. Per le istruzioni, vedere [Come usare l'azione script](./hdinsight-hadoop-customize-cluster/#howto). 


## <a name="sampleScripts"></a>Esempi di script personalizzati

Microsoft fornisce script di esempio per installare i componenti in un cluster HDInsight. Gli script di esempio e le istruzioni su come usarli sono disponibili nei collegamenti seguenti:

- [Installare e usare Spark 1.0 nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster Hadoop HDInsight][hdinsight-r-scripts]
- [Installare e usare Solr nei cluster HDInsight](../hdinsight-hadoop-solr-install)
- [Installare e usare Giraph nei cluster HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE] Lo script di esempio funziona solo con il cluster HDInsight versione 3.1 o successiva. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/).

## <a name="testScript"></a>Come testare lo script personalizzato con HDInsight Emulator

Un modo semplice per testare uno script personalizzato prima di usarlo in un comando di azione script di HDInsight consiste nell'eseguirlo manualmente in HDInsight Emulator. Installare Emulator localmente, in una macchina virtuale Azure IaaS Windows Server 2012 R2 o in un computer locale e verificare se il comportamento dello script è corretto. Windows Server 2012 R2 corrisponde alla macchina virtuale usata da HDInsight per i propri nodi.

Questa sezione descrive la procedura per usare HDInsight Emulator localmente per i test, che è simile alla procedura che prevede l'uso di una macchina virtuale.

**Installare HDInsight Emulator**: per eseguire localmente le azioni script, è necessario che sia installato HDInsight Emulator. Per istruzioni su come installarlo, vedere [Introduzione all'uso di HDInsight Emulator](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-get-started-emulator/)

**Impostare i criteri di esecuzione per Azure PowerShell:** aprire Microsoft Azure PowerShell ed eseguire come amministratore il seguente comando per impostare i criteri di esecuzione su  *LocalMachine* e su  *Unrestricted*.
 
	Set-ExecutionPolicy Unrestricted -Scope LocalMachine

Questi criteri devono essere senza restrizioni perché gli script non sono firmati.

**Scaricare l'azione script** da eseguire in una destinazione locale. Gli script Spark e R che è possibile eseguire localmente sono disponibili, ad esempio, nei seguenti percorsi:

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

**Eseguire gli script azione**: aprire una nuova istanza di Azure PowerShell in modalità di amministratore ed eseguire lo script di installazione Spark o R dal percorso locale in cui è stato salvato.

**Esempi di utilizzo**
Quando si usano i cluster Spark e R, i file di dati necessari potrebbero non essere presenti in HDInsight Emulator. Potrebbe essere quindi necessario caricare i file TXT rilevanti che contengono i dati richiesti in un percorso in HDFS che verrà poi usato per l'accesso ai dati. Ad esempio:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


In alcuni casi, uno script personalizzato può dipendere effettivamente dai componenti di HDInsight, ad esempio per rilevare se determinati servizi Hadoop sono attivi. In questo caso, sarà necessario testare gli script personalizzati eseguendone la distribuzione su un cluster HDInsight effettivo.


## <a name="debugScript"></a>Come eseguire il debug di uno script personalizzato

I log di errore dello script vengono archiviati insieme ad altri output nell'account di archiviazione predefinito specificato per il cluster al momento della creazione. I log vengono archiviati in una tabella denominata *u<\cluster-name-fragment><\time-stamp>setuplog*. Si tratta di log aggregati contenenti record provenienti da tutti i nodi, head e di lavoro, in cui lo script viene eseguito nel cluster.

È anche possibile accedere in remoto ai nodi cluster per visualizzare sia STDOUT che STDERR degli script personalizzati. I log in ciascun nodo sono specifici solo per tale nodo e sono accessibili da **C:\HDInsightLogs\DeploymentAgent.log**. Questi file di log registrano tutti gli output dello script personalizzato. Un frammento di log di esempio per un'azione script Spark è simile a quanto riportato di seguito:

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

[Personalizzare cluster HDInsight mediante l'azione script][hdinsight-cluster-customize] 


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/<!--HONumber=42-->
