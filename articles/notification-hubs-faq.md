<properties 
	pageTitle="Hub di notifica di Azure - Domande frequenti (FAQ)" 
	description="Domande frequenti su progettazione/implementazione di soluzioni di Hub di notifica" 
	services="notification-hubs" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="piyushjo" />

#Hub di notifica di Azure - Domande frequenti (FAQ)

##Generale
###1.	Qual è il prezzo di Hub di notifica?
Hub di notifica viene offerto in tre livelli:  *gratuito*, *base* e  *standard*. Ulteriori informazioni - [Prezzi di Hub di notifica]. Il prezzo viene addebitato a livello di sottoscrizione e in base al numero di push, per cui non è importante il numero di hub di notifica o spazi dei nomi di cui si dispone. 
Il livello gratuito è offerto a scopo di sviluppo senza contratto di servizio. I livelli Base e Standard sono offerti per l'utilizzo di produzione con le seguenti funzionalità abilitate solo per il livello Standard:

- *Trasmissione*: il livello base limita il numero di tag di Hub di notifica a 3000 (si applica > 5 dispositivi). Se la dimensione dei destinatari è superiore a 3000, è necessario passare al livello Standard. 
- *Telemetria avanzata*: il livello base non consente l'esportazione dei dati di telemetria o registrazioni. Se si desidera poter esportare i dati di telemetria per l'analisi e la visualizzazione non in linea, è necessario passare al livello Standard. 
- *Multi-tenancy*: se si sta creando un'app mobile con Hub di notifica per supportare più tenant, è necessario considerare il passaggio al livello Standard. In questo modo è possibile impostare le credenziali dei Servizi di notifica Push (PNS) a livello di spazio dei nomi di Hub di notifica per l'app e quindi isolare i tenant fornendo loro singoli hub in tale spazio dei nomi comune. Ciò fornisce facilità di manutenzione e mantiene le chiavi di firma di accesso condiviso per inviare e ricevere notifiche da hub di notifica separati per ogni tenant evitando la sovrapposizione tra tenant. 

###2.	Che cos'è il contratto di servizio?
Per i livelli di base e standard di Hub di notifica, è possibile garantire che almeno per il 99,9% del tempo le applicazioni correttamente configurate saranno in grado di inviare notifiche o eseguire operazioni di gestione registrazione per un Hub di notifica distribuito all'interno di un livello di Hub di notifica base o standard. Per ulteriori informazioni sul contratto di servizio, visitare la pagina Contratto di servizio qui: [contratto di servizio di Hub di notifica]. Si noti che non vi sono garanzie di contratto di servizio per il segmento tra il Servizio di notifica della piattaforma e il dispositivo poiché gli hub di notifica dipendono dal provider di piattaforma esterno per il recapito della notifica al dispositivo. 
 
###3.	Quali clienti utilizzano Hub di notifica?
Diversi clienti utilizzano Hub di notifica. Di seguito ne sono indicati alcuni:

* Sochi 2014 - centinaia di gruppi di interesse, oltre 3 milioni di dispositivi, più di 150 milioni di notifiche spedite in 2 settimane. [Caso studio - Sochi]
* Skanska - [Caso studio - Skanska]
* Seattle Times - [Caso studio - Seattle Times]
* Mural.ly - [Caso studio - Mural.ly]
* 7Digital - [Caso studio - 7Digital]
* Bing App - 10 milioni di dispositivi, invio di 3 milioni di notifiche al giorno
 
##Progettazione e sviluppo
###1.	Quali piattaforme sul lato servizio sono supportate?
Sono forniti SDK ed esempi per .NET, Java, PHP, Python, Node. js in modoack-end in modo che un'app possa essere configurata per comunicare con Hub di notifica utilizzando una qualsiasi di queste piattaforme. Le API di Hub di notifica si basano sull'interfaccia REST per cui è possibile scegliere di comunicare direttamente. Ulteriori informazioni qui - [NH - API REST]
 
###2.	Quali piattaforme per dispositivi sono supportate?
È supportato l'invio di notifiche alle piattaforme Apple iOS, Android, Windows Universal e Windows Phone, Kindle, Android China (via Baidu), Xamarin (iOS e Android), Chrome Apps. Esercitazioni introduttive passo passo per queste piattaforme sono disponibili qui: [NH - Esercitazioni introduttive]
 
