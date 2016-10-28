<properties
	pageTitle="Hub di notifica di Azure - Domande frequenti (FAQ)"
	description="Domande frequenti su progettazione/implementazione di soluzioni di Hub di notifica"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="erikre"
    keywords="notifica push, notifiche push, notifiche push iOS, notifiche push android, push ios, push android"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc" />

#Notifiche push sicure con Hub di notifica di Azure - Domande frequenti

##Generale
###1\. Qual è il modello di prezzo di Hub di notifica?
Hub di notifica è disponibile in tre livelli:

* **Gratuito**: ottenere fino a 1 milione di push per sottoscrizione al mese.
* **Basic**: ottenere fino a 10 milioni di push per sottoscrizione al mese come base, con opzioni di aumento della quota.
* **Standard**: ottenere fino a 10 milioni di push per sottoscrizione al mese come base, con opzioni di aumento della quota, oltre a funzionalità di telemetria avanzata.

Per informazioni dettagliate aggiornate, vedere la pagina [Prezzi di Hub di notifica]. Il prezzo viene definito a livello di sottoscrizione e in base al numero di notifiche push, per cui il numero di spazi dei nomi o di hub di notifica creati nella sottoscrizione di Azure è ininfluente.

Il livello **Gratuito** è offerto a scopo di sviluppo senza contratto di servizio. Anche se potrebbe essere un buon punto di partenza per coloro che vogliono esplorare le funzionalità delle notifiche push tramite Hub di notifica di Azure, questo livello potrebbe non essere ottimale per le applicazioni su media-larga scala.

I livelli **Basic** e **Standard** sono offerti per l'utilizzo di produzione con le seguenti funzionalità abilitate *solo per il livello Standard*:

- *Telemetria avanzata*: gli hub di notifica offrono numerose funzionalità per esportare i dati di telemetria e le informazioni di registrazione delle notifiche push per la visualizzazione e l'analisi offline.
- *Multi-tenancy*: ideale se si sta creando un'app per dispositivi mobili con Hub di notifica per supportare più tenant. In questo modo è possibile impostare le credenziali dei Servizi di notifica Push (PNS) a livello di spazio dei nomi di Hub di notifica per l'app e quindi isolare i tenant fornendo loro singoli hub in tale spazio dei nomi comune. Ciò agevola la manutenzione e mantiene le chiavi di firma di accesso condiviso per l'invio e la ricezione di notifiche dagli hub di notifica separate per ogni tenant, evitando la sovrapposizione tra tenant.
- *Push pianificati*: consente di pianificare le notifiche push, che verranno successivamente inserite nella coda e quindi inviate.
- *Importazione in blocco*: consente di importare le registrazioni in blocco.

###2\. Qual è il contratto di servizio di Hub di notifica?
Per i livelli **Basic** e **Standard** di Hub di notifica, Microsoft garantisce che per almeno il 99,9% del tempo le applicazioni configurate correttamente saranno in grado di inviare notifiche push o di eseguire operazioni di gestione delle registrazioni per un hub di notifica distribuito all'interno di un livello supportato. Per altre informazioni sul contratto di servizio, visitare la pagina del [contratto di servizio per Hub di notifica].

> [AZURE.NOTE] Il contratto di servizio non prevede garanzie per il segmento tra il servizio di notifica della piattaforma e il dispositivo perché gli hub di notifica dipendono dal provider di piattaforma esterno per il recapito della notifica push al dispositivo.

###3\. Quali clienti utilizzano Hub di notifica?
I clienti che usano Hub di notifica sono moltissimi. Di seguito sono indicati alcuni dei più importanti:

