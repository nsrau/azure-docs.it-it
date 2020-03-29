---
title: Aggiungere un'origine eventi Hub eventi - Azure Time Series Insights . Documenti Microsoft
description: Informazioni su come aggiungere un'origine evento di Hub eventi di Azure all'ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905402"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Aggiungere un'origine evento di un hub eventi all'ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati da Hub eventi di Azure.

> [!NOTE]
> I passaggi descritti in questo articolo si applicano sia agli ambienti Time Series Insights GA che Time Series Insights Preview.

## <a name="prerequisites"></a>Prerequisiti

- Creare un ambiente Time Series Insights come descritto in [Creare un ambiente Azure Time Series Insights.](./time-series-insights-update-create-environment.md)
- Creare un hub eventi. Leggere [Creare uno spazio dei nomi hub eventi e un hub eventi tramite il portale](../event-hubs/event-hubs-create.md)di Azure.
- L'hub eventi deve ricevere eventi messaggio attivi. Informazioni su come inviare eventi agli hub eventi di [Azure usando .NET Framework.](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)
- Creare un gruppo di consumer dedicato nell'hub eventi da usare nell'ambiente Time Series Insights. Tutte le origini evento di Time Series Insights devono avere un proprio gruppo di consumer dedicato non condiviso con altri consumer. Se più lettori utilizzano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori presentino errori. È previsto un limite di 20 gruppi di consumer per hub eventi. Per informazioni dettagliate, leggere la guida alla [programmazione di Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Aggiungere un gruppo di consumer all'hub eventi

Le applicazioni usano gruppi di consumer per estrarre i dati da Hub eventi di Azure. Per leggere in modo affidabile i dati dall'hub eventi, fornire un gruppo di consumer dedicato che viene usato solo da questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer nell'hub eventi:

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'istanza dell'hub eventi dal riquadro **Panoramica** dello spazio dei nomi dell'hub eventi. Selezionare **Entità > Hub eventi** o trovare l'istanza in **Nome**.

    [![Aprire lo spazio dei nomi dell'hub eventiOpen your event hub namespace](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Nell'istanza dell'hub eventi selezionare **Entità > gruppi consumer**. Quindi, selezionare **gruppo di** consumer per aggiungere un nuovo gruppo di consumer. 

   [![Hub eventi - Aggiungere un gruppo di consumerEvent hub - Add a consumer group](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   In caso contrario, selezionare un gruppo di consumer esistente e passare alla sezione successiva.

1. Nella pagina **Gruppi di consumer** immettere un nuovo valore univoco per **Nome**.  Usare questo nome al momento della creazione di una nuova origine evento nell'ambiente Time Series Insights.

1. Selezionare **Crea**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Individuare l'ambiente Time Series Insights esistente. Nel menu a sinistra selezionare **Tutte le risorse** e quindi selezionare l'ambiente Time Series Insights.

1. Selezionare **Origini eventi**, quindi **Aggiungi**.

   [![In Origini eventi selezionare il pulsante Aggiungi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Immettere un valore per **Nome origine evento** univoco per `Contoso-TSI-GA-Event-Hub-ES`questo ambiente Time Series Insights, ad esempio .

1. In **Origine** selezionare **Hub eventi**.

1. Selezionare i valori appropriati in **Opzione di importazione**:

   * Se in una delle sottoscrizioni è presente un hub eventi esistente, selezionare **Usare un hub eventi dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.

     [![Selezionare un'opzione di importazione Origine evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  La tabella seguente descrive le proprietà obbligatorie per l'opzione **Usare un hub eventi dalle sottoscrizione disponibili**:

       [![Dettagli dell'hub di sottoscrizioni e eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Proprietà | Descrizione |
       | --- | --- |
       | Subscription | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi desiderata. |
       | Nome dell'hub eventi | Nome dell'istanza dell'hub eventi desiderata. |
       | Valore dei criteri dell'hub eventi | Selezionare il criterio di accesso condiviso desiderato. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Precompilato dal valore del criterio Hub eventi selezionato. |

    * Se l'hub eventi è esterno alle sottoscrizioni o se si vogliono scegliere opzioni avanzate, selezionare **Specificare le impostazioni dell'hub eventi manualmente**.

       La tabella seguente descrive le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub eventi manualmente**:
 
       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Resource group | Gruppo di risorse a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi desiderata. |
       | Nome dell'hub eventi | Nome dell'istanza dell'hub eventi desiderata. |
       | Valore dei criteri dell'hub eventi | Selezionare il criterio di accesso condiviso desiderato. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria. |

    * Entrambe le opzioni condividono le seguenti opzioni di configurazione:

       | Proprietà | Descrizione |
       | --- | --- |
       | Gruppo di consumer dell'hub eventi | Gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento. |
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi di evento devono essere in questo formato o i dati non possono essere letti. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviati all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer di Time Series Insights dedicato aggiunto all'hub eventi.

1. Selezionare **Crea**.

   Dopo aver creato l'origine evento, Time Series Insights avvia automaticamente lo streaming dei dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine eventi.

* Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
