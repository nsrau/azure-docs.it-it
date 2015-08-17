<properties 
	pageTitle="Accedere ai log applicazioni YARN Hadoop a livello di codice| Microsoft Azure" 
	description="Accedere ai log applicazioni a livello di codice su un cluster Hadoop in HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# Accedere ai log applicazioni YARN su Hadoop in HDInsight a livello di codice

Questo argomento spiega come enumerare a livello di codice le applicazioni YARN (Yet Another Resource Negotiator) che sono state completate in un cluster Hadoop in Azure HDInsight e come accedere a livello di codice ai log applicazioni senza che sia necessario connettersi ai cluster tramite RDP (Remote Desktop Protocol). In particolare, sono stati aggiunti un nuovo componente e una nuova API:

  1. Nei cluster HDInsight è abilitato il server generico della cronologia delle applicazioni. Si tratta di un componente all'interno del server di sequenza temporale YARN che gestisce l'archiviazione e il recupero di informazioni generiche sulle applicazioni completate.
  2. In Azure HDInsight .NET SDK sono disponibili API che consentono di enumerare a livello di codice le applicazioni eseguite nei cluster e di scaricare i log rilevanti specifici dell'applicazione o del contenitore (in formato di testo normale) per facilitare il debug di eventuali problemi relativi alle applicazioni.


## Prerequisiti

Per usare il codice presentato in questo argomento in un'applicazione .NET Framework, è necessario Azure HDInsight SDK. La build più recente dell'SDK pubblicata è disponibile nel sito [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started).

Per installare HDInsight SDK da un'applicazione di Visual Studio, passare al menu **Strumenti**, fare clic su **Gestione pacchetti NuGet** e quindi fare clic su **Console di Gestione pacchetti**. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Questo comando aggiunge librerie .NET per HDInsight e riferimenti a queste ultime nel progetto corrente di Visual Studio.


## <a name="YARNTimelineServer"></a>Server di sequenza temporale YARN

Il <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">server di sequenza temporale YARN</a> fornisce informazioni generiche sulle applicazioni completate, nonché informazioni sulle applicazioni specifiche del framework, tramite due interfacce diverse. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati a partire dalla versione 3.1.1.374. 
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.


Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati:

* ID applicazione, un identificatore univoco di un'applicazione 
* Utente che ha avviato l'applicazione 
* Informazioni sui tentativi effettuati per completare l'applicazione 
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico 

Nei cluster HDInsight queste informazioni verranno archiviate da Gestione risorse di Azure in un archivio di cronologia nel contenitore predefinito dell'account di archiviazione di Azure predefinito. Questi dati generici sulle applicazioni completate possono essere recuperati tramite un'API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Sono state aggiunte nuove API a HDInsight .NET SDK per semplificare il recupero di questi dati a livello di codice. Si noti che i dati generici possono essere recuperati anche eseguendo i comandi dell'interfaccia della riga di comando (CLI) YARN direttamente sui nodi del cluster (dopo la connessione al cluster tramite RDP).

## <a name="YARNAppsAndLogs"></a>Log e applicazioni YARN

YARN supporta diversi modelli di programmazione, tra cui MapReduce, separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. A tale scopo, vengono usati un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione, inoltre, può essere costituita da più *tentativi dell'applicazione* finalizzati al completamento in presenza di arresti anomali o di perdita della comunicazione tra gli oggetti AM e RM. Di conseguenza, i contenitori vengono concessi a uno specifico tentativo di un'applicazione. In un certo senso, un contenitore fornisce il contesto per l'unità di base del lavoro eseguito da un'applicazione YARN e tutto il lavoro svolto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti YARN][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN fornisce un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni mediante la funzionalità di [aggregazione dei log][log-aggregation]. La funzionalità di aggregazione dei log rende più deterministico l'accesso ai log applicazioni poiché aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia sotto forma di unico file di log aggregato per ogni nodo di lavoro nel file system predefinito dopo il completamento di un'applicazione. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro verranno sempre aggregati in un unico file, dando luogo a un solo file di log per ogni nodo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

	wasb:///app-logs/<user>/logs/<applicationId>

In questo percorso, *user* è il nome dell'utente che ha avviato l'applicazione e *applicationId* è l'identificatore univoco di un'applicazione assegnato dall'oggetto RM YARN.

I log aggregati non sono leggibili direttamente, poiché vengono scritti in un oggetto [TFile][T-file], un [formato binario][binary-format] indicizzato dal contenitore. YARN fornisce gli strumenti CLI per eseguire il dump di questi log come testo normale per le applicazioni o i contenitori di interesse. È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi YARN direttamente sui nodi del cluster (dopo la connessione tramite RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

La sezione seguente descrive come accedere ai log specifici dell'applicazione o del contenitore a livello di codice, senza dover usare RDP per connettersi ai cluster HDInsight.

## <a name="enumerate-and-download"></a>Enumerazione delle applicazioni e download di log a livello di codice

Per usare gli esempi di codice seguenti, è necessario soddisfare i prerequisiti indicati sopra scaricando la versione più recente di HDInsight .NET SDK. Vedere le istruzioni fornite.

Il codice riportato di seguito mostra come usare le nuove API per enumerare le applicazioni e scaricare i log relativi alle applicazioni completate.

> [AZURE.NOTE]Le API seguenti funzionano solo su cluster Hadoop con stato "Running" versione 3.1.1.374 o successiva. Aggiungere le direttive seguenti:

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Tali direttive fanno riferimento alle API appena definite nel codice seguente. Il frammento di codice seguente crea un client di cronologia delle applicazioni in un cluster con stato "Running" nella sottoscrizione:

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


A questo punto, è possibile usare il client di cronologia delle applicazioni per elencare le applicazioni completate, filtrare le applicazioni in base ai propri criteri e scaricare i log applicazioni rilevanti. Il frammento di codice seguente mostra come viene eseguita questa operazione a livello di codice:

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

Il codice sopra riportato elenca/individua le applicazioni di interesse usando il client di cronologia delle applicazioni e quindi scarica i log relativi a tali applicazioni in una cartella locale.

In alternativa, il frammento di codice seguente scarica i log relativi a un'applicazione con ID noto. L'ID applicazione è un identificatore univoco globale di un'applicazione, assegnato dall'oggetto RM. Tale identificatore viene creato usando l'ora di avvio dell'oggetto RM insieme a un contatore a incremento progressivo costante per le applicazioni inviate. L'ID applicazione ha il formato "applicazione\_&lt;ora-avvio-RM&gt;\_&lt;Contatore&gt;". Si noti che ID applicazione e ID processo sono distinti. L'ID processo è un concetto specifico del framework MapReduce, mentre l'ID applicazione è un concetto YARN indipendente dal framework. In YARN un ID processo identifica un processo MapReduce specifico, gestito dall'oggetto AM di un'applicazione MapReduce inviata all'oggetto RM.

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Se necessario, è possibile anche scaricare i log per tutti i contenitori, o per un contenitore specifico, usati da un'applicazione, come mostrato di seguito.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
 

<!---HONumber=August15_HO6-->