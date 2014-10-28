<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Come monitorare le entità di messaggistica del bus di servizio

In questo argomento vengono descritti la gestione e il monitoraggio delle entità del bus di servizio mediante l'uso del [portale di gestione di Azure][portale di gestione di Azure]. Grazie al portale, è possibile ottenere una vista completa dello stato delle query e degli argomenti. È anche possibile monitorarne l'uso.

## Come monitorare l'attività delle code del bus di servizio

Per monitorare una coda del bus di servizio, eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **Service Bus** nella barra di spostamento a sinistra per ottenere l'elenco di spazi dei nomi servizio.
3.  Fare clic sullo spazio dei nomi che contiene la coda che si desidera monitorare.
4.  Sulla barra pivot nella parte superiore della pagina fare clic su **Queues**.
5.  Fare clic sul nome della coda che si desidera monitorare. Viene visualizzato il dashboard della coda.
6.  È possibile vedere l'attività per le code create. È possibile visualizzare queste informazioni in base a diversi intervalli di tempo. L'impostazione predefinita è 1 ora ma è possibile fare clic sul menu a discesa accanto all'ora per scegliere un intervallo di tempo diverso: le ultime 24 ore, 7 giorni o gli ultimi 30 giorni. È possibile visualizzare i dati con un livello di precisione corrispondente a punti di misurazione anche di soli 5 minuti per l'intervallo di un'ora, 1 ora per l'intervallo di 24 ore e 1 giorno per gli intervalli di 7 e 30 giorni.

Per qualsiasi coda è possibile visualizzare i grafici relativi a:

-   **Messaggi in arrivo**: numero di messaggi in coda durante l'intervallo di tempo.
-   **Messaggi in uscita**: numero di messaggi rimossi dalla coda durante l'intervallo di tempo.
-   **Lunghezza**: numero di messaggi nell'entità alla fine di questo intervallo di tempo.
-   **Dimensione**: lo spazio di archiviazione (in MB) utilizzato dall'entità alla fine dell'intervallo di tempo.

### Quick Glance

**Quick Glance** nel dashboard riflette la dimensione attuale della coda come **Queue Length**. Vengono inoltre visualizzate altra proprietà della coda o dell'argomento. Queste informazioni vengono aggiornate ogni 10 secondi.

![][]

## Come monitorare l'attività degli argomenti del bus di servizio

Per monitorare un argomento del bus di servizio, eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **Service Bus** nella barra di spostamento a sinistra per ottenere l'elenco di spazi dei nomi servizio.
3.  Fare clic sullo spazio dei nomi che contiene l'argomento che si desidera monitorare.
4.  Sulla barra pivot nella parte superiore della pagina fare clic su **Topics**.
5.  Fare clic sul nome dell'argomento che si desidera monitorare. Viene visualizzato il dashboard dell'argomento.

Un dashboard dell'argomento è simile a un dashboard della coda, ad eccezione delle metriche di utilizzo. I messaggi in uscita e le relative lunghezze non sono presenti nel dashboard degli argomenti, poiché tali informazioni sarebbero diverse per ognuna delle sottoscrizioni per un argomento. La scheda **Monitor** consente di aggiungere metriche di utilizzo (numero di messaggi in uscita e relative lunghezze) per ogni sottoscrizione a un argomento. Per aggiungere tali metriche, fare clic sul pulsante **Monitor**. Fare quindi clic su **Add Metrics** nella parte inferiore della pagina, quindi scegliere tra le sottoscrizioni presenti nell'argomento.

![][1]

  [portale di gestione di Azure]: http://manage.windowsazure.com
  []: ./media/service-bus-monitor-message-entities/QueueDashboard.png
  [1]: ./media/service-bus-monitor-message-entities/AddMetrics.png
