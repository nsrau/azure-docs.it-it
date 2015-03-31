<properties
	pageTitle="Panoramica della messagistica del bus di servizio - Azure"
	description="Messaggistica bus di servizio: Recapito dei dati flessibile nel cloud"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor="mattshel"/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>


# Messaggistica bus di servizio: Recapito dei dati flessibile nel cloud

La messaggistica del bus di servizio di Microsoft Azure è un servizio di recapito di informazioni affidabile. Lo scopo di questo servizio è semplificare la comunicazione. Quando due o più parti desiderano scambiarsi informazioni, è necessario un meccanismo di comunicazione. La messaggistica del bus di servizio è un meccanismo di comunicazione negoziata o di terze parti. È simile a un servizio postale nel mondo fisico. I servizi postali semplificano l'invio di diversi tipi di lettere e pacchetti con una serie di garanzie di recapito, in qualsiasi parte del mondo.

La messaggistica del bus di servizio di Azure Service, analogamente al servizio postale di recapito lettere, fornisce flessibilità di recapito delle informazioni sia per il mittente sia per il destinatario. Il servizio di messaggistica garantisce che le informazioni vengano recapitate anche se le due parti non sono mai in linea contemporaneamente o se non sono disponibili nello stesso momento. In questo modo, la messaggistica è simile all'invio di una lettera, mentre la comunicazione non negoziata è simile a una chiamata telefonica del passato, prima dell'introduzione dell'attesa di chiamata e dell'ID chiamate, che la rendono simile alla messaggistica negoziata.

Il mittente del messaggio può richiedere anche un'ampia gamma di caratteristiche di recapito tra cui transazioni, rilevamento dei duplicati, scadenza basata su tempo e invio in batch. Anche questi modelli presentano analogie con il servizio postale: consegna ripetuta, firma obbligatoria, modifica dell'indirizzo o richiamo.

La messaggistica del bus di servizio presenta due distinte funzionalità: code e argomenti. Entrambe le entità di messaggistica supportano tutti i concetti descritti in precedenza ed altri. La differenza principale è che gli argomenti supportano funzionalità di pubblicazione/sottoscrizione, utilizzabili per la logica di instradamento e recapito sofisticata basata sul contenuto, tra cui l'invio a più destinatari.

## Passaggi successivi

Per ulteriori informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

- [Panoramica dell'architettura del bus di servizio di Azure](../fundamentals-service-bus-hybrid-solutions/)

- [Come usare le code del bus di servizio](../service-bus-dotnet-how-to-use-queues)

- [Come usare gli argomenti del bus di servizio](../service-bus-dotnet-how-to-use-topics-subscriptions)

<!--HONumber=47-->
