<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.5.1" 
   description="Note sulla versione di Azure SDK per .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="juliako"/>


# Note sulla versione di Azure SDK per .NET 2.5.1

Questo documento contiene le note sulla versione per Azure SDK per.NET 2.5.1.

##Note sulla versione di Azure SDK per .NET 2.5.1

Di seguito sono riportate le nuove funzionalità e i nuovi aggiornamenti inclusi in Azure SDK per .NET 2.5.1.

- Nuove funzionalità e scenari correlati alle **estensioni Strumenti Web**. 

	- Siti Web di Azure è stato rinominato servizio app di Azure. Per altre informazioni, vedere [Azure App Service e i servizi di Azure esistenti](app-service-changes-existing-services.md).
	- È stato aggiunto il supporto per le app per le API di Azure (anteprima) per consentire ai clienti di pubblicare progetti ASP.NET come app per le API e quindi usare il movimento del client Aggiungi > App per le API di Azure nei progetti C# per generare codice basato sulla struttura dell'app per le API distribuita. 
	- Il nodo Siti Web in Esplora server è stato deprecato in favore del nodo Servizio app di Azure, che contiene il supporto per il raggruppamento, basato sui gruppi di risorse, di app per le API, di app per dispositivi mobili e di app Web di Azure.
	- È stato aggiunto il supporto per le app per dispositivi mobili di Azure (anteprima) per consentire ai clienti di creare nuovi progetti di app per dispositivi mobili, di aggiungere controller per queste ultime, pubblicare i progetti ed eseguire il debug in modalità remota.
	- Il movimento del client Aggiungi > App per le API di Azure ora supporta file JSON Swagger locali, pertanto gli sviluppatori di API possono usare pacchetti NuGet di terze parti, come Swashbuckle, per generare codice Swagger o modificarlo manualmente. In questo modo, gli sviluppatori di client potranno usare le funzionalità di generazione di codice per usare qualsiasi endpoint Swagger nei progetti C#. 
	- Le finestre di dialogo di pubblicazione di app Web e per le API sono state migliorate per supportare il concetto di raggruppamento delle risorse del portale di Azure e la selezione/creazione di gruppi di risorse e piani del servizio app di Azure sono rappresentati nella nuova finestra di dialogo relativa al provisioning di app Web e per le API. 
	- I nodi relativi alle app per le API di Azure in Esplora server forniscono accesso al collegamento diretto alle app per le API nel portale di Azure, oltre che ad altre funzionalità quali log in streaming e debug remoto.

	Per informazioni sui problemi noti e sulle limitazioni in Azure SDK .NET 2.5.1, vedere [questa](app-service-release-notes.md#known_issues_2_5_1) sezione più avanti nell'articolo.


- Nuove funzionalità e scenari correlati a **HDInsight Tools** in Visual Studio sono ora disponibili in questa versione.
	- Convalida locale di script Hive. Fare clic sul pulsante Convalida script nella barra degli strumenti per verificare se sono presenti errori nello script. 
	- Debug migliorato dei processi Hive. È ora possibile eseguire il debug di processi Hive tramite l'accesso ai log Yarn in Visual Studio. Se l'applicazione presenta problemi di prestazioni, l'esame dei log YARN può fornire informazioni utili.
	- (Anteprima pubblica) Completamento automatico di parole chiave e supporto IntelliSense per Hive. Per consentire la creazione di script Hive, HDInsight Tools per Visual Studio offre ora il completamento automatico delle parole chiave e il supporto IntelliSense per Hive.
	- Supporto per Storm. È ora possibile usare HDInsight Tools per Visual Studio per sviluppare topologie, Sput e Bolt di Storm in C#. Sarà quindi possibile inviare la topologia sviluppata in un cluster Storm e verificare lo stato della topologia, Bolt o Spout. È possibile usare i log di sistema e del cliente per procedere alla risoluzione dei problemi relativi alle topologie, Bolt o Sput di Storm. È anche possibile usare asset JAVA esistenti in Storm su HDInsight.
	
	Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Problemi noti e limitazioni di Azure SDK per .NET 2.5.1

- App per le API di Azure è visibile come destinazione di sviluppo per app per dispositivi mobili. Le app Web dovrebbero essere l'unica destinazione per app per dispositivi mobili fino a una versione successiva. 
- Il provisioning di app per le API può avere esito positivo ma l'aggiornamento dello stato di avanzamento nella finestra delle attività del servizio app di Azure potrebbe funzionare in modo intermittente. La soluzione alternativa consiste nel verificare lo stato della nuova app per le API di Azure nel portale di Azure. 
- L'uso di File > Nuovo progetto > App per le API > F5 può dare luogo a un errore HTTP poiché non è presente un file /index.html predefinito. La soluzione alternativa consiste nell'individuazione manuale dell'URL di /api/values. 
- Le icone di Esplora server possono apparire appiattite in modo intermittente. Il problema si risolve con il riavvio di VS. 
- Se viene generata un'eccezione durante il provisioning di app Web o per le API (ad esempio errori di quota superata o correlati al nome del gateway dell'app per le API di Azure) tali errori mostrano testo JSON non elaborato. 
- Problemi intermittenti di creazione del progetto quando Application Insights è selezionato nel momento della creazione del progetto.
- Occasionalmente, nel codice client dell'app per le API di Azure mancano degli spazi dei nomi, che dovranno essere inclusi manualmente (o importati automaticamente tramite cue di Visual Studio) per completare la compilazione del codice. 
- I progetti di app per dispositivi mobili devono essere pubblicati in app Web, ma è necessario scegliere un sito creato come app mobile nel portale di Azure, poiché i progetti di app per dispositivi mobili richiedono un database. 
- La pagina iniziale per le app per dispositivi mobili usa il termine "servizi mobili" anziché il termine "app per dispositivi mobili". 
- Il completamento della creazione di un progetto di app mobile può richiedere fino a un minuto. 
- Durante il provisioning di app per le API (in alcuni casi) viene restituito un errore dall'API di Azure che riflette una situazione in cui le autorizzazioni potrebbero non essere impostate correttamente, sebbene sia stato correttamente eseguito il provisioning dell'app per le API e quest'ultima sia pronta per l'uso. È possibile impostare le autorizzazioni manualmente tramite il portale di Azure.
- Application Insights non è supportato in modelli di app per le API e di app per dispositivi mobili.
- I progetti di app per le API non possono essere usati insieme a progetti di servizio cloud.
- I modelli di progetti di app per le API sono disponibili solo in C#.
- L'utilizzo di app per le API tramite il menu di scelta rapida "Aggiungi client di app per le API di Azure" è supportato solo in C#.

 

<!---HONumber=August15_HO6-->