<properties 
	pageTitle="Hub di notifica di Azure - Linee guida sulla diagnostica" 
	description="Linee guida sulla diagnostica dei problemi comuni relativi a Hub di notifica di Azure." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="piyushjo"/>

#Hub di notifica di Azure - Linee guida sulla diagnostica

Una delle domande più frequenti dei clienti che usano Hub di notifica di Azure riguarda il motivo per cui una notifica inviata dal back-end dell'applicazione non viene visualizzata nel dispositivo client: dove e perché sono state eliminate le notifiche e come risolvere il problema? In questo articolo verranno esaminate le varie ragioni per cui le notifiche possono essere eliminate o non giungere ai dispositivi. Verrà anche illustrato come analizzare e individuare la causa principale. 

Prima di tutto, è fondamentale comprendere il modo in cui Hub di notifica di Azure invia le notifiche push ai dispositivi.
![][0]

In un tipico flusso di invio di notifiche il messaggio viene inviato dal **back-end dell'applicazione** a **Hub di notifica di Azure**, che a sua volta elabora tutte le registrazioni tenendo conto dei tag configurati e delle espressioni tag per determinare i "destinatari", ovvero tutte le registrazioni che devono ricevere la notifica push. Queste registrazioni possono coprire alcune o tutte le piattaforme supportate: iOS, Google, Windows, Windows Phone, Kindle e Baidu per Andoid in Cina. Una volta determinate le destinazioni, Hub di notifica invia notifiche push, suddivise su più batch di registrazioni, al **servizio di notifica push (PNS)** specifico della piattaforma del dispositivo, ad esempio APNS per Apple, GCM per Google e così via. Hub di notifica esegue l'autenticazione con il rispettivo PNS in base alle credenziali impostate dall'utente nella pagina di configurazione di Hub di notifica nel portale di Azure. Il PNS inoltra quindi le notifiche ai **dispositivi client** corrispondenti. Questa è la modalità ottimale per la piattaforma per il recapito delle notifiche push e tenere presente che l'ultima parte del recapito delle notifiche avviene tra il PNS della piattaforma e il dispositivo. Pertanto si hanno quattro componenti principali: *client*, *back-end dell'applicazione*, *Hub di notifica di Azure (NH)* e *servizio di notifica push (PNS)*; uno di questi componenti può essere la causa dell'eliminazione delle notifiche. Informazioni più dettagliate su questa architettura sono disponibili nelle informazioni generali su [Hub di notifica di Azure].

Il mancato recapito delle notifiche può avvenire nella fase di test iniziale/staging, che potrebbe indicare un problema di configurazione, oppure può avvenire nell'ambiente di produzione, con l'eliminazione di alcune o tutte le notifiche, che indica un problema più profondo a livello di modello di messaggistica o applicazione. Nella seguente sezione verranno esaminati alcuni scenari di notifiche eliminate, dai più comuni ai più rari, alcuni dei quali possono sembrare ovvi, altri molto meno. 

###Configurazione errata di Hub di notifica di Azure 

Hub di notifica di Azure deve eseguire l'autenticazione nel contesto dell'applicazione dello sviluppatore affinché possa inviare notifiche al PNS corrispondente. Ciò è reso possibile dagli sviluppatori creando un account sviluppatore con la rispettiva piattaforma (Google, Apple, Windows e così via) e quindi registrando l'applicazione, dove ottengono le credenziali che devono essere configurate nella sezione di configurazione di Hub di notifica nel portale di Azure. Se non perviene nessuna notifica, prima di tutto occorre assicurarsi che siano configurate le credenziali corrette in Hub di notifica, verificandone la corrispondenza con l'applicazione creata con l'account sviluppatore specifico della piattaforma. L'[esercitazione introduttiva] è utile per ottenere istruzioni dettagliate su questa procedura. Ecco alcuni errori di configurazione comuni:

1. **Generale**
 
	a) assicurarsi che il nome dell'hub di notifica (privo di errori di digitazione) sia lo stesso:

	- Dove si esegue la registrazione dal client 
	- Dove si inviano le notifiche dal back-end  
	- Dove sono state configurate le credenziali PNS e 
	- Di cui sono state configurate le credenziali della firma di accesso condiviso nel client e nel back-end. 
		
	b) Assicurarsi di usare le stringhe corrette di configurazione della firma di accesso condiviso nel client e nel back-end dell'applicazione. In linea generale, è necessario usare **DefaultListenSharedAccessSignature** sul client e **DefaultFullSharedAccessSignature** sul back-end dell'applicazione (che fornisce l'autorizzazione per poter inviare la notifica a Hub di notifica)

2. **Configurazione di Apple Push Notification Service (APNS)**
 
	È necessario gestire due hub diversi, uno per la produzione e un altro a scopo di test. Ciò significa che il certificato da usare nell'ambiente sandbox e il certificato da usare nell'ambiente di produzione devono essere caricati in hub separati. Non tentare di caricare certificati di tipo diverso in uno stesso hub poiché questo può provocare errori di notifica a valle. Se dovesse capitare di caricare inavvertitamente certificati di tipo diverso nello stesso hub, è consigliabile eliminare l'hub e ricominciare da zero. Se per qualche motivo non fosse possibile eliminare l'hub, sarà necessario quanto meno eliminare tutte le registrazioni esistenti dall'hub. 

3. **Configurazione di Google Cloud Messaging (GCM)** 

	a) Assicurarsi di abilitare "Google Cloud Messaging per Android" nel progetto cloud. 
	
	![][2]
	
	b) Creare una chiave server durante l'acquisizione delle credenziali, che Hub di notifica userà per l'autenticazione con GCM. 
	
	![][3]
	
	c) Assicurarsi di avere configurato un ID progetto sul client, un'entità totalmente numerica che è possibile ottenere dal dashboard:
	
	![][1]

###Problemi relativi all'applicazione

1) **Tag/Espressioni tag**

Se si usano tag o espressioni tag per segmentare i destinatari, è sempre possibile che al momento dell'invio della notifica non venga trovato alcun destinatario in base ai tag o alle espressioni tag specificate nella chiamata di invio. Si consiglia di esaminare le registrazioni per assicurarsi che vi siano tag corrispondenti quando si invia la notifica, quindi verificare la ricezione della notifica solo dai client con tali registrazioni. Se ad esempio è stato usato il tag "Politica" in tutte le registrazioni con Hub di notifica e si invia una notifica con il tag "Sport", la notifica non viene inviata ad alcuni dispositivo. Un caso complesso con espressioni tag è quello in cui la registrazione è stata eseguita solo con "Tag A" OPPURE "Tag B", ma durante l'invio delle notifiche si usa "Tag A & & Tag B" come destinazione. Nella sezione dei suggerimenti per la diagnostica in autonomia sono indicati alcuni modi per esaminare le registrazioni con i tag di cui dispongono. 

2) **Problemi relativi ai modelli**

Se si usano i modelli, seguire le indicazioni riportate in [Linee guida sui modelli]. 

3) **Registrazioni non valide**

Supponendo che Hub di notifica sia stato configurato correttamente e che tutti i tag/espressioni tag siano stati usati correttamente, con conseguente individuazione di destinatari validi a cui inviare le notifiche, Hub di notifica genera diversi batch di elaborazione in parallelo, ognuno dei quali invia messaggi a un set di registrazioni. 

> [AZURE.NOTE] Dato che l'elaborazione avviene in parallelo, l'ordine di recapito delle notifiche non è garantito. 

A questo punto, Hub di notifica di Azure è ottimizzato per un modello di recapito "at-most-once" per i messaggi, che comporta un tentativo di deduplicazione per fare in modo che nessuna notifica venga recapitata più di una volta a un dispositivo. A questo scopo, prima dell'invio effettivo del messaggio al PNS vengono esaminate le registrazioni per garantire che venga inviato un solo messaggio per ID dispositivo. Dato che ogni batch viene inviato al PNS, che a sua volta accetta e convalida le registrazioni, è possibile che il PNS rilevi un errore in una o più registrazioni in un batch, restituisca un errore a Hub di notifica di Azure e interrompa l'elaborazione, eliminando completamente il batch interessato. Questo vale soprattutto per gli APNS che usano un protocollo di flusso TCP. Vista l'ottimizzazione per il recapito "at-most-once", tenere presente che non vi saranno nuovi tentativi per il batch non riuscito poiché non si sa con certezza se il batch è stato eliminato interamente o parzialmente dal PNS. Il PNS, comunque, indica a Hub di notifica di Azure quale registrazione ha causato l'errore e sulla base di questo feedback è possibile rimuovere la registrazione dal database. Ciò significa che un batch di registrazioni o un suo subset potrebbe non ricevere una notifica, ma siccome la registrazione errata è stata eliminata, al successivo tentativo di invio è più probabile che l'operazione abbia esito positivo. Visto il numero crescente dei dispositivi di destinazione (alcuni clienti inviano notifiche a milioni di dispositivi), l'eliminazione sporadica di un batch non fa molta differenza in termini di percentuale complessiva di dispositivi che ricevono le notifiche, ma se invece le notifiche inviate sono poche e sono presenti alcuni errori PNS, è possibile che la mancata ricezione interessi tutte le notifiche o gran parte di esse. Se questo comportamento si verifica frequentemente, è necessario identificare le registrazioni errate ed eliminarle. È necessario sicuramente eliminare le registrazioni create manualmente perché sono la causa più comune delle notifiche non recapitate. Nel caso di un ambiente di test, è possibile eliminare direttamente tutte le registrazioni poiché le app, all'apertura nei dispositivi, tenteranno di ripetere la registrazione in Hub di notifica, assicurando così la validità di tutte le registrazioni create successivamente. 

###Problemi relativi a PNS

Quando il PNS corrispondente riceve il messaggio di notifica, si occuperà di recapitare la notifica al dispositivo. Hub di notifica di Azure non è coinvolto in questa operazione e non controlla in alcun modo se e quando la notifica verrà recapitata al dispositivo. Dato che i servizi di notifica della piattaforma sono piuttosto affidabili, le notifiche tendono a raggiungere i dispositivi in pochi secondi dal PNS. In caso di limitazione del PNS, però, Hub di notifica di Azure applica una strategia di backoff esponenziale e se il PNS rimane irraggiungibile per 30 minuti viene applicato un criterio che determina la scadenza e l'eliminazione definitiva di quei messaggi. 

Se un PNS tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal PNS per un periodo di tempo limitato e viene recapitata al dispositivo quando ritorna disponibile. Viene archiviata solo una notifica recente per una determinata app. Se vengono inviate più notifiche quando il dispositivo è offline, ogni nuova notifica determina la rimozione di quella precedente. Il comportamento che prevede che venga mantenuta solo la notifica più recente prende il nome di unione di notifiche in APNS e compressione in GCM (che usa una chiave di compressione). Se il dispositivo rimane offline a lungo, le notifiche archiviate per esso vengono rimosse. 
Fonte: [Linee guida su APNS] e [Linee guida su GCM]

Con Hub di notifica di Azure è possibile passare una chiave di unione tramite un'intestazione HTTP mediante l'API generica `SendNotification` (ad esempio  `SendNotificationAsync` per .NET SDK) che accetta anche le intestazioni HTTP che vengono passate al rispettivo PNS così come sono. 

##Suggerimenti per l'utente relativi alla diagnostica

Di seguito vengono illustrate le diverse strategie per diagnosticare e identificare la causa principale dei problemi relativi a Hub di notifica:

###Verificare le credenziali

1. **Portale per sviluppatori PNS**

	Verificarle nel rispettivo portale per sviluppatori PNS (APNS, GCM, WNS e così via) usando l'[Esercitazione introduttiva].

2. **Portale di gestione di Azure**

	Passare alla scheda Configura per esaminare e confrontare le credenziali con quelle ottenute dal portale per sviluppatori PNS. 

	![][4]

###Verificare le registrazioni

1. **Visual Studio**

	Se si usa Visual Studio per lo sviluppo è possibile connettersi a Microsoft Azure e visualizzare e gestire una serie di servizi di Azure, incluso Hub di notifica, da Esplora server. Questo risulta particolarmente utile per l'ambiente sviluppo/test. 

	![][9]

	È possibile visualizzare e gestire tutte le registrazioni nell'hub, comodamente suddivise in registrazioni di piattaforma, native o di modello, tutti i tag, gli identificatori di PNS, gli ID di registrazione e le date di scadenza. È anche possibile modificare una registrazione in tempo reale; questo è utile, ad esempio, se si vogliono modificare i tag. 

	![][8]
 
	> [AZURE.NOTE] La funzionalità di Visual Studio per la modifica delle registrazioni deve essere usata solo durante le attività di sviluppo/test su un numero limitato di registrazioni. Se è necessario correggere le registrazioni in blocco, è possibile usare la funzionalità di esportazione/importazione delle registrazioni descritta in [Esportazione/Importazione di registrazioni] (disponibile solo nel livello Standard)

2. **Service Bus Explorer**

	Molti clienti usano Service Bus Explorer (descritto in [Service Bus Explorer]) per visualizzare e gestire l'hub di notifica. Si tratta di un progetto open source disponibile su code.microsoft.com ([codice di Service Bus Explorer]).

###Verificare le notifiche di messaggi

1. **Portale di Azure**

	Passare alla scheda "Debug" per inviare notifiche di prova ai client senza la necessità di un servizio back-end operativo. 

	![][7]

2. **Visual Studio**

	È anche possibile inviare notifiche comodamente da Visual Studio:

	![][10]

	Altre informazioni sulla funzionalità di esplorazione di Hub di notifica di Azure di Visual Studio sono disponibili qui: 
	
	- [Informazioni generali su Esplora server di Visual Studio]
	- [Post di blog su Esplora server di Visual Studio - 1]
	- [Post di blog su Esplora server di Visual Studio - 2]

###Eseguire il debug di notifiche non riuscite/esaminare l'esito della notifica

**Proprietà EnableTestSend**

Quando si invia una notifica tramite Hub di notifica, questa viene inizialmente inserita nella coda per consentire a Hub di notifica di eseguire elaborazioni allo scopo di individuare tutte le destinazioni e infine viene inviata al PNS. Pertanto, quando si usa l'API REST o un SDK client, l'esito corretto della chiamata di invio significa solo che il messaggio è stato inserito nella coda in Hub di notifica. Non fornisce indicazioni su ciò che è accaduto quando Hub di notifica ha inviato il messaggio al PNS. Se la notifica non arriva al dispositivo client, è possibile che si sia verificato un errore quando Hub di notifica ha tentato di recapitare il messaggio al PNS, ad esempio che siano state superate le dimensioni massime per il payload consentite dal PNS oppure che le credenziali configurate in Hub di notifica non siano valide. 
Per informazioni sugli errori del PNS, è stata introdotta una proprietà denominata [funzionalità EnableTestSend]. Questa proprietà viene abilitata automaticamente quando si inviano messaggi di prova dal portale o dal client di Visual Studio e consente di visualizzare informazioni di debug dettagliate. Può essere usata tramite API, secondo l'esempio di .NET SDK in cui ora è disponibile, e verrà aggiunta a tutti gli SDK dei client. Per usarla con la chiamata REST, è sufficiente aggiungere un parametro di stringa di query denominato "test" alla fine della chiamata di invio, ad esempio 

	https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Esempio (.NET SDK)*
 
