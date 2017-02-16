---
title: Panoramica della messaggistica del bus di servizio | Documentazione di Microsoft
description: 'Messaggistica del bus di servizio: recapito flessibile dei dati nel cloud'
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 9ace119de3676bcda45d524961ebea27ab093415
ms.openlocfilehash: d48b460e1e1c12eb2df21e7feab149df9890374a


---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Messaggistica del bus di servizio: recapito flessibile dei dati nel cloud
Il bus di servizio di Microsoft Azure è un servizio di recapito di informazioni affidabile. Lo scopo di questo servizio è semplificare la comunicazione. Quando due o più parti desiderano scambiarsi informazioni, è necessario un meccanismo di comunicazione. Il bus di servizio è un meccanismo di comunicazione negoziato o di terze parti. È simile a un servizio postale nel mondo fisico. I servizi postali semplificano l'invio di diversi tipi di lettere e pacchetti con una serie di garanzie di recapito, in qualsiasi parte del mondo.

Analogamente al servizio postale di recapito della corrispondenza, il bus di servizio offre il recapito flessibile delle informazioni sia per il mittente che per il destinatario. Il servizio di messaggistica garantisce che le informazioni vengano recapitate anche se le due parti non sono mai in linea contemporaneamente o se non sono disponibili nello stesso momento. In questo modo, la messaggistica è simile all'invio di una lettera, mentre la comunicazione non negoziata è simile a una chiamata telefonica del passato, prima dell'introduzione dell'attesa di chiamata e dell'ID chiamate, che la rendono simile alla messaggistica negoziata.

Il mittente del messaggio può richiedere anche un'ampia gamma di caratteristiche di recapito tra cui transazioni, rilevamento dei duplicati, scadenza basata su tempo e invio in batch. Anche questi modelli presentano analogie con il servizio postale: consegna ripetuta, firma obbligatoria, modifica dell'indirizzo o richiamo.

Il bus di servizio supporta due modelli di messaggistica distinti: *inoltro* e *messaggistica negoziata*.

## <a name="service-bus-relay"></a>Inoltro del bus di servizio
Il componente di [inoltro WCF](../service-bus-relay/service-bus-relay-overview.md) del bus di servizio è un servizio centralizzato, ma con carico altamente bilanciato, che supporta un'ampia gamma di protocolli di trasporto e standard dei servizi Web. tra cui SOAP, WS-* e anche REST. Il [servizio di inoltro](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) offre una varietà di opzioni di connettività di inoltro e può facilitare la negoziazione di connessioni peer-to-peer dirette quando possibile. Bus di servizio è ottimizzato per gli sviluppatori .NET che utilizzano Windows Communication Foundation (WCF), in termini di prestazioni e usabilità e fornisce accesso completo al servizio di inoltro mediante interfacce SOAP e REST. In questo modo è possibile l’integrazione con il Bus di servizio per qualsiasi SOAP o ambiente di programmazione REST.

Il servizio di inoltro supporta la messaggistica unidirezionale tradizionale, la messaggistica richiesta-risposta e la messaggistica peer-to-peer. Supporta inoltre la distribuzione degli eventi nell'ambito di Internet per abilitare scenari di pubblicazione/sottoscrizione e le comunicazioni tramite socket bidirezionale per migliorare l'efficienza point-to-point. Nel modello di messaggistica inoltrata, un servizio locale si connette al servizio di inoltro attraverso una porta in uscita e crea un socket bidirezionale per la comunicazione associato a un determinato indirizzo rendezvous. Il client può quindi comunicare con il servizio locale inviando messaggi al servizio di inoltro come destinazione l'indirizzo rendezvous. Il servizio di inoltro eseguirà quindi l’"inoltro" dei messaggi al servizio locale attraverso il socket bidirezionale già in uso. Il client non necessita di una connessione diretta al servizio locale, non deve conoscere la posizione in cui risiede il servizio e il servizio locale non richiede porte in ingresso aperte sul firewall.

È possibile avviare la connessione tra il servizio locale e il servizio di inoltro mediante una suite di associazioni di "inoltro" WCF. Dietro le quinte, eseguire il mapping delle associazioni di inoltro ai nuovi elementi di associazione di trasporto progettati per creare i componenti di canale WCF che si integrano con il bus di servizio nel cloud.

L'inoltro WCF del bus di servizio offre numerosi vantaggi, ma richiede che il server e il client siano online contemporaneamente per inviare e ricevere messaggi. Questo non è ottimale per la comunicazione di tipo HTTP, in cui le richieste potrebbero essere in genere a breve tempo, né per i client che si connettono solo occasionalmente, come browser, applicazioni per dispositivi mobili e così via. La messaggistica negoziata supporta la comunicazione disaccoppiata e presenta dei vantaggi; i client e server possono connettersi quando necessario ed eseguire le operazioni in modo asincrono.

## <a name="brokered-messaging"></a>Messaggistica negoziata
A differenza dello schema dell'inoltro, la [messaggistica negoziata](service-bus-queues-topics-subscriptions.md) può essere considerata asincrona o disaccoppiata dal punto di vista temporale. Produttori (mittenti) e utenti (ricevitori) non devono essere necessariamente online contemporaneamente. L'infrastruttura di messaggistica archivia in modo affidabile i messaggi in un "broker" (ad esempio una coda) fino a quando l’utente è pronto a riceverli. Questo consente la disconnessione volontaria (ad esempio, per attività di manutenzione) o involontaria (a seguito di un guasto) dei componenti dell'applicazione distribuita senza ripercussioni sull'intero sistema. Inoltre, l'applicazione ricevente deve solo essere in linea durante determinate ore del giorno, ad esempio un sistema di gestione inventario che deve essere eseguito alla fine della giornata lavorativa.

I componenti principali dell'infrastruttura di messaggistica negoziata del bus di servizio sono code, argomenti e sottoscrizioni.  La differenza principale è che gli argomenti supportano funzionalità di pubblicazione/sottoscrizione che possono essere usate per la logica di instradamento e recapito sofisticata basata sul contenuto, incluso l'invio a più destinatari. Questi componenti consentono nuovi scenari di messaggistica asincrona, ad esempio disaccoppiamento temporale, pubblicazione/sottoscrizione e bilanciamento del carico. Per altre informazioni sulle entità di messaggistica, vedere [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md).

Come per l'infrastruttura di inoltro WCF, la funzionalità di messaggistica negoziata viene resa disponibile per i programmatori di .NET Framework e WCF e anche tramite REST.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)




<!--HONumber=Nov16_HO3-->