* Sochi 2014 – centinaia di gruppi di interesse, oltre 3 milioni di dispositivi, più di 150 milioni di notifiche spedite in 2 settimane. [Caso studio - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle Times - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing App – 10 milioni di dispositivi, invio di 3 milioni di notifiche al giorno.

###4\. Come eseguire l'aggiornamento o il downgrade degli hub di notifica per modificare il livello di servizio?
Passare al [portale di Azure classico], fare clic su Bus di servizio e fare clic sullo spazio dei nomi e quindi sull'hub di notifica. Nella scheda di scalabilità, sarà possibile modificare il livello di servizio degli hub di notifica.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##Progettazione e sviluppo
###1\. Quali piattaforme sul lato server sono supportate?
Sono forniti SDK ed [esempi completi] per .NET, Java, PHP, Python e Node.js in modo che un'app back-end possa essere configurata per comunicare con Hub di notifica usando una qualsiasi di queste piattaforme. Le API di Hub di notifica si basano sulle interfacce REST per cui è possibile decidere di comunicare direttamente con queste se non si vuole aggiungere una dipendenza aggiuntiva. Per informazioni più dettagliate, vedere la pagina dedicata alle [API REST di Hub di notifica].

###2\. Quali piattaforme client sono supportate?
È supportato l'invio di notifiche push alle piattaforme [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Per un elenco completo delle esercitazioni introduttive relative all'invio di notifiche push su queste piattaforme, visitare la pagina delle [esercitazioni introduttive su Hub di notifica].

###3\. Sono supportate le notifiche SMS/Email/Web?
Hub di notifica è progettato principalmente per l'invio di notifiche alle app per dispositivi mobili usando le piattaforme elencate sopra. Microsoft non fornisce funzionalità di invio di avvisi tramite posta elettronica o SMS, ma è possibile integrare piattaforme di terze parti che forniscono queste funzionalità con Hub di notifica per inviare notifiche push native usando [App per dispositivi mobili di Azure].

Hub di notifica non fornisce neanche un servizio predefinito per il recapito di notifiche push all'interno del browser. I clienti possono scegliere di implementare questa funzionalità mediante SignalR su piattaforme sul lato server supportate. Se si intende inviare notifiche alle app basate su browser nel sandbox Chrome, vedere l'[esercitazione per app Chrome].

###4\. Qual è la relazione tra le app per dispositivi mobili di Azure e gli hub di notifica di Azure e quando si usano?
Se si ha già un back-end dell'app per dispositivi mobili e si vuole solo aggiungere la funzionalità di invio di notifiche push, è possibile usare gli hub di notifica di Azure. Se si vuole configurare il back-end dell'app per dispositivi mobili da zero, è consigliabile valutare l'uso di App per dispositivi mobili di Azure. App per dispositivi mobili di Azure esegue automaticamente il provisioning di un hub di notifica per consentire l'invio di notifiche push dal back-end dell'app per dispositivi mobili. Il prezzo per le app per dispositivi mobili di Azure include le spese di base per un hub di notifica e deve essere corrisposto solo quando si superano i push inclusi. Per informazioni più dettagliate sui costi, vedere la pagina dei [prezzi del servizio app].

###5\. Quanti dispositivi sono supportati se si inviano notifiche push tramite Hub di notifica?
Per informazioni dettagliate sul numero di dispositivi supportati, vedere la pagina [Prezzi di Hub di notifica].

Per determinati scenari, se è necessario un supporto maggiore rispetto ai 10.000.000 di dispositivi registrati, [contattare](https://azure.microsoft.com/overview/contact-us/) direttamente il supporto di Azure per informazioni su come ridimensionare la soluzione.

###6\. Quante notifiche push si può inviare?
A seconda del piano selezionato, Azure passerà automaticamente a un piano superiore in base al numero di notifiche inviate nel sistema.

>[AZURE.NOTE] Il costo di utilizzo generale può aumentare in base al numero di notifiche push gestite. È importante conoscere i limiti dei piani esistenti descritti nella pagina [Prezzi di Hub di notifica].

I clienti usano Hub di notifica per inviare milioni di notifiche push ogni giorno. Non è necessario eseguire alcuna operazione particolare per ridimensionare la copertura delle notifiche push, purché si usi Hub di notifica di Azure.

###7\. Quanto tempo occorre perché le notifiche push inviate raggiungano il dispositivo?
Hub di notifica di Azure è in grado di elaborare almeno **1 milione di invii di notifiche push al minuto** in uno scenario di utilizzo normale dove il carico in ingresso è piuttosto uniforme e non presenta picchi. Questa velocità può variare in base al numero di tag, alla natura delle trasmissioni in ingresso e ad altri fattori esterni.

Nell'intervallo di tempo stimato per il recapito, il servizio è in grado di calcolare le destinazioni per piattaforma e di instradare i messaggi ai rispettivi servizi di recapito delle notifiche push in base ai tag/espressioni tag registrati. Da qui in avanti, è responsabilità dei Servizi di notifiche Push (PNS) inviare la notifica al dispositivo.

Un PNS non garantisce alcun contratto di servizio per il recapito delle notifiche. In genere, comunque, ma maggior parte delle notifiche push viene recapitata ai dispositivi di destinazione entro pochi minuti (solitamente meno di 10) dal momento in cui vengono inviati alla piattaforma. Potrebbero verificarsi casi isolati in cui potrebbe essere necessario più tempo.

>[AZURE.NOTE] Hub di notifica di Azure include anche un criterio per l'eliminazione delle notifiche che non vengono recapitate al PNS entro 30 minuti. Questo ritardo può verificarsi per diversi motivi, più comunemente perché il PNS sta rallentando l'applicazione.

###8\. La latenza è garantita?
Data la natura delle modifiche push, che vengono recapitate da un servizio di notifica push esterno specifico di una piattaforma, non esiste alcuna garanzia di latenza. In genere, la maggior parte delle notifiche push viene recapitata entro un paio di minuti.

###9\. Quali sono gli aspetti da considerare per la progettazione di una soluzione con spazi dei nomi e Hub di notifica?

####Ambiente/app per dispositivi mobili

* È consigliabile un Hub di notifica per app per dispositivi mobili per ogni ambiente.
* In uno scenario multi-tenant, ogni tenant dovrebbe avere un hub separato.
* È sempre sconsigliabile la condivisione di un Hub di notifica tra ambiente di test e di produzione in quanto tale condivisione può causare problemi durante l'invio delle notifiche. Ad esempio, Apple offre endpoint Sandbox e Production Push, che hanno credenziali separate.
* Per impostazione predefinita, è possibile inviare notifiche ai dispositivi registrati tramite il portale di Azure o il componente integrato di Azure in Visual Studio. La soglia è impostata su 10 dispositivi, che vengono selezionati in modo casuale dal pool di registrazione.

>[AZURE.NOTE] Se l'hub è stato inizialmente configurato con certificato sandbox Apple e successivamente riconfigurato per usare il certificato di produzione di Apple, i token di dispositivo precedenti non sono più validi con il nuovo certificato e causano l'esito negativo dei push. È consigliabile separare l'ambiente di test da quello di produzione e utilizzare hub diversi per diversi ambienti.

####Credenziali PNS

Quando un'app mobile viene registrata con il portale per sviluppatori di una piattaforma (ad esempio, Apple o Google e così via), si ottiene un identificatore app e token di sicurezza richiesti che un back-end dell'app deve fornire ai servizi di notifica Push della piattaforma per poter inviare notifiche push ai dispositivi. Questi token di sicurezza, che possono essere sotto forma di certificati (ad esempio per Apple iOS o Windows Phone) o chiavi di protezione (Google Android, Windows e così via), devono essere configurate in Hub di notifica. Questo avviene in genere a livello di hub di notifica, ma può essere eseguito anche a livello di spazio dei nomi in uno scenario multi-tenant.

####Spazi dei nomi

Gli spazi dei nomi possono essere usati anche per il raggruppamento di distribuzione. Possono essere usati anche per rappresentare tutti gli hub di notifica per tutti i tenant della stessa app nello scenario multi-tenant.

####Distribuzione geografica

La distribuzione geografica non è sempre fondamentale negli scenari di notifiche push. Tenere presente che i vari servizi di notifica push, come APNS, GCM e così via, che recapitano le notifiche push ai dispositivi non sono distribuiti in modo uniforme.

Se si ha un'applicazione che viene usata in tutto il mondo, è possibile creare più hub in diversi spazi dei nomi sfruttando la disponibilità del servizio Hub di notifica in diverse aree di Azure nel mondo.

>[AZURE.NOTE] Questo metodo comporta un aumento dei costi di gestione, in particolare quelli relativi alle registrazioni, per cui non è consigliabile e deve essere usato solo se strettamente necessario.

###10\. Le registrazioni devono essere eseguite dal back-end dell'app o direttamente tramite i dispositivi client?
Le registrazioni dal back-end dell'app sono utili quando è necessario eseguire l'autenticazione di un client prima di creare la registrazione o in presenza di tag che devono essere creati o modificati dal back-end dell'app in base a una logica dell'app. Per informazioni dettagliate, vedere [Guida alla registrazione del back-end] e [Guida alla registrazione del back-end - 2].

###11\. Qual è il modello di sicurezza del recapito di notifiche push?
Hub di notifica di Azure usa un modello di sicurezza basato sulla [firma di accesso condiviso (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). È possibile usare i token di firma di accesso condiviso a livello di spazio dei nomi radice o a livello di hub di notifica granulare. Questi token SAS possono essere impostati con regole di autorizzazione diverse, ad esempio le autorizzazioni di invio messaggi, le autorizzazioni di ascolto notifica e così via. Per altre informazioni, vedere il documento relativo al [modello di sicurezza di Hub di notifica].

###12\. Come si gestisce un payload sensibile nelle notifiche push?
Tutte le notifiche vengono recapitate ai dispositivi di destinazione dai servizi di notifica push (PNS) della piattaforma. Quando un mittente invia una notifica ad Hub di notifica di Azure, questa viene elaborata e passata al rispettivo PNS.

Tutte le connessioni tra mittente e Hub notifiche di Azure e PNS utilizzano HTTPS.

>[AZURE.NOTE] Gli Hub notifica di Azure non registrano il payload del messaggio in alcun modo.

Per l'invio di payload sensibili, tuttavia, si consiglia un modello di push sicuro in cui il mittente invia una notifica ping con un identificatore di messaggio al dispositivo senza il payload sensibile e, quando l'app sul dispositivo riceve il payload, è in grado di chiamare direttamente un'API sicura per recuperare i dettagli del messaggio. Nell'[esercitazione sul push sicuro in Hub di notifica] viene spiegato come implementare il modello descritto sopra.

##Operazioni
###1\. Cos'è il ripristino di emergenza?
È fornita una copertura del ripristino di emergenza dei metadati (nome dell'hub di notifica, stringa di connessione e altre informazioni fondamentali) sul lato del servizio. Quando viene attivato uno scenario di ripristino di emergenza, i dati delle registrazioni sono l'**unico segmento** dell'infrastruttura di Hub di notifica che andrà perso. Sarà necessario implementare una soluzione per ripopolare questi dati nel nuovo hub dopo il ripristino.

- *Passaggio 1*: Creare un Hub di notifica secondario in un controller di dominio diverso. È possibile crearlo in tempo reale al momento dell'evento di ripristino di emergenza oppure crearlo in fase iniziale. La scelta effettuata non è molto importante perché il provisioning di Hub di notifica è un processo relativamente rapido che richiede pochi secondi. Se viene creato inizialmente, si eviterà il rischio che l'evento di ripristino di emergenza influisca sulle funzionalità di gestione, per cui si tratta dell'opzione consigliata.

- *Passaggio 2*: Completare l'Hub di notifica secondario con le registrazioni dall'Hub di notifica primario. Non è consigliabile tentare di mantenere le registrazioni in entrambi gli hub e provare a mantenerli sincronizzati in tempo reale man mano che giungono le registrazioni. Questo in genere si è dimostrato un approccio non ottimale data la tendenza intrinseca delle registrazioni a raggiungere la scadenza sul lato PNS. Gli Hub di notifica le eliminano alla ricezione del feedback PNS sulle registrazioni scadute o non valida.

Si consiglia di utilizzare un backend dell'app con:

- Mantiene un set di registrazioni sul proprio lato per poter eseguire un inserimento in blocco nell'hub di notifica secondario in caso di ripristino di emergenza

**OR**

- Ottiene un regolare dump delle registrazioni dall'hub primario come backup e quindi esegue un inserimento di massa nell'Hub di notifica secondario

>[AZURE.NOTE] La funzionalità di esportazione/importazione di registrazioni disponibile nel livello Standard è descritta nel documento relativo all'[esportazione/importazione di registrazioni].

Se non si ha un back-end, all'avvio dell'app in uno qualsiasi dei dispositivi di destinazione verrà eseguita una nuova registrazione nell'hub di notifica secondario e alla fine tutti i dispositivi attivi saranno registrati nell'hub di notifica secondario.

Lo svantaggio è che, per un certo periodo, i dispositivi in cui non sono state aperte le app non riceveranno le notifiche.

###2\. È disponibile una funzionalità di log di controllo?
Tutte le operazioni di gestione degli hub di notifica passano ai log delle operazioni, esposti nel [portale di Azure classico].

##Monitoraggio e risoluzione dei problemi
###1\. Quali funzionalità di risoluzione dei problemi sono disponibili?
Gli hub di notifica di Azure forniscono numerose funzionalità per la risoluzione dei problemi comuni, in particolare nello scenario frequente delle notifiche eliminate. Vedere i dettagli nel white paper dedicato alla [risoluzione dei problemi di Hub di notifica].

###2\. Sono disponibili le funzionalità di telemetria?
Hub di notifica di Azure consente la visualizzazione di dati di telemetria nel [portale di Azure classico]. Per informazioni dettagliate sulle metriche disponibili, vedere la pagina relativa alle [metriche di Hub di notifica].

>[AZURE.NOTE] La riuscita delle notifiche indica solo che le notifiche push sono state recapitate al servizio di notifica push esterno, ad esempio servizio APN per Apple, GCM per Google e così via. È il PNS che dovrà recapitare la notifica ai dispositivi di destinazione. In genere, il PNS non espone le metriche di recapito a terze parti.

È offerta anche la possibilità di esportare i dati di telemetria a livello di codice (nel livello **Standard**). Per informazioni dettagliate, vedere l'[esempio di metriche di Hub di notifica].

[portale di Azure classico]: https://manage.windowsazure.com
[Prezzi di Hub di notifica]: http://azure.microsoft.com/pricing/details/notification-hubs/
[contratto di servizio per Hub di notifica]: http://azure.microsoft.com/support/legal/sla/
[Caso studio - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API REST di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[esercitazioni introduttive su Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[esercitazione per app Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Guida alla registrazione del back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Guida alla registrazione del back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[modello di sicurezza di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[esercitazione sul push sicuro in Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[risoluzione dei problemi di Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[metriche di Hub di notifica]: https://msdn.microsoft.com/library/dn458822.aspx
[esempio di metriche di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[esportazione/importazione di registrazioni]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[esempi completi]: https://github.com/Azure/azure-notificationhubs-samples
[App per dispositivi mobili di Azure]: https://azure.microsoft.com/it-IT/services/app-service/mobile/
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/

<!---HONumber=AcomDC_0706_2016-->