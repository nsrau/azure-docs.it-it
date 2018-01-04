---
title: Hub di notifica di Azure - Domande frequenti (FAQ) | Documentazione Microsoft
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
ms.openlocfilehash: d19a1b7c8d50ef0fde3cf65c9fd469bc34a27adc
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notifiche push sicure con Hub di notifica di Azure - Domande frequenti
## <a name="general"></a>Generale
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Che cos'è la struttura di risorse di Hub di notifica?

Hub di notifica di Azure dispone di due livelli di risorse: hub e spazi dei nomi. Un hub è una singola risorsa push in grado di contenere le informazioni push multipiattaforma di un'app. Uno spazio dei nomi è una raccolta di hub in un'area.

Il mapping consigliato abbina uno spazio dei nomi con un'unica app. In uno spazio dei nomi è possibile configurare un hub di produzione che funzioni con l'app di produzione, un hub di test che funzioni con l'app di test e così via.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual è il modello di prezzo di Hub di notifica?
Per informazioni dettagliate e aggiornate sui prezzi, vedere la pagina [Prezzi di Hub di notifica]. Hub di notifica viene fatturato a livello di spazio dei nomi. (Per la definizione di uno spazio dei nomi, vedere "Che cos'è la struttura di risorse di Hub di notifica?") Hub di notifica offre tre livelli:

* **Gratuito**: questo livello è un buon punto di partenza per esplorare le funzionalità push. Non è consigliabile per le applicazioni di produzione. Si ottengono 500 dispositivi e 1 milione di push inclusi per ogni spazio dei nomi al mese, senza garanzia di Contratto di servizio.
* **Basic**: questo livello, come il livello Standard, è consigliato per piccole applicazioni di produzione. Si ottengono 200.000 dispositivi e 10 milioni di push inclusi per ogni spazio dei nomi al mese come baseline. Sono incluse opzioni di aumento di quota.
* **Standard**: livello consigliato per le applicazioni di produzione di medie o grandi dimensioni. Si ottengono 10 milioni di dispositivi e 10 milioni di push inclusi per ogni spazio dei nomi al mese come baseline. Sono incluse opzioni di aumento di quota e capacità di telemetria avanzata.

Funzionalità del livello Standard:
* **Telemetria avanzata**: è possibile usare Hub di notifica per la telemetria per messaggio per tenere traccia delle richieste push e i commenti di Platform Notification System per il debug.
* **Multi-tenancy**: è possibile lavorare con le credenziali di Platform Notification System a livello di spazio dei nomi. Questa opzione consente di suddividere facilmente i tenant in hub all'interno del medesimo spazio dei nomi.
* **Push pianificati**: è possibile le notifiche da inviare.

### <a name="what-is-the-notification-hubs-sla"></a>Qual è il contratto di servizio di Hub di notifica?
Per i livelli Basic e Standard di Hub di notifica, le applicazioni correttamente configurate possono inviare notifiche push o eseguire operazioni di gestione delle registrazioni almeno per il 99,9% del tempo. Per altre informazioni sul Contratto di servizio, visitare la pagina del [contratto di servizio per Hub di notifica](https://azure.microsoft.com/support/legal/sla/notification-hubs/).

> [!NOTE]
> Poiché le notifiche push dipendono da Platform Notification System di terze parti (come Apple APN e Google FCM), non sono presenti contratti di servizio per la consegna di tali messaggi. Dopo che Hub di notifica ha inviato i batch a Platform Notification System, come garantito dal contratto di servizio, è Platform Notification System a dover recapitare i push, senza garanzia del contratto di servizio.

### <a name="which-customers-are-using-notification-hubs"></a>Quali clienti utilizzano Hub di notifica?
Molti clienti usano Hub di notifica. Di seguito sono elencati alcuni dei più importanti:

* Sochi 2014: centinaia di gruppi di interesse, oltre 3 milioni di dispositivi e oltre 150 milioni di notifiche inviate nelle due settimane. [Case study: Sochi]
* Skanska: [Case study: Skanska]
* Seattle Times: [Case study: Seattle Times]
* Mural.ly: [Case study: Mural.ly]
* 7Digital: [Case study: 7Digital]
* Bing App: decine di milioni di dispositivi, invio di 3 milioni di notifiche al giorno.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Come eseguire l'aggiornamento o il downgrade di un hub o di uno spazio dei nomi a un livello diverso?
Passare al **[portale di Azure]** > **Spazi dei nomi di Hub di notifica** o **Hub di notifica**. Selezionare la risorsa che si desidera aggiornare, quindi passare al **piano tariffario**. Tenere presenti i requisiti seguenti:

* Il piano tariffario aggiornato si applica a *tutti* gli hub nello spazio dei nomi in uso.
* Se il conteggio dei dispositivi supera il limite del livello a cui si passa, è necessario eliminare i dispositivi prima effettuare il downgrade.


## <a name="design-and-development"></a>Progettazione e sviluppo
### <a name="which-server-side-platforms-do-you-support"></a>Quali piattaforme sul lato server sono supportate?
Gli SDK server sono disponibili per .NET, Java, Node.js, PHP e Python. Le API di Hub di notifica si basano su interfacce REST, pertanto è possibile lavorare direttamente con le API REST se si usano piattaforme diverse o se non si desidera una dipendenza aggiuntiva. Per altre informazioni, vedere la pagina sulle [API REST di Hub di notifica].

### <a name="which-client-platforms-do-you-support"></a>Quali piattaforme client sono supportate?
È supportato l'invio di notifiche push alle piattaforme [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Per altre informazioni, vedere la pagina [Introduzione ad Hub di notifica].

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Sono supportate le notifiche via SMS, messaggi di posta elettronica o Web?
Hub di notifica è progettato principalmente per l'invio di notifiche alle app per dispositivi mobili. Non offre funzionalità di posta elettronica o SMS. Esistono però piattaforme di terze parti che forniscono queste funzionalità e che possono essere integrate con Hub di notifica per inviare notifiche push native tramite [App per dispositivi mobili].

Hub di notifica non fornisce neanche un servizio predefinito per il recapito di notifiche push all'interno del browser. I clienti possono implementare questa funzionalità mediante SignalR su piattaforme sul lato server supportate. Se si vuole inviare notifiche alle app basate su browser nel sandbox Chrome, vedere l'[esercitazione per app Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Come sono correlati App per dispositivi mobili e Hub di notifica di Azure e quando si usano?
Se si ha già un back-end dell'app per dispositivi mobili e si vuole solo aggiungere la funzionalità di invio di notifiche push, è possibile usare Hub di notifica di Azure. Se si desidera configurare il back-end dell'app per dispositivi mobili da zero, è consigliabile usare la funzionalità App per dispositivi mobili di Servizio app di Azure. Un'app per dispositivi mobili esegue automaticamente il provisioning di un hub di notifica per consentire l'invio di notifiche push dal back-end dell'app per dispositivi mobili. I prezzi di App per dispositivi mobili includono le spese di base per un hub di notifica. Si paga solo quando si superano i push inclusi. Per altre informazioni sui costi, vedere la pagina [Servizio app Prezzi].

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quanti dispositivi sono supportati se si inviano notifiche push tramite Hub di notifica?
Per informazioni dettagliate sul numero di dispositivi supportati, vedere la pagina [Prezzi di Hub di notifica].

Se è necessario il supporto per più di 10 milioni di dispositivi registrati, [contattare direttamente il supporto di Azure](https://azure.microsoft.com/overview/contact-us/) per informazioni su come ridimensionare la soluzione.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quante notifiche push si può inviare?
A seconda del piano selezionato, Hub di notifica di Azure passa automaticamente a un piano superiore in base al numero di notifiche inviate nel sistema.

> [!NOTE]
> Il costo di utilizzo generale può aumentare in base al numero di notifiche push gestite. È importante conoscere i limiti dei piani descritti nella pagina [Prezzi di Hub di notifica].
> 
> 

I clienti usano Hub di notifica per inviare milioni di notifiche push ogni giorno. Non è necessario eseguire alcuna operazione particolare per ridimensionare la copertura delle notifiche push, purché si usi Hub di notifica di Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo occorre perché le notifiche push inviate raggiungano il dispositivo?
In uno scenario di utilizzo normale, dove il carico in ingresso è piuttosto coerente e uniforme, Hub di notifica di Azure è in grado di elaborare almeno *1 milione di invii di notifiche push al minuto*. Questa velocità può variare in base al numero di tag, alla natura delle trasmissioni in ingresso e ad altri fattori esterni.

Nell'intervallo di tempo stimato per il recapito, il servizio calcola le destinazioni per piattaforma e instrada i messaggi al Push Notification Service (PNS) in base ai tag/espressioni tag registrati. È responsabilità del PNS inviare le notifiche al dispositivo.

Il PNS non garantisce alcun Contratto di servizio per il recapito delle notifiche. Tuttavia, la maggior parte delle notifiche push vengono recapitate ai dispositivi di destinazione entro pochi minuti (in genere entro 10 minuti) dal momento in cui vengono inviati a Hub di notifica. Alcune notifiche potrebbero richiedere più tempo.

> [!NOTE]
> Hub di notifica di Azure include anche un criterio per l'eliminazione delle notifiche che non vengono recapitate al PNS entro 30 minuti. Questo ritardo può verificarsi per diversi motivi, più comunemente perché il PNS sta rallentando l'applicazione.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>La latenza è garantita?
Data la natura delle modifiche push, che vengono recapitate da un PNS esterno specifico di una piattaforma, non esiste alcuna garanzia di latenza. In genere, la maggior parte delle notifiche push viene recapitata entro pochi minuti.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Quali sono gli aspetti da considerare per la progettazione di una soluzione con spazi dei nomi e hub di notifica?
#### <a name="mobile-appenvironment"></a>Ambiente/app per dispositivi mobili
* Usare un hub di notifica per ogni app per dispositivi mobili per ogni ambiente.
* In uno scenario multi-tenant ogni tenant deve avere un hub separato.
* Non condividere mai lo stesso hub di notifica fra ambienti di produzione e test. Ciò potrebbe causare problemi durante l'invio delle notifiche. Apple offre endpoint Sandbox e Production Push, che hanno credenziali separate.
* Per impostazione predefinita, è possibile inviare notifiche ai dispositivi registrati tramite il portale di Azure o il componente integrato di Azure in Visual Studio. La soglia è impostata su 10 dispositivi, che vengono selezionati in modo casuale dal pool di registrazione.

> [!NOTE]
> Se l'hub è stato originariamente configurato con un certificato Sandbox Apple e successivamente è stato riconfigurato per usare un certificato di produzione Apple, i token del dispositivo originale non sono validi. I token non validi determinano la mancata riuscita del push. Separare l'ambiente di test da quello di produzione e usare hub diversi per ambienti diversi.
> 
> 

#### <a name="pns-credentials"></a>Credenziali PNS
Quando un'app per dispositivi mobili viene registrata con il portale per sviluppatori della piattaforma (ad esempio Apple o Google), vengono inviati un identificatore app e i token di sicurezza. Il back-end di app fornisce questi token al PNS della piattaforma, in modo che possano essere inviate notifiche push ai dispositivi. I token di sicurezza possono essere sotto forma di certificati (ad esempio Apple iOS o Windows Phone) o chiavi di sicurezza (ad esempio Google Android o Windows). Devono essere configurati in hub di notifica. La configurazione viene eseguita in genere a livello di hub di notifica, ma può essere eseguita anche a livello di spazio dei nomi in uno scenario multi-tenant.

#### <a name="namespaces"></a>Spazi dei nomi
Gli spazi dei nomi possono essere usati anche per il raggruppamento di distribuzione. Possono essere usati anche per rappresentare tutti gli hub di notifica per tutti i tenant della stessa app in uno scenario multi-tenant.

#### <a name="geo-distribution"></a>Distribuzione geografica
La distribuzione geografica non è sempre fondamentale negli scenari di notifiche push. Vari PNS (ad esempio APNS o GCM) che recapitano notifiche push ai dispositivi non sono distribuiti uniformemente.

Se si ha un'applicazione che viene usata a livello globale, è possibile creare hub in vari spazi dei nomi tramite il servizio Hub di notifica in diverse aree di Azure nel mondo.

> [!NOTE]
> Tuttavia non si consiglia di farlo, perché in questo modo aumenta il costo di gestione, in particolare per le registrazioni. Si consiglia di farlo solo se vi è l'esigenza esplicita.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Le registrazioni devono essere eseguite dal back-end dell'app o direttamente tramite i dispositivi client?
Le registrazioni dal back-end dell'app sono utili quando è necessario autenticare i client prima di creare la registrazione. Sono utili anche quando ci sono tag che devono essere creati o modificati dal back-end dell'app in base alla logica dell'applicazione. Per altre informazioni, vedere le pagine [Registrazione dal back-end dell'app] e [Guida alla registrazione del back-end 2].

### <a name="what-is-the-push-notification-delivery-security-model"></a>Qual è il modello di sicurezza del recapito di notifiche push?
Hub di notifica di Azure usa un modello di sicurezza basato sulla [firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md). È possibile usare i token di firma di accesso condiviso a livello di spazio dei nomi radice o a livello di hub di notifica granulare. I token di firma di accesso condiviso possono essere impostati in modo da seguire regole di autorizzazione diverse, ad esempio per inviare le autorizzazioni di messaggi o per ascoltare le autorizzazioni di notifica. Per altre informazioni, vedere il documento sul [modello di sicurezza di Hub di notifica].

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Come si gestisce un payload sensibile nelle notifiche push?
Tutte le notifiche vengono recapitate ai dispositivi di destinazione dal PNS della piattaforma. Quando una notifica viene inviata a Hub di notifica di Azure, viene elaborata e passata al rispettivo PNS.

Tutte le connessioni, dal mittente a Hub notifiche di Azure al PNS, usano HTTPS.

> [!NOTE]
> Hub notifica di Azure non registra il payload dei messaggi in alcun modo.
> 
> 

Per l'invio di payload sensibili è consigliabile usare un modello push sicuro. Il mittente invia una notifica ping con un identificatore di messaggio al dispositivo senza il payload sensibile. Quando l'app sul dispositivo riceve il payload, l'applicazione chiama un'API sicura direttamente per recuperare i dettagli del messaggio. Per informazioni di guida su come implementare questo modello, vedere la pagina sull'[esercitazione relativa al push sicuro di Hub di notifica].

## <a name="operations"></a>Operazioni
### <a name="what-support-is-provided-for-disaster-recovery"></a>Quale supporto è fornito per il ripristino di emergenza?
È fornita la copertura del ripristino di emergenza dei metadati (nome di Hub di notifica, stringa di connessione e altre informazioni fondamentali) sul lato del servizio. Quando viene attivato uno scenario di ripristino di emergenza, i dati delle registrazioni sono l'*unico segmento* dell'infrastruttura di Hub di notifica che andrà perso. Sarà necessario implementare una soluzione per ripopolare questi dati nel nuovo hub dopo il ripristino:

1. Creare un hub di notifica secondario in un controller di dominio diverso. È consigliabile crearne uno dall'inizio per proteggersi da un evento di ripristino di emergenza che potrebbe interferire con le capacità di gestione. È anche possibile crearne uno al momento dell'evento di ripristino di emergenza.

2. Completare l'hub di notifica secondario con le registrazioni dall'hub di notifica primario. Non è consigliabile tentare di mantenere le registrazioni in entrambi gli hub e mantenerle sincronizzate a partire dal loro ingresso. Questa prassi non funziona a causa della tendenza insita delle registrazioni a scadere sul lato PNS. Hub di notifica le elimina quando riceve il feedback PNS sulle registrazioni scadute o non valide.  

Per i back-end dell'app ci sono due raccomandazioni:

* Usare un back-end di app che mantenga un determinato set di registrazioni sul proprio lato. Esso potrà quindi eseguire un inserimento di massa nell'hub di notifica secondario.

* Usare un back-end di app che ottiene regolarmente un dump delle registrazioni dall'hub di notifica primario come backup. Esso potrà quindi eseguire un inserimento di massa nell'hub di notifica secondario.

> [!NOTE]
> La funzionalità di esportazione/importazione delle registrazioni disponibile nel livello Standard è descritta nel documento relativo all'[esportazione/importazione delle registrazioni].
> 
> 

Se non si ha un back-end, all'avvio dell'app nei dispositivi di destinazione viene eseguita una nuova registrazione nell'hub di notifica secondario. Alla fine l'hub di notifica secondario avrà tutti i dispositivi attivi registrati.

Vi sarà un periodo di tempo in cui i dispositivi con app non aperte non riceveranno le notifiche.

### <a name="is-there-audit-log-capability"></a>È disponibile una funzionalità di log di controllo?
Passare tutte le operazioni di gestione degli hub di notifica, che sono esposte nel log delle operazioni di [portale di Azure].

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi
### <a name="what-troubleshooting-capabilities-are-available"></a>Quali funzionalità di risoluzione dei problemi sono disponibili?
Hub di notifica di Azure fornisce numerose funzionalità per la risoluzione dei problemi, in particolare nello scenario frequente delle notifiche eliminate. Per informazioni dettagliate, vedere il white paper sulla [risoluzione dei problemi relativi a Hub di notifica].

### <a name="what-telemetry-features-are-available"></a>Sono disponibili le funzionalità di telemetria?
Visualizzazione dei dati di telemetria in Azure consente di hub di notifica di [portale di Azure]. Per informazioni dettagliate sulle metriche, vedere la pagina relativa alle [metriche di Hub di notifica].

> [!NOTE]
> La riuscita delle notifiche indica solo che le notifiche push sono state recapitate al PNS esterno, ad esempio servizio APNS per Apple, GCM per Google e così via. È responsabilità del PNS inviare le notifiche ai dispositivi di destinazione. In genere il PNS non espone le metriche di recapito a terze parti.  
> 
> 

È disponibile anche l'opzione di esportare i dati di telemetria a livello di codice (nel livello Standard). Per informazioni dettagliate, vedere l'[esempio sulle metriche di Hub di notifica].

[portale di Azure]: https://portal.azure.com
[Prezzi di Hub di notifica]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Case Study: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Case Study: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Case Study: Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Case Study: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Case Study: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API REST di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Introduzione ad Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[esercitazione per app Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Registrazione dal back-end dell'app]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Guida alla registrazione del back-end 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[modello di sicurezza di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[esercitazione relativa al push sicuro di Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[risoluzione dei problemi relativi a Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[metriche di Hub di notifica]: https://msdn.microsoft.com/library/dn458822.aspx
[esempio sulle metriche di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[esportazione/importazione delle registrazioni]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App per dispositivi mobili]: https://azure.microsoft.com/services/app-service/mobile/
[Servizio app Prezzi]: https://azure.microsoft.com/pricing/details/app-service/
