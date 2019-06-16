---
title: Aggiungere un'origine evento dell'hub eventi in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come aggiungere un'origine evento connessa a un Hub eventi di Azure all'ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8b39001481764eb955ab4535e8c6ea1752e0c012
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475720"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Aggiungere un'origine evento di un hub eventi all'ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati da Hub eventi di Azure.

> [!NOTE]
> I passaggi descritti in questo articolo si applicano sia per gli ambienti di versione di anteprima di tempo Series Insights e il tempo Series Insights a livello generale.

## <a name="prerequisites"></a>Prerequisiti

- Creare un ambiente Time Series Insights, come descritto in [creare un ambiente Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Creare un hub eventi. Visualizzare [creare uno spazio dei nomi di hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
- L'hub eventi deve ricevere eventi messaggio attivi. Informazioni su come [inviare eventi a hub eventi di Azure usando .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Creare un gruppo di consumer dedicato nell'hub eventi da usare nell'ambiente Time Series Insights. Tutte le origini evento di Time Series Insights devono avere un proprio gruppo di consumer dedicato non condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori riscontrino errori. È previsto un limite di 20 gruppi di consumer per hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Aggiungere un gruppo di consumer all'hub eventi

Le applicazioni usano gruppi di consumer per estrarre i dati da Hub eventi di Azure. Per leggere in modo affidabile i dati dall'hub eventi, specificare un gruppo di consumer dedicato che viene usato solo per questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer nell'hub eventi:

1. Nel portale di Azure individuare e aprire l'hub eventi.

1. In **Entità** selezionare **Gruppi di consumer** e quindi selezionare **Gruppo di consumer**.

   [![Hub eventi - aggiungere un gruppo di consumer](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. Nella pagina **Gruppi di consumer** immettere un nuovo valore univoco per **Nome**.  Usare questo nome al momento della creazione di una nuova origine evento nell'ambiente Time Series Insights.

1. Selezionare **Create**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente Time Series Insights esistente. Nel menu a sinistra selezionare **Tutte le risorse** e quindi selezionare l'ambiente Time Series Insights.

1. In **Topologia dell'ambiente** selezionare **Origini eventi** e fare clic su **Aggiungi**.

   [![In origini eventi, selezionare il pulsante Aggiungi](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Immettere un valore nel campo **Nome dell'origine evento** univoco per l'ambiente Time Series Insights, ad esempio **event-stream**.

1. In **Origine** selezionare **Hub eventi**.

1. Selezionare i valori appropriati in **Opzione di importazione**:
   - Se in una delle sottoscrizioni è presente un hub eventi esistente, selezionare **Usare un hub eventi dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.

       [![Nel riquadro dell'origine evento nuovo, immettere i valori per i primi tre parametri](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)


       [![Dettagli dell'hub eventi e sottoscrizione](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     La tabella seguente descrive le proprietà obbligatorie per l'opzione **Usare un hub eventi dalle sottoscrizione disponibili**:

     | Proprietà | Descrizione |
     | --- | --- |
     | ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub eventi è stato creato.
     | Spazio dei nomi del bus di servizio | Selezionare lo spazio dei nomi del bus di servizio di Azure che contiene l'hub eventi.
     | Nome hub eventi | Selezionare il nome dell'hub eventi.
     | Nome criteri hub eventi | Selezionare i criteri di accesso condiviso. È possibile creare criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
     | Chiave criteri hub eventi | Il valore della chiave può essere prepopolato.
     | Gruppo di consumer dell'hub eventi | Gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento. |
     | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi di evento devono essere nel formato o i dati non possono essere letti. |
     | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviati all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

    - Se l'hub eventi è esterno alle sottoscrizioni o se si vogliono scegliere opzioni avanzate, selezionare **Specificare le impostazioni dell'hub eventi manualmente**.

      La tabella seguente descrive le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub eventi manualmente**:
 
      | Proprietà | Descrizione |
      | --- | --- |
      | ID sottoscrizione | La sottoscrizione in cui l'hub eventi è stato creato.
      | Gruppo di risorse | Gruppo di risorse in cui è stato creato questo hub eventi.
      | Spazio dei nomi del bus di servizio | Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio.
      | Nome hub eventi | Nome dell'hub eventi. Al momento della creazione, all'hub eventi è stato anche assegnato un nome specifico.
      | Nome criteri hub eventi | Criteri di accesso condiviso. È possibile creare criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
      | Chiave criteri hub eventi | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria.
      | Gruppo di consumer dell'hub eventi | Gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento.
      | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi di evento devono essere nel formato o i dati non possono essere letti. |
      | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviati all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer di Time Series Insights dedicato aggiunto all'hub eventi.

1. Selezionare **Create**.

   [![Selezionare Crea](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Dopo la creazione dell'origine evento, Time Series Insights inizia automaticamente a trasmettere i dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
