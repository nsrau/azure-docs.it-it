---
title: Hub di notifica di Azure - Domande frequenti (FAQ)
description: Domande frequenti su progettazione/implementazione di soluzioni di Hub di notifica
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: notifica push, notifiche push, notifiche push iOS, notifiche push android, push ios, push android
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notifiche push sicure con Hub di notifica di Azure - Domande frequenti
## <a name="general"></a>Generale
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.  Che cos'è la struttura di risorse di Hub di notifica?

Gli Hub di notifica dispongono di due livelli di risorse, hub e spazi dei nomi. Un hub è una singola risorsa push in grado di contenere le informazioni push multipiattaforma di un'app. Uno spazio dei nomi è una raccolta di hub in un'area.

Il mapping generale consigliato inserisce uno spazio dei nomi corrispondente a un'app. Nello spazio dei nomi è possibile configurare un hub di produzione che funzioni con l'app di produzione, un hub di test che funzioni con l'app di test e così via.

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.  Qual è il modello di prezzo di Hub di notifica?
Per informazioni dettagliate aggiornate, vedere la pagina [Prezzi di Hub di notifica] . Hub di notifica comporta dei costi a livello di spazio dei nomi (vedere la struttura delle risorse menzionata in precedenza per altri dettagli a riguardo) e offre tre livelli:

* **Gratuito**: un buon punto di partenza per esplorare le funzionalità push, ma sconsigliato per applicazioni di produzione. Sono inclusi 500 dispositivi e 1 milioni di push al mese per ogni spazio dei nomi, senza contratto di servizio.
* **Base**: il livello Base, come quello Standard, è consigliato per piccole applicazioni di produzione. Sono inclusi 200.000 dispositivi e 10 milioni di push al mese per ogni spazio dei nomi, con opzioni di aumento della quota.
* **Standard**: livello consigliato per le applicazioni di produzione di medie o grandi dimensioni. Sono inclusi 10 milioni di dispositivi e 10 milioni di push al mese per ogni spazio dei nomi, con opzioni di aumento della quota e funzionalità di telemetria avanzata.

Ecco alcune funzionalità eccezionali del livello Standard:
* *Telemetria avanzata*: Hub di notifica offre la telemetria per messaggio per tenere traccia delle richieste push e i commenti di Platform Notification System per il debug.
* *Multi-tenancy*: è possibile lavorare con le credenziali del Platform Notification System (PNS) a livello dello spazio dei nomi. In questo modo è possibile suddividere facilmente i tenant in hub all'interno del medesimo spazio dei nomi.
* *Push pianificati*: è possibile pianificare per inviare le notifiche.

### <a name="2--what-is-the-notification-hubs-sla"></a>2.  Qual è il contratto di servizio di Hub di notifica?
Per i livelli **Basic** e **Standard** di Hub di notifica, Microsoft garantisce che per almeno il 99,9% del tempo le applicazioni configurate correttamente saranno in grado di inviare notifiche push o di eseguire operazioni di gestione delle registrazioni per un hub di notifica distribuito all'interno di un livello supportato. Per altre informazioni sul contratto di servizio, visitare la pagina del [contratto di servizio per Hub di notifica](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Poiché le notifiche push dipendono da Platform Notification System di terze parti (Apple APN, Google FCM e così via), non sono presenti contratti di servizio per la consegna di tali messaggi. Dopo che Hub di notifica invia i batch ai PNS (con contratto di servizio), sta ai PNS consegnare i push (nessun contratto di servizio).

### <a name="3--which-customers-are-using-notification-hubs"></a>3.  Quali clienti utilizzano Hub di notifica?
I clienti che usano Hub di notifica sono moltissimi. Di seguito sono indicati alcuni dei più importanti:

