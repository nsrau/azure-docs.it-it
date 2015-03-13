<properties 
	pageTitle="Accedere ai log dell'applicazione HDInsight a livello di codice| Azure" 
	description="Accedere ai log dell'applicazione HDInsight a livello di codice." 
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
	ms.date="11/21/2014" 
	ms.author="bradsev"/>

# Accedere ai log dell'applicazione HDInsight a livello di codice

Questo argomento spiega come enumerare a livello di codice le applicazioni YARN che sono state completate in un cluster Hadoop in HDInsight e come accedere a livello di codice ai log dell'applicazione senza che sia necessaria la presenza di RDP nei cluster. In particolare, sono stati aggiunti un nuovo componente e una nuova API:

  1. Nei cluster HDInsight è stato abilitato il server generico della cronologia delle applicazioni. Si tratta di un componente all'interno del server di sequenza temporale YARN che gestisce l'archiviazione e il recupero di informazioni generiche sulle applicazioni completate.
  2. Sono state aggiunte nuove API a Azure HDInsight .NET SDK per consentire all'utente di enumerare a livello di codice le applicazioni eseguite sui cluster e di scaricare i log rilevanti specifici dell'applicazione o del contenitore (in formato testo normale) per facilitare il debug di eventuali problemi relativi alle applicazioni.


## Prerequisiti

Azure HDInsight SDK è necessario per usare il codice presentato in questo argomento in un'applicazione .NET. La più recente build pubblicata di SDK è disponibile all'indirizzo [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). 

Per installare HDInsight SDK da un'applicazione di Visual Studio, passare al menu **Strumenti**, fare clic su **Gestione pacchetti NuGet**, quindi fare clic su **Console di Gestione pacchetti**. Eseguire i seguenti comandi nella console per installare i pacchetti.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Questo comando aggiunge librerie .NET per HDInsight e riferimenti a queste ultime nel progetto corrente di Visual Studio.

## Contenuto dell'articolo

- [Server di sequenza temporale YARN](#YARNTimelineServer)
- [Applicazioni e log YARN](#YARNAppsAndLogs)
- [Enumerazione delle applicazioni e download di log a livello di codice](#enumerate-and-download)


## <a name="YARNTimelineServer"></a>Server di sequenza temporale YARN

Il <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Server di sequenza temporale YARN</a> fornisce informazioni generiche sulle applicazioni completate, nonché informazioni sulle applicazioni specifiche del framework, tramite due differenti interfacce. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati nella versione 3.1.1.374 o successiva. 
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.


Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati: 

* **ApplicationId** (un identificatore univoco di un'applicazione) 
* **utente** che ha avviato l'applicazione 
* informazioni sui **tentativi** effettuati per completare l'applicazione 
* **contenitori** usati da qualsiasi tentativo di applicazione specificato 

Nei cluster di HDInsight queste informazioni verranno archiviate dal gestore delle risorse in un archivio di cronologia nel contenitore predefinito dell'account di archiviazione predefinito. Questi dati generici sulle applicazioni completate possono essere recuperati tramite un'API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Sono state aggiunte nuove API a HDInsight .NET SDK per semplificare il recupero di questi dati a livello di codice. Si noti che i dati generici possono essere recuperati anche eseguendo i comandi CLI YARN direttamente sui nodi del cluster (dopo la connessione al cluster tramite RDP).

## <a name="YARNAppsAndLogs"></a>Applicazioni e log YARN

YARN (Yet Another Resource Negotiator) supporta più modelli di programmazione (MapReduce è uno di essi) separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. A tale scopo vengono usati un  *Resource Manager* (RM) globale,  *Node Managers* (NM) per nodo di lavoro e  *Application Masters* (AM) per applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'RM funziona con gli NM per concedere queste risorse come *Containers*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori. 

Ciascuna applicazione, inoltre, può essere costituita da più *Application Attempts* per eseguire il completamento in presenza di arresti anomali del sistema o di perdita della comunicazione tra un AM e un RM. Di conseguenza, i contenitori vengono concessi a uno specifico tentativo di un'applicazione. In un certo senso, un contenitore fornisce il contesto per l'unità di base del lavoro eseguito da un'applicazione YARN e tutto il lavoro eseguito all'interno del contesto di un contenitore viene eseguito sul singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti YARN][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN fornisce un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazione mediante la funzionalità di [aggregazione dei log][log-aggregation]. La funzionalità di aggregazione dei log rende più deterministico l'accesso ai log applicazione poiché aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia sotto forma di file di log aggregato per ogni nodo di lavoro nel file system predefinito dopo il completamento dell'applicazione. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro verranno sempre aggregati in un unico file, dando luogo a un solo file di log per ogni nodo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log viene abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso riportato di seguito:

	wasb:///app-logs/<user>/logs/<applicationId>

dove "*user*" è il nome dell'utente che ha avviato l'applicazione e "*applicationId*" è l'identificatore univoco di un'applicazione assegnato dal gestore delle risorse YARN.

I log aggregati non sono direttamente leggibili poiché vengono scritti in un [TFile][T-file], [formato binario][binary-format] indicizzato dal contenitore. YARN fornisce gli strumenti CLI per eseguire il dump di questi log come testo normale per le applicazioni o i contenitori di interesse. È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi YARN direttamente sui nodi del cluster (dopo la connessione tramite RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

La sezione seguente descrive il modo in cui è possibile accedere ai log specifici dell'applicazione o del contenitore a livello di codice, senza che sia necessaria la presenza di RDP sui cluster HDInsight.

## <a name="enumerate-and-download"></a>Enumerazione delle applicazioni e download di log a livello di codice

Per usare gli esempi di codice riportati di seguito, è necessario soddisfare i prerequisiti sopra indicati scaricando la versione più recente di HDInsight .NET SDK. Vedere le istruzioni fornite.

Il codice riportato di seguito mostra come usare le nuove API per enumerare le applicazioni e scaricare i log relativi alle applicazioni completate.

> [AZURE.NOTE] Le API seguenti funzionano solo su cluster Hadoop "Running" versione 3.1.1.374 o successiva. Aggiungere le direttive seguenti.

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Tali direttive fanno riferimento alle API appena definite nel codice seguente. Il seguente frammento di codice crea un client di cronologia delle applicazioni in un cluster "Running" della sottoscrizione.

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight Client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


 A questo punto, è possibile usare il client di cronologia delle applicazioni per elencare le applicazioni completate, filtrare le applicazioni in base ai propri criteri e scaricare i log applicazione rilevanti. Il frammento di codice riportato di seguito mostra come questa operazione viene eseguita a livello di codice.

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

Il codice sopra riportato elenca/individua le applicazioni di interesse usando il client di cronologia delle applicazioni e scarica quindi i log relativi a tali applicazioni in una cartella locale. 

In alternativa, il frammento di codice riportato di seguito scarica i log relativi a un'applicazione con "ApplicationId" (identificatore univoco) noto. ApplicationId è un identificatore univoco globale di un'applicazione assegnato dal gestore delle risorse. Tale identificatore viene creato usando l'ora di inizio del gestore delle risorse insieme a un contatore a incremento progressivo costante per le applicazioni presentate. Il formato dell'ApplicationId è "application\_&lt;ora-avvio-RM&gt;\_&lt;Contatore&gt;". Si noti che ApplicationId e JobId sono entità diverse. JobId è un concetto specifico del framework MapReduce mentre ApplicationId è un concetto YARN indipendente dal framework. In YARN un JobId identifica uno specifico processo MapReduce come gestito dal master applicazioni di un'applicazione MapReduce inviata al gestore delle risorse.

	// Download application logs for an application whose application Id is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Se necessario, è possibile scaricare i log per i singoli contenitori o per contenitori specifici usati da un'applicazione come descritto di seguito.

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



[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/browse/HADOOP-3315
[binary-format]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf 
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
<!--HONumber=42-->