###3.	Sono supportate le notifiche SMS/Email/Web?
Hub di notifica è progettato principalmente per inviare notifiche alle app mobili utilizzando le piattaforme elencate sopra. Microsoft non fornisce funzionalità di invio di posta elettronica o SMS, tuttavia piattaforme di terze parti che forniscono queste funzionalità possono essere integrate con Hub di notifica per inviare notifiche push nativo tramite Servizi mobili di Azure. Ad esempio, in questa esercitazione viene illustrato come inviare notifiche SMS tramite Servizi Mobili di Azure - [Inviare SMS con Servizi mobili]
Microsoft non fornisce, inoltre, una notifica push interna al browser preconfezionata. I clienti possono scegliere di implementarla mediante SignalR. È fornita un'esercitazione per l'invio di notifiche push alle app Chrome, che funzioneranno nel browser Google Chrome. Cedere - [Esercitazione per app Chrome]
 
###4.	Qual è la relazione tra Servizi mobili di Azure e Hub di notifica di Azure e quando si utilizzano? 
Se si dispone di un back-end di app mobile esistente e si desidera solo aggiungere la funzionalità di invio di notifiche push, è necessario utilizzare Hub di notifica di Azure. Per impostare il back-end dell'app mobile da zero, è opportuno valutare l'utilizzo di Servizi mobili di Azure. Un servizio mobile di Azure esegue automaticamente il provisioning di un Hub di notifica per consentire l'invio di notifiche push dal back-end dell'app mobile. Il prezzo dei Servizi mobili di Azure include le spese di base per un Hub di notifica e deve essere pagato solo quando si superano i push inclusi. Ulteriori informazioni qui - [Prezzi di Servizi mobili]
 
###5.	Quanti dispositivi possono essere supportati?
Nel livello Base e Standard non sono previsti limiti per il numero di dispositivi attivi che possono ricevere notifiche. Per informazioni dettagliate, vedere: [Prezzi di Hub di notifica]
 
###6.	Quante notifiche push si può inviare? 
I clienti utilizzano gli Hub di notifica di Azure per inviare milioni di notifiche push al giorno. Non è necessario eseguire alcuna operazione aggiuntiva per scalare Hub di notifica,  che viene calcolato automaticamente in base al numero di notifiche che attraversano il sistema. Si noti che il prezzo non dipende dalle notifiche push servite. 
 
###7.	Quanto tempo occorre perché le notifiche raggiungano il dispositivo?
Hub di notifica di Azure è in grado di elaborare almeno 1 milione di invii in 1 minuto in uno scenario di utilizzo normale dove il carico in ingresso è piuttosto uniforme e non presenta picchi. Questa velocità può variare a seconda del numero di tag, della la natura degli invii in ingresso e così via. In tale intervallo di tempo è possibile calcolare le destinazioni per piattaforma e instradare i messaggi ai rispettivi servizi di notifica push a seconda delle espressioni di tag registrate. Da qui in avanti, è responsabilità dei Servizi di notifiche Push (PNS) inviare la notifica al dispositivo. I PNS non garantiscono alcun contratto di servizio per il recapito delle notifiche, tuttavia in genere la vasta maggioranza dei messaggi viene recapitata ai dispositivi entro pochi minuti (< 10 minuti) dal momento in cui vengono inviati alla piattaforma Microfost. Potrebbero verificarsi casi isolati in cui potrebbe essere necessario più tempo. Hub di notifica di Azure dispone anche di un criterio in grado di eliminare le notifiche che non vengono recapitate al PNS entro 30 minuti. Questo ritardo può verificarsi per diversi motivi, più comunemente perché il PNS sta rallentando l'applicazione. 
 
###8.	La latenza è garantita?
Poiché le notifiche push vengono recapitate da un servizio notifica push specifico di una piattaforma esterna, non esiste alcuna garanzia di latenza. In genere, la maggior parte delle notifiche viene recapitata entro pochi minuti. 
 
###9.	Quali sono gli aspetti da considerare per la progettazione di una soluzione con spazi dei nomi e Hub di notifica? 
*App mobile/ambiente:*
È consigliabile un Hub di notifica per app mobile per ogni ambiente. In uno scenario multi-tenant - ogni tenant deve avere un hub separato. 
È sempre sconsigliabile la condivisione di un Hub di notifica tra ambiente di test e di produzione in quanto tale condivisione può causare problemi durante l'invio delle notifiche. Ad esempio, Apple offre endpoint Sandbox e Production Push, che hanno credenziali separate. Se l'hub è stato inizialmente configurato con certificato sandbox Apple e successivamente riconfigurato per utilizzare il certificato di produzione di Apple, i token di dispositivo precedenti potrebbero diventare non validi con il nuovo certificato e causare la mancate esecuzione dei push. È consigliabile separare l'ambiente di test da quello di produzione e utilizzare hub diversi per diversi ambienti. 

*Credenziali PNS:*
Quando un'app mobile viene registrata con il portale per sviluppatori di una piattaforma (ad esempio, Apple o Google e così via), si ottiene un identificatore app e token di sicurezza richiesti che un back-end dell'app deve fornire ai servizi di notifica Push della piattaforma per poter inviare notifiche push ai dispositivi. Questi token di sicurezza, che possono essere sotto forma di certificati (ad esempio per Apple iOS o Windows Phone) o chiavi di protezione (Google Android, Windows), devono essere configurate in Hub di notifica e così via. Questo avviene in genere a livello di hub di notifica, ma può essere eseguito anche a livello di spazio dei nomi in uno scenario multi-tenant. 

*Spazi dei nomi:*
Gli spazi dei nomi possono essere utilizzati anche per il raggruppamento di distribuzione. Possono inoltre essere utilizzati per rappresentare tutti gli hub di notifica per tutti i tenant della stessa app nello scenario multi-tenant. 

*Distribuzione geografica:* 
La distribuzione geografica non è sempre fondamentale in caso di notifiche push. È utile notare che i diversi servizi di notifica push (ad esempio, APNS, GCM e così via) che recapitano le notifiche push ai dispositivi non sono distribuiti in modo uniforme. Tuttavia, se si dispone di un'applicazione che viene utilizzata in tutto il mondo è possibile creare diversi hub in diversi spazi dei nomi sfruttando il vantaggio della disponibilità del servizio Hub di notifica in diverse aree di Azure nel mondo. Si noti che questo metodo comporta un aumento dei costi di gestione in particolare quelli relative alle registrazioni, per cui non è molto consigliabile e deve essere utilizzato solo se è veramente necessario. 
 
###10.	È necessario eseguire registrazioni dal back-end dell'applicazione o direttamente dai dispositivi?
Le registrazioni dal back-end dell'app sono utili quando è necessario eseguire l'autenticazione di un client prima di creare la registrazione o in presenza si tag che devono essere creati o modificati dal back-end dell'app in base a una logica di applicazione. Ulteriori informazioni sono disponibili qui - [Guida alla registrazione back-end] e [Guida alla registrazione del back-end - 2]
 
###11.	Che cos'è il modello di protezione?
Gli Hub di notifica di Azure utilizza un modello di protezione basato sulla firma di accesso condiviso (SAS). È possibile utilizzare i token SAS a livello di spazio dei nomi radice o a livello di hub di notifica granulari. Questi token SAS possono essere impostati con regole di autorizzazione diverse, ad esempio le autorizzazioni di invio messaggi, le autorizzazioni di ascolto notifica e così via. Ulteriori informazioni qui - [Modello di protezione NH] 
 
###12.	Come è possibile gestire il payload sensibile nelle notifiche? 
Tutte le notifiche vengono recapitate ai dispositivi dai servizi di notifica (PNS) delle piattaforme. Quando un mittente invia una notifica ad Hub di notifica di Azure, questa viene elaborata e passata al rispettivo PNS. Tutte le connessioni tra mittente e Hub notifiche di Azure e PNS utilizzano HTTPS. Gli Hub notifica di Azure non registrano il payload del messaggio in alcun modo. 
Per l'invio di payload sensibili, tuttavia, si consiglia un modello di push sicuro dove il mittente invia una notifica  'ping' con un identificatore di messaggio al dispositivo senza il payload sensibile e quando l'app sul dispositivo riceve il payload viene chiamata direttamente una API di back-end dell'app sicura per recuperare i dettagli del messaggio. L'esercitazione per implementare il modello è qui: [NH - esercitazione sul push sicuro]
 
##Operations
###1.	Cos'è il ripristino di emergenza?
Un ripristino di emergenza dei metadati alla nostra estremità (nome dell'Hub di notifica, stringa di connessione e così via). In caso di un ripristino di emergenza, i dati delle registrazioni andranno persi rendendo necessaria l'individuazione di una soluzione per ripopolare l'hub. 

- *Passaggio 1* - Creare un Hub di notifica secondario in un controller di dominio diverso. È possibile crearlo in tempo reale al momento dell'evento di ripristino di emergenza oppure è possibile crearne uno da get go. La scelta è ininfluente perché il provisioning NH è un processo rapido nell'ordine di pochi secondi. Disponendo di uno dall'inizio si sarà anche protetti da eventi di ripristino di emergenza che influiscono sulle funzionalità di gestione. 

- *Passaggio 2* - Completare l'Hub di notifica secondario con le registrazioni dall'Hub di notifica primario. Si consiglia di non tentare di gestire le registrazioni in entrambi gli hub e di provare di mantenerli sincronizzati in tempo reale come registrazioni in arrivo, che non vengono utilizzate a causa della natura intrinseca delle registrazioni di raggiungere la scadenza per messo di PNS. Gli Hub di notifica le eliminano alla ricezione del feedback PNS sulle registrazioni scadute o non valida. 

Si consiglia di utilizzare un backend dell'app con:

- Gestisce il set di registrazioni alla fine in modo da poter eseguire un inserimento di massa nell'hub di notifica secondario in caso di ripristino di emergenza, OPPURE

- Ottiene un regolare dump delle registrazioni dall'hub primario come backup e quindi esegue un inserimento di massa nell'Hub di notifica secondario  

(la funzionalità di esportazione/importazione registrazioni è disponibile nel livello Standard, come descritto di seguito: [Esportazione/importazione di registrazioni]) 
 
Se non si dispone di un back-end all'avvio dell'app nei dispositivi, viene eseguita una nuova registrazione nell'hub di notifica secondario, che alla fine avrà tutti i dispositivi attivi registrati. Lo svantaggio è che ci sarà un periodo di tempo in cui i dispositivi in cui non sono state aperte le app non riceveranno notifiche. 
 
###2.	È disponibile una funzionalità di log di controllo?
Tutte le operazioni di gestione degli hub di notifica passano ai log delle operazioni, esposti nel portale di gestione di Azure. 
 
##Monitoraggio e risoluzione dei problemi
###1.	Quali funzionalità di risoluzione dei problemi sono disponibili?
Gli hub di notifica di Azure forniscono numerose funzionalità a scopo di risoluzione dei problemi comuni, in particolare nello scenario più comune relativo alle notifiche eliminate. Visualizzare i dettagli in questo white paper sulla risoluzione dei problemi - [NH - risoluzione dei problemi]
 
###2.	Sono disponibili le funzionalità di telemetria?
Gli Hub di notifica di Azure consentono la visualizzazione di dati di telemetria nel portale di gestione di Azure. Dettagli delle metriche disponibili sono disponibili qui: [NH - Metriche].
Si noti che le notifiche sono considerate riuscite quando sono state recapitate al Servizio di notifica Push esterno (APNS di Apple, GCM per Google e così via), per cui è responsabilità del PNS recapitare le notifiche ai dispositivi, e il PNS non espone tali metriche. 
È offerta inoltre la possibilità di esportare i dati di telemetria a livello di codice (nel livello Standard). Vedere questo esempio per ulteriori informazioni - [NH - Esempio di metriche]

[Prezzi di Hub di notifica]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Contratto di servizio di Hub di notifica]: http://azure.microsoft.com/support/legal/sla/
[Caso studio - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Caso studio - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Caso studio - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Caso studio - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Caso studio - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Esercitazioni introduttive]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Inviare SMS con Servizi mobili]: http://azure.microsoft.com/documentation/articles/partner-twilio-mobile-services-how-to-use-voice-sms/
[Esercitazione per app Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Prezzi di Servizi mobili]: http://azure.microsoft.com/pricing/details/mobile-services/
[Guida alla registrazione del back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx 
[Guida alla registrazione del back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modello di protezione NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx.
[NH - Esercitazione sul push sicuro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - Risoluzione dei problemi]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - Metriche]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - Esempio di metriche]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Esportazione/importazione di registrazioni]: https://msdn.microsoft.com/library/dn790624.aspx
 



<!--HONumber=47-->