* Sochi 2014 – centinaia di gruppi di interesse, oltre 3 milioni di dispositivi, più di 150 milioni di notifiche spedite in 2 settimane. [Caso studio - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle Times - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing App – 10 milioni di dispositivi, invio di 3 milioni di notifiche al giorno.

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4. Come eseguire l'aggiornamento o il downgrade di un hub o di uno spazio dei nomi a un livello diverso?
Aprire il [portale di Azure], aprire la sezione degli spazi dei nomi di Hub di notifica o la sezione Hub di notifica, fare clic sulla risorsa da aggiornare e passare al piano tariffario nella navigazione. È possibile aggiornare al livello desiderato, ma ci sono alcune considerazioni da fare:
* Il piano tariffario aggiornato verrà applicato a *tutti* gli hub nello spazio dei nomi in uso.
* Se si esegue il downgrade e il conteggio dei dispositivi supera il limite del livello verso cui si sta tentando di effettuare il downgrade, sarà necessario eliminare i dispositivi per rientrare nel limite prima di eseguire il downgrade.


## <a name="design--development"></a>Progettazione e sviluppo
### <a name="1--which-server-side-platforms-do-you-support"></a>1.  Quali piattaforme sul lato server sono supportate?
Gli utenti dispongono di SDK server per .NET, Java, Node.js, PHP e Python. Inoltre, le API di Hub di notifica si basano su interfacce REST, pertanto è possibile lavorare direttamente con le API REST se si usano piattaforme diverse o se non si desidera una dipendenza aggiuntiva. Per informazioni più dettagliate, vedere la pagina dedicata alle [API REST di Hub di notifica].

### <a name="2--which-client-platforms-do-you-support"></a>2.  Quali piattaforme client sono supportate?
È supportato l'invio di notifiche push alle piattaforme [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Vedere la pagina [Esercitazioni introduttive] per altre informazioni.

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.  Sono supportate le notifiche SMS/Email/Web?
Hub di notifica è progettato principalmente per l'invio di notifiche alle app per dispositivi mobili e non offre funzionalità di posta elettronica o SMS. Esistono tuttavia piattaforme di terze parti che forniscono queste funzionalità e che possono essere integrate con Hub di notifica per inviare notifiche push native tramite [Servizi mobili di Azure].

Hub di notifica non fornisce neanche un servizio predefinito per il recapito di notifiche push all'interno del browser. I clienti possono scegliere di implementare questa funzionalità mediante SignalR su piattaforme sul lato server supportate. Se si intende inviare notifiche alle app basate su browser nel sandbox Chrome, vedere l' [esercitazione per app Chrome].

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.    Qual è la relazione tra le app per dispositivi mobili di Azure e gli hub di notifica di Azure e quando si usano?
Se si ha già un back-end dell'app per dispositivi mobili e si vuole solo aggiungere la funzionalità di invio di notifiche push, è possibile usare gli hub di notifica di Azure. Se si vuole configurare il back-end dell'app per dispositivi mobili da zero, è consigliabile valutare l'uso di App per dispositivi mobili di Azure. App per dispositivi mobili di Azure esegue automaticamente il provisioning di un hub di notifica per consentire l'invio di notifiche push dal back-end dell'app per dispositivi mobili. Il prezzo per le app per dispositivi mobili di Azure include le spese di base per un hub di notifica e deve essere corrisposto solo quando si superano i push inclusi. Per informazioni più dettagliate sui costi, vedere la pagina dei [prezzi del servizio app] .

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.    Quanti dispositivi sono supportati se si inviano notifiche push tramite Hub di notifica?
Per informazioni dettagliate sul numero di dispositivi supportati, vedere la pagina [Prezzi di Hub di notifica] .

Per determinati scenari, se è necessario un supporto maggiore rispetto ai 10.000.000 di dispositivi registrati, [contattare](https://azure.microsoft.com/overview/contact-us/) direttamente il supporto di Azure per informazioni su come ridimensionare la soluzione.

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.    Quante notifiche push si può inviare?
A seconda del piano selezionato, Azure passerà automaticamente a un piano superiore in base al numero di notifiche inviate nel sistema.

> [!NOTE]
> Il costo di utilizzo generale può aumentare in base al numero di notifiche push gestite. È importante conoscere i limiti dei piani esistenti descritti nella pagina [Prezzi di Hub di notifica] .
> 
> 

I clienti usano Hub di notifica per inviare milioni di notifiche push ogni giorno. Non è necessario eseguire alcuna operazione particolare per ridimensionare la copertura delle notifiche push, purché si usi Hub di notifica di Azure.

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.    Quanto tempo occorre perché le notifiche push inviate raggiungano il dispositivo?
Hub di notifica di Azure è in grado di elaborare almeno **1 milione di invii di notifiche push al minuto** in uno scenario di utilizzo normale dove il carico in ingresso è piuttosto uniforme e non presenta picchi. Questa velocità può variare in base al numero di tag, alla natura delle trasmissioni in ingresso e ad altri fattori esterni.

Nell'intervallo di tempo stimato per il recapito, il servizio è in grado di calcolare le destinazioni per piattaforma e di instradare i messaggi ai rispettivi servizi di recapito delle notifiche push in base ai tag/espressioni tag registrati. Da qui in avanti, è responsabilità dei Servizi di notifiche Push (PNS) inviare la notifica al dispositivo.

Un PNS non garantisce alcun contratto di servizio per il recapito delle notifiche. In genere, comunque, ma maggior parte delle notifiche push viene recapitata ai dispositivi di destinazione entro pochi minuti (solitamente meno di 10) dal momento in cui vengono inviati alla piattaforma. Potrebbero verificarsi casi isolati in cui potrebbe essere necessario più tempo.

> [!NOTE]
> Hub di notifica di Azure include anche un criterio per l'eliminazione delle notifiche che non vengono recapitate al PNS entro 30 minuti. Questo ritardo può verificarsi per diversi motivi, più comunemente perché il PNS sta rallentando l'applicazione.
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.    La latenza è garantita?
Data la natura delle modifiche push, che vengono recapitate da un servizio di notifica push esterno specifico di una piattaforma, non esiste alcuna garanzia di latenza. In genere, la maggior parte delle notifiche push viene recapitata entro un paio di minuti.

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.    Quali sono gli aspetti da considerare per la progettazione di una soluzione con spazi dei nomi e Hub di notifica?
#### <a name="mobile-appenvironment"></a>Ambiente/app per dispositivi mobili
* È consigliabile un Hub di notifica per app per dispositivi mobili per ogni ambiente.
* In uno scenario multi-tenant, ogni tenant dovrebbe avere un hub separato.
* È sempre sconsigliabile la condivisione di un Hub di notifica tra ambiente di test e di produzione in quanto tale condivisione può causare problemi durante l'invio delle notifiche. Ad esempio, Apple offre endpoint Sandbox e Production Push, che hanno credenziali separate.
* Per impostazione predefinita, è possibile inviare notifiche ai dispositivi registrati tramite il portale di Azure o il componente integrato di Azure in Visual Studio. La soglia è impostata su 10 dispositivi, che vengono selezionati in modo casuale dal pool di registrazione.

> [!NOTE]
> Se l'hub è stato inizialmente configurato con certificato sandbox Apple e successivamente riconfigurato per usare il certificato di produzione di Apple, i token di dispositivo precedenti non sono più validi con il nuovo certificato e causano l'esito negativo dei push. È consigliabile separare l'ambiente di test da quello di produzione e utilizzare hub diversi per diversi ambienti.
> 
> 

#### <a name="pns-credentials"></a>Credenziali PNS
Quando un'app mobile viene registrata con il portale per sviluppatori di una piattaforma (ad esempio, Apple o Google e così via), si ottiene un identificatore app e token di sicurezza richiesti che un back-end dell'app deve fornire ai servizi di notifica Push della piattaforma per poter inviare notifiche push ai dispositivi. Questi token di sicurezza, che possono essere sotto forma di certificati (ad esempio per Apple iOS o Windows Phone) o chiavi di protezione (Google Android, Windows e così via), devono essere configurate in Hub di notifica. Questo avviene in genere a livello di hub di notifica, ma può essere eseguito anche a livello di spazio dei nomi in uno scenario multi-tenant.

#### <a name="namespaces"></a>Spazi dei nomi
Gli spazi dei nomi possono essere usati anche per il raggruppamento di distribuzione.  Possono essere usati anche per rappresentare tutti gli hub di notifica per tutti i tenant della stessa app nello scenario multi-tenant.

#### <a name="geo-distribution"></a>Distribuzione geografica
La distribuzione geografica non è sempre fondamentale negli scenari di notifiche push. Tenere presente che i vari servizi di notifica push, come APNS, GCM e così via, che recapitano le notifiche push ai dispositivi non sono distribuiti in modo uniforme.

Se si ha un'applicazione che viene usata in tutto il mondo, è possibile creare più hub in diversi spazi dei nomi sfruttando la disponibilità del servizio Hub di notifica in diverse aree di Azure nel mondo.

> [!NOTE]
> Questo metodo comporta un aumento dei costi di gestione, in particolare quelli relativi alle registrazioni, per cui non è consigliabile e deve essere usato solo se strettamente necessario.
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.    Le registrazioni devono essere eseguite dal back-end dell'app o direttamente tramite i dispositivi client?
Le registrazioni dal back-end dell'app sono utili quando è necessario eseguire l'autenticazione di un client prima di creare la registrazione o in presenza di tag che devono essere creati o modificati dal back-end dell'app in base a una logica dell'app. Per informazioni dettagliate, vedere [Registrazione dal back-end dell'app] e [Gestione registrazione].

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.    Qual è il modello di sicurezza del recapito di notifiche push?
Hub di notifica di Azure usa un modello di sicurezza basato sulla [firma di accesso condiviso (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). È possibile usare i token di firma di accesso condiviso a livello di spazio dei nomi radice o a livello di hub di notifica granulare. Questi token SAS possono essere impostati con regole di autorizzazione diverse, ad esempio le autorizzazioni di invio messaggi, le autorizzazioni di ascolto notifica e così via. Per altre informazioni, vedere il documento [Sicurezza].

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.    Come si gestisce un payload sensibile nelle notifiche push?
Tutte le notifiche vengono recapitate ai dispositivi di destinazione dai servizi di notifica push (PNS) della piattaforma. Quando un mittente invia una notifica ad Hub di notifica di Azure, questa viene elaborata e passata al rispettivo PNS.

Tutte le connessioni tra mittente e Hub notifiche di Azure e PNS utilizzano HTTPS.

> [!NOTE]
> Gli Hub notifica di Azure non registrano il payload del messaggio in alcun modo.
> 
> 

Per l'invio di payload sensibili, tuttavia, si consiglia un modello di push sicuro in cui il mittente invia una notifica ping con un identificatore di messaggio al dispositivo senza il payload sensibile e, quando l'app sul dispositivo riceve il payload, è in grado di chiamare direttamente un'API sicura per recuperare i dettagli del messaggio. Nell' [esercitazione sul push sicuro in Hub di notifica] viene spiegato come implementare il modello descritto sopra.

## <a name="operations"></a>Operazioni
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.    Cos'è il ripristino di emergenza?
È fornita una copertura del ripristino di emergenza dei metadati (nome dell'hub di notifica, stringa di connessione e altre informazioni fondamentali) sul lato del servizio. Quando viene attivato uno scenario di ripristino di emergenza, i dati delle registrazioni sono l' **unico segmento** dell'infrastruttura di Hub di notifica che andrà perso. Sarà necessario implementare una soluzione per ripopolare questi dati nel nuovo hub dopo il ripristino.

* *Passaggio 1* : Creare un Hub di notifica secondario in un controller di dominio diverso. È possibile crearlo in tempo reale al momento dell'evento di ripristino di emergenza oppure crearlo in fase iniziale. La scelta effettuata non è molto importante perché il provisioning di Hub di notifica è un processo relativamente rapido che richiede pochi secondi. Se viene creato inizialmente, si eviterà il rischio che l'evento di ripristino di emergenza influisca sulle funzionalità di gestione, per cui si tratta dell'opzione consigliata.
* *Passaggio 2*: Completare l'hub di notifica secondario con le registrazioni dall'hub di notifica primario. Non è consigliabile tentare di mantenere le registrazioni in entrambi gli hub e provare a mantenerli sincronizzati in tempo reale man mano che giungono le registrazioni. Questo in genere si è dimostrato un approccio non ottimale data la tendenza intrinseca delle registrazioni a raggiungere la scadenza sul lato PNS. Gli Hub di notifica le eliminano alla ricezione del feedback PNS sulle registrazioni scadute o non valida.  

Si consiglia di utilizzare un backend dell'app con:

* Mantiene un set di registrazioni sul proprio lato per poter eseguire un inserimento in blocco nell'hub di notifica secondario in caso di ripristino di emergenza

**OR**

* Ottiene un regolare dump delle registrazioni dall'hub primario come backup e quindi esegue un inserimento di massa nell'Hub di notifica secondario

> [!NOTE]
> La funzionalità di esportazione/importazione di registrazioni disponibile nel livello Standard è descritta nel documento relativo all' [esportazione/importazione di registrazioni] .
> 
> 

Se non si ha un back-end, all'avvio dell'app in uno qualsiasi dei dispositivi di destinazione verrà eseguita una nuova registrazione nell'hub di notifica secondario e alla fine tutti i dispositivi attivi saranno registrati nell'hub di notifica secondario.

Lo svantaggio è che, per un certo periodo, i dispositivi in cui non sono state aperte le app non riceveranno le notifiche.

### <a name="2----is-there-any-audit-log-capability"></a>2.    È disponibile una funzionalità di log di controllo?
Tutte le operazioni di gestione degli hub di notifica passano ai log delle operazioni, esposti nel [portale di Azure classico].

## <a name="monitoring--troubleshooting"></a>Monitoraggio e risoluzione dei problemi
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.    Quali funzionalità di risoluzione dei problemi sono disponibili?
Gli hub di notifica di Azure forniscono numerose funzionalità per la risoluzione dei problemi comuni, in particolare nello scenario frequente delle notifiche eliminate. Vedere i dettagli nel white paper dedicato alla [risoluzione dei problemi di Hub di notifica] .

### <a name="2----what-telemetry-features-are-available"></a>2.    Sono disponibili le funzionalità di telemetria?
Hub di notifica di Azure consente la visualizzazione di dati di telemetria nel [portale di Azure classico]. Per informazioni dettagliate sulle metriche disponibili, vedere la pagina relativa alle [metriche di Hub di notifica] .

> [!NOTE]
> La riuscita delle notifiche indica solo che le notifiche push sono state recapitate al servizio di notifica push esterno, ad esempio servizio APN per Apple, GCM per Google e così via. È il PNS che dovrà recapitare la notifica ai dispositivi di destinazione. In genere, il PNS non espone le metriche di recapito a terze parti.  
> 
> 

È offerta anche la possibilità di esportare i dati di telemetria a livello di codice (nel livello **Standard** ). Per informazioni dettagliate, vedere l' [esempio di metriche di Hub di notifica] .

[portale di Azure classico]: https://manage.windowsazure.com
[Prezzi di Hub di notifica]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Caso studio - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API REST di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[esercitazione per app Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Registrazione dal back-end dell'app]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Gestione registrazione]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Sicurezza]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[esercitazione sul push sicuro in Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[risoluzione dei problemi di Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[metriche di Hub di notifica]: https://msdn.microsoft.com/library/dn458822.aspx
[esempio di metriche di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[esportazione/importazione di registrazioni]: https://msdn.microsoft.com/library/dn790624.aspx
[portale di Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Servizi mobili di Azure]: https://azure.microsoft.com/services/app-service/mobile/
[prezzi del servizio app]: https://azure.microsoft.com/pricing/details/app-service/