Si supponga di usare .NET SDK per inviare una notifica di tipo avviso popup nativa:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`  indicherà semplicemente  `Enqueued` alla fine dell'esecuzione, senza alcun approfondimento su ciò che è accaduto al push. 
Ora è possibile usare la proprietà booleana `EnableTestSend` durante l'inizializzazione di  `NotificationHubClient` per ottenere lo stato dettagliato relativo agli errori PNS verificatisi durante l'invio della notifica. In questo caso la chiamata di invio richiederà più tempo perché restituisce il risultato solo dopo il recapito della notifica a PNS da parte di Hub di notifica. 
 
	bool enableTestSend = true;
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
	
	var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
	Console.WriteLine(outcome.State);
	
	foreach (RegistrationResult result in outcome.Results)
	{
	    Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
	}

*Output di esempio*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Questo messaggio indica che nell'hub di notifica sono state configurate credenziali non valide oppure che si è verificato un problema relativo alle registrazioni nell'hub. La procedura consigliata consiste nell'eliminare la registrazione e lasciare che il client la ricrei prima di inviare il messaggio. 
 
> [AZURE.NOTE] Si noti che l'uso di questa proprietà è molto limitato e deve quindi essere riservato solo ad ambienti di sviluppo/test con un set limitato di registrazioni. Le notifiche di debug vengono inviate solo a 10 dispositivi. Inoltre è previsto un limite di 10 invii al minuto per l'elaborazione di debug. 

###Esaminare i dati di telemetria 

1. **Usare il portale di Azure**

	Il portale di Azure consente di ottenere una rapida panoramica di tutte le attività in Hub di notifica. 
	
	a) Nella scheda "Dashboard" è presente una vista aggregata delle registrazioni, delle notifiche e degli errori per piattaforma. 
	
	![][5]
	
	b) Nella scheda "Monitoraggio" è possibile aggiungere molte altre metriche specifiche della piattaforma per ottenere informazioni più approfondite sugli errori specifici del PNS restituiti quando Hub di notifica tenta di inviare la notifica al PNS. 
	
	![][6]
	
	c) È consigliabile esaminare prima di tutto **Messaggi in arrivo**, **Operazioni di registrazione** e **Notifiche operazioni riuscite** e quindi passare alla scheda relativa alla piattaforma per esaminare gli errori specifici del PNS. 
	
	d) Se Hub di notifica non è configurato con le impostazioni di autenticazione corrette, verrà visualizzato l'errore di autenticazione del PNS. Questo errore suggerisce appunto che è necessario controllare le credenziali PNS. 

2) **Accesso programmatico**

Per informazioni dettagliate: 

- [Accesso alla telemetria a livello di codice]
- [Esempio di accesso alla telemetria tramite API] 

> [AZURE.NOTE] Molte funzionalità correlate alla telemetria, come l'**esportazione/importazione di registrazioni** e l'**accesso alla telemetria tramite API**, sono disponibili solo al livello Standard. Se si tenta di usare queste funzionalità in un livello Basic o Gratuito, viene visualizzato un apposito messaggio di eccezione quando si usa l'SDK e il messaggio HTTP 403 (Accesso negato) quando vengono usate direttamente dalle API REST. Assicurarsi di passare al livello Standard tramite il portale di gestione di Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Panoramica dell'Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-overview/
[Esercitazioni introduttive]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Linee guida sui modelli]: https://msdn.microsoft.com/library/dn530748.aspx 
[Linee guida su APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Linee guida su GCM]: http://developer.android.com/google/gcm/adv.html
[Esportazione/Importazione di registrazioni]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[Codice di ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Informazioni generali su Esplora server di Visual Studio]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Post di blog su Esplora server di Visual Studio - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Post di blog su Esplora server di Visual Studio - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Funzionalità EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Accesso alla telemetria a livello di codice]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Esempio di accesso alla telemetria tramite API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel


<!--HONumber=49-->