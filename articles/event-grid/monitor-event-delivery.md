---
title: Visualizzare le metriche di griglia di eventi di Azure e impostare gli avvisi
description: Questo articolo descrive come usare la portale di Azure per visualizzare le metriche per gli argomenti e le sottoscrizioni di griglia di eventi di Azure e creare avvisi su di essi.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86114884"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorare il recapito dei messaggio di Griglia di eventi 
Questo articolo descrive come usare il portale per visualizzare le metriche per gli argomenti e le sottoscrizioni di griglia di eventi e per creare avvisi su di essi. 

## <a name="metrics"></a>Metriche

Il portale visualizza le metriche per lo stato di recapito dei messaggi di evento.

Per gli argomenti, di seguito sono riportate alcune delle metriche:

* **Publish Succeeded** (Pubblicazione riuscita): l'evento è stato inviato all'argomento ed elaborato con una risposta 2xx.
* **Publish Failed** (Pubblicazione non riuscita): l'evento è stato inviato all'argomento, ma è stato rifiutato con un codice errore.
* **Unmatched** (Non abbinato): l'evento è stato pubblicato nell'argomento, ma non abbinato a una sottoscrizione di eventi. L'evento è stato eliminato.

Per le sottoscrizioni, di seguito sono riportate alcune delle metriche:

* **Delivery Succeeded** (Recapito riuscito): l'evento è stato recapitato all'endpoint della sottoscrizione ed è stato ricevuta una risposta 2xx.
* **Recapito non riuscito**: ogni volta che il servizio tenta di recapitare e il gestore eventi non restituisce un codice 2xx di esito positivo, viene incrementato il contatore **recapito non riuscito** . Se si tenta di recapitare lo stesso evento più volte e si verifica un errore, il contatore **recapito non riuscito** viene incrementato per ogni errore.
* **Expired Events** (Eventi scaduti): l'evento non è stato recapitato e sono inviati tutti i tentativi di ripetizione del recapito. L'evento è stato eliminato.
* **Matched Events** (Eventi abbinati): l'evento nell'argomento è stato abbinato dalla sottoscrizione di eventi.

    > [!NOTE]
    > Per l'elenco completo delle metriche, vedere [metriche supportate da griglia di eventi di Azure](metrics.md).

## <a name="view-custom-topic-metrics"></a>Visualizzare le metriche degli argomenti personalizzati

Se è stato pubblicato un argomento personalizzato, è possibile visualizzare le relative metriche. 

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella barra di ricerca in questo argomento, digitare **argomenti di griglia di eventi**, quindi selezionare **argomenti griglia di eventi** nell'elenco a discesa. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::
3. Selezionare l'argomento personalizzato dall'elenco di argomenti. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::
4. Visualizzare le metriche per l'argomento dell'evento personalizzato nella pagina dell' **argomento di griglia di eventi** . Nell'immagine seguente la sezione **Essentials** che mostra il gruppo di risorse, la sottoscrizione e così via è ridotta a icona. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::

È possibile creare grafici con metriche supportate usando la scheda **metriche** della pagina dell'argomento di **griglia di eventi** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::

Per altre informazioni sulle metriche, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md)

Vedere, ad esempio, il grafico delle metriche per la metrica **eventi pubblicati** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::


## <a name="view-subscription-metrics"></a>Visualizzare le metriche delle sottoscrizioni
1. Passare alla pagina dell' **argomento griglia di eventi** attenendosi alla procedura descritta nella sezione precedente. 
2. Selezionare la sottoscrizione nel riquadro inferiore, come illustrato nell'esempio seguente. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::    

    È anche possibile cercare le **sottoscrizioni di griglia di eventi** nella barra di ricerca del portale di Azure, selezionare **tipo di argomento**, **sottoscrizione**e **località** per visualizzare una sottoscrizione di eventi. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::        

    Per gli argomenti personalizzati, selezionare **argomenti griglia di eventi** come **tipo di argomento**. Per gli argomenti di sistema, selezionare il tipo di risorsa di Azure, ad esempio **account di archiviazione (BLOB, GPv2)**. 
3. Vedere le metriche per la sottoscrizione nel home page per la sottoscrizione in un grafico. È possibile visualizzare **le metriche generali**, **errori**, **latenza**e messaggi non **recapitabili** per le ultime 1 ora, 6 ore, 12 ore, 1 giorno, 7 giorni o 30 giorni. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::    

## <a name="view-system-topic-metrics"></a>Visualizza le metriche degli argomenti di sistema

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella barra di ricerca in questo argomento, digitare **argomenti di sistema griglia di eventi**, quindi selezionare **argomenti del sistema di griglia di eventi** nell'elenco a discesa. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::
3. Selezionare l'argomento di sistema dall'elenco di argomenti. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::
4. Visualizzare le metriche per l'argomento di sistema nella pagina di **argomento del sistema di griglia di eventi** . Nell'immagine seguente la sezione **Essentials** che mostra il gruppo di risorse, la sottoscrizione e così via è ridotta a icona. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::

È possibile creare grafici con metriche supportate usando la scheda **metriche** della pagina dell'argomento di **griglia di eventi** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::

Per altre informazioni sulle metriche, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per informazioni su come creare avvisi per le metriche e le operazioni del log attività, vedere [impostare gli avvisi](set-alerts.md).
- Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
