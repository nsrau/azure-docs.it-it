---
title: Aggiungere un'origine evento di hub eventi-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come aggiungere un'origine evento di hub eventi di Azure all'ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: fd57231901c157ffc0d5a3d4219d827629b401f3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764177"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Aggiungere un'origine evento di un hub eventi all'ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati da Hub eventi di Azure.

> [!NOTE]
> I passaggi descritti in questo articolo si applicano sia agli ambienti Time Series Insights GA che Time Series Insights Preview.

## <a name="prerequisites"></a>Prerequisiti

- Creare un ambiente di Time Series Insights come descritto in [creare un ambiente Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Creare un hub eventi. Vedere [creare uno spazio dei nomi di hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
- L'hub eventi deve ricevere eventi messaggio attivi. Informazioni su come [inviare eventi a hub eventi di Azure usando il .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Creare un gruppo di consumer dedicato nell'hub eventi da usare nell'ambiente Time Series Insights. Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato non condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, tutti i lettori hanno probabilità di vedere errori. È previsto un limite di 20 gruppi di consumer per hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Aggiungere un gruppo di consumer all'hub eventi

Le applicazioni usano gruppi di consumer per estrarre i dati da Hub eventi di Azure. Per leggere in modo affidabile i dati dall'hub eventi, fornire un gruppo di consumer dedicato usato solo da questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer nell'hub eventi:

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'istanza di hub eventi dal riquadro **Panoramica** dello spazio dei nomi dell'hub eventi. Selezionare **entità > Hub eventi** o trovare l'istanza in **nome**.

    [![aprire lo spazio dei nomi dell'hub eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Nell'istanza dell'hub eventi selezionare **entità > gruppi di consumer**. Quindi selezionare **+ gruppo di consumer** per aggiungere un nuovo gruppo di consumer. 

   [![Hub eventi-aggiungere un gruppo di consumer](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   In caso contrario, selezionare un gruppo di consumer esistente e passare alla sezione successiva.

1. Nella pagina **Gruppi di consumer** immettere un nuovo valore univoco per **Nome**.  Usare questo nome al momento della creazione di una nuova origine evento nell'ambiente Time Series Insights.

1. Selezionare **Create** (Crea).

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente Time Series Insights esistente. Nel menu a sinistra selezionare **Tutte le risorse** e quindi selezionare l'ambiente Time Series Insights.

1. Selezionare **origini eventi**, quindi selezionare **Aggiungi**.

   [![in origini evento, selezionare il pulsante Aggiungi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Immettere un valore per il **nome dell'origine evento** univoco per questo ambiente di Time Series Insights, ad esempio `Contoso-TSI-GA-Event-Hub-ES`.

1. In **Origine** selezionare **Hub eventi**.

1. Selezionare i valori appropriati in **Opzione di importazione**:

   * Se in una delle sottoscrizioni è presente un hub eventi esistente, selezionare **Usare un hub eventi dalle sottoscrizioni disponibili**. Questa opzione è l'approccio più semplice.

     [![selezionare un'opzione di importazione dell'origine evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  La tabella seguente descrive le proprietà obbligatorie per l'opzione **Usare un hub eventi dalle sottoscrizione disponibili**:

       [Dettagli ![sottoscrizione e hub eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Proprietà | Description |
       | --- | --- |
       | Sottoscrizione | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi. |
       | Nome dell'hub eventi | Nome dell'istanza di hub eventi desiderata. |
       | Valore criterio Hub eventi | Selezionare i criteri di accesso condiviso desiderati. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Pre-popolato dal valore dei criteri dell'hub eventi selezionato. |

    * Se l'hub eventi è esterno alle sottoscrizioni o se si vogliono scegliere opzioni avanzate, selezionare **Specificare le impostazioni dell'hub eventi manualmente**.

       La tabella seguente descrive le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub eventi manualmente**:
 
       | Proprietà | Description |
       | --- | --- |
       | ID sottoscrizione | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Gruppo di risorse | Il gruppo di risorse a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi. |
       | Nome dell'hub eventi | Nome dell'istanza di hub eventi desiderata. |
       | Valore criterio Hub eventi | Selezionare i criteri di accesso condiviso desiderati. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria. |

    * Entrambe le opzioni condividono le opzioni di configurazione seguenti:

       | Proprietà | Description |
       | --- | --- |
       | Gruppo di consumer dell'hub eventi | Gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per l'origine evento. |
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi di evento devono essere in questo formato o non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviati all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer di Time Series Insights dedicato aggiunto all'hub eventi.

1. Selezionare **Create** (Crea).

   Dopo la creazione dell'origine evento, Time Series Insights avvia automaticamente il flusso dei dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
