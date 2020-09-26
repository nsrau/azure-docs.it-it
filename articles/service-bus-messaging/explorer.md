---
title: Usare Azure Service Bus Explorer per eseguire operazioni sui dati nel bus di servizio (anteprima)
description: Questo articolo fornisce informazioni su come usare Azure Service Bus Explorer basato sul portale per accedere ai dati del bus di servizio di Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: e5e97c6860c2cc01048f4f7caf9f40f9e07592d0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295601"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Usare Service Bus Explorer per eseguire operazioni sui dati nel bus di servizio (anteprima)

## <a name="overview"></a>Panoramica

Il bus di servizio di Azure consente alle applicazioni client mittente e ricevente di separare la logica di business tramite la familiare semantica point-to-point (coda) e di pubblicazione-sottoscrizione (argomento-sottoscrizione).

Le operazioni eseguite su uno spazio dei nomi del bus di servizio di Azure sono di due tipi 
   * Operazioni di gestione: creazione, aggiornamento ed eliminazione di spazio dei nomi, code, argomenti e sottoscrizioni del bus di servizio.
   * Operazioni sui dati: invio e ricezione di messaggi da code, argomenti e sottoscrizioni.

Azure Service Bus Explorer espande le funzionalità del portale oltre le operazioni di gestione per supportare le operazioni sui dati (invio, ricezione, anteprima) su code, argomenti e sottoscrizioni (e le relative sottoentità per i messaggi non recapitabili), direttamente dal portale di Azure.

> [!NOTE]
> Questo articolo evidenzia le funzionalità di Azure Service Bus Explorer disponibili nel portale di Azure.
>
> Lo strumento Azure Service Bus Explorer ***non*** è lo strumento OSS [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) di proprietà della community.
>

## <a name="prerequisites"></a>Prerequisiti

Per usare lo strumento Service Bus Explorer, sarà necessario effettuare il provisioning di uno spazio dei nomi del bus di servizio di Azure. 

Una volta eseguito il provisioning dello spazio dei nomi del bus di servizio, è necessario creare una coda per l'invio e la ricezione di un messaggio da o a un argomento con una sottoscrizione per testare la funzionalità.

Per altre informazioni su come creare code, argomenti e sottoscrizioni, fare riferimento ai collegamenti seguenti.
   * [Avvio rapido - Creare code](service-bus-quickstart-portal.md)
   * [Avvio rapido - Creare argomenti](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>Uso di Service Bus Explorer

Per usare Azure Service Bus Explorer, è necessario passare allo spazio dei nomi del bus di servizio in cui eseguire le operazioni di invio, anteprima e ricezione.

Per eseguire operazioni su una coda, scegliere **Code** dal menu di spostamento. Per eseguire operazioni su un argomento (e le relative sottoscrizioni), scegliere **Argomenti**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Selezione dell'entità":::

Dopo aver selezionato **Code** o **Argomenti**, selezionare la coda o l'argomento specifico.

Selezionare **Service Bus Explorer (anteprima)** dal menu di spostamento a sinistra

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu di spostamento a sinistra di Service Bus Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Invio di un messaggio a una coda o a un argomento

Per inviare un messaggio a una **coda** o a un **argomento**, fare clic sulla scheda ***Invia*** in Service Bus Explorer.

Per comporre un messaggio: 

1. Selezionare il **tipo di contenuto**: "text/plain", "application/xml" o "application/json".
2. Aggiungere il **contenuto** del messaggio. Assicurarsi che corrisponda al **tipo di contenuto** impostato in precedenza.
3. Impostare le **proprietà avanzate** (facoltativo), tra cui ID correlazione, ID messaggio, etichetta, ReplyTo, durata (TTL) e ora di accodamento pianificata (per i messaggi pianificati).
4. Impostare le **proprietà personalizzate**: può trattarsi di qualsiasi proprietà utente impostata su una chiave del dizionario.

Dopo avere composto il messaggio, fare clic su Invia.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Composizione del messaggio":::

Quando l'operazione di invio viene completata correttamente: 

* Se è stato eseguito l'invio alla coda, verrà incrementato il contatore della metrica **Messaggi attivi**.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Se è stato eseguito l'invio all'argomento, verrà incrementato il contatore della metrica **Messaggi attivi** per la sottoscrizione a cui è stato indirizzato il messaggio.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Ricezione di un messaggio da una coda

La funzione di ricezione in Service Bus Explorer consente di ricevere un singolo messaggio alla volta. L'operazione di ricezione viene eseguita usando la modalità **ReceiveAndDelete**.

> [!IMPORTANT]
> Si noti che l'operazione di ricezione eseguita da Service Bus Explorer è una ***ricezione distruttiva***, ovvero il messaggio viene rimosso dalla coda quando è visualizzato nello strumento Service Bus Explorer.
>
> Per esplorare i messaggi senza rimuoverli dalla coda, valutare se usare la funzionalità ***Anteprima***.
>

Per ricevere un messaggio da una coda (o dalla relativa coda secondaria dei messaggi non recapitabili) 

1. Fare clic sulla scheda ***Ricevi*** in Service Bus Explorer.
2. Controllare le metriche per verificare se sono presenti **messaggi attivi** o **messaggi non recapitabili** per la ricezione.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Scegliere tra ***Coda*** o la coda secondaria ***Messaggi non recapitabili***.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Fare clic sul pulsante ***Ricevi***, quindi su ***Sì*** per confermare l'operazione di ricezione ed eliminazione.


Quando l'operazione di ricezione ha esito positivo, i dettagli del messaggio vengono visualizzati nella griglia come illustrato di seguito. È possibile selezionare il messaggio dalla griglia per visualizzarne i dettagli.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Screenshot della finestra code in Esplora bus di servizio di Azure con i dettagli del messaggio visualizzati per un messaggio attivo selezionato nella coda.":::


### <a name="peeking-a-message-from-a-queue"></a>Anteprima di un messaggio da una coda

Con la funzionalità Anteprima, è possibile usare Service Bus Explorer per visualizzare i primi 32 messaggi in una coda o nella coda dei messaggi non recapitabili.

1. Per visualizzare l'anteprima del messaggio in una coda, fare clic sulla scheda ***Anteprima*** in Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Controllare le metriche per verificare se sono presenti **messaggi attivi** o **messaggi non recapitabili** per l'anteprima.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Scegliere tra ***Coda*** o la coda secondaria ***Messaggi non recapitabili***.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Fare clic sul pulsante ***Anteprima***. 

Al termine dell'operazione di anteprima, nella griglia vengono visualizzati fino a 32 messaggi, come illustrato di seguito. Per visualizzare i dettagli di un determinato messaggio, selezionarlo nella griglia. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Poiché l'anteprima non è un'operazione distruttiva, il messaggio **non** verrà rimosso dalla coda.
>

### <a name="receiving-a-message-from-a-subscription"></a>Ricezione di un messaggio da una sottoscrizione

Come nel caso di una coda, è possibile eseguire l'operazione di ***ricezione*** per una sottoscrizione o la relativa entità per i messaggi non recapitabili. Tuttavia, poiché una sottoscrizione è intrinseca al contesto dell'argomento, l'operazione di ricezione viene eseguita passando a Service Bus Explorer per un argomento specifico.

> [!IMPORTANT]
> Si noti che l'operazione di ricezione eseguita da Service Bus Explorer è una ***ricezione distruttiva***, ovvero il messaggio viene rimosso dalla coda quando è visualizzato nello strumento Service Bus Explorer.
>
> Per esplorare i messaggi senza rimuoverli dalla coda, valutare se usare la funzionalità ***Anteprima***.
>

1. Fare clic sulla scheda ***Ricevi*** e selezionare la specifica ***sottoscrizione*** dal selettore a discesa.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Scegliere tra ***Sottoscrizione*** o la sottoentità ***Messaggi non recapitabili***.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Fare clic sul pulsante ***Ricevi***, quindi su ***Sì*** per confermare l'operazione di ricezione ed eliminazione.

Quando l'operazione di ricezione ha esito positivo, il messaggio ricevuto verrà visualizzato nella griglia come illustrato di seguito. Per visualizzare i dettagli del messaggio, fare clic sul messaggio.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Screenshot della scheda ricezione in Esplora bus di servizio di Azure con i dettagli del messaggio visualizzati per un messaggio attivo che è stato ricevuto.":::

### <a name="peeking-a-message-from-a-subscription"></a>Anteprima di un messaggio da una sottoscrizione

Per esplorare semplicemente i messaggi in una sottoscrizione o nella relativa sottoentità per i messaggi non recapitabili, è anche possibile usare la funzionalità ***Anteprima*** nella sottoscrizione.

1. Fare clic sulla scheda ***Anteprima*** e selezionare la specifica ***sottoscrizione*** dal selettore a discesa.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Scegliere tra ***Sottoscrizione*** o la sottoentità ***Messaggi non recapitabili***.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Fare clic sul pulsante ***Anteprima***.

Al termine dell'operazione di anteprima, nella griglia vengono visualizzati fino a 32 messaggi, come illustrato di seguito. Per visualizzare i dettagli di un determinato messaggio, selezionarlo nella griglia. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Poiché l'anteprima non è un'operazione distruttiva, il messaggio **non** verrà rimosso dalla coda.
>

## <a name="next-steps"></a>Passaggi successivi

   * Altre informazioni sulle [code](service-bus-queues-topics-subscriptions.md#queues) e gli [argomenti](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) del bus di servizio
   * Altre informazioni sulla creazione di [code del bus di servizio tramite il portale di Azure](service-bus-quickstart-portal.md)
   * Altre informazioni sulla creazione di [argomenti e sottoscrizioni del bus di servizio tramite il portale di Azure](service-bus-quickstart-topics-subscriptions-portal.md)