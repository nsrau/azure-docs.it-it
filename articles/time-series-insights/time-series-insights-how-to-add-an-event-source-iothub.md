---
title: Come aggiungere un'origine evento dell'hub IoT in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come aggiungere un'origine evento connessa a un hub IoT nell'ambiente Time Series Insights.
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
ms.openlocfilehash: 955b0e36c63b181e2fe6d2f87e7b015196fceff9
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455610"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Aggiungere un'origine evento di un hub IoT in un ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati dall'hub IoT.

> [!NOTE]
> Le istruzioni in questo articolo si applicano sia agli ambienti di disponibilità generale che a quelli di anteprima di Azure Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

* Creare un [ambiente Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Creare un [hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md).
* L'hub IoT deve ricevere eventi messaggio attivi.
* Creare un gruppo di consumer dedicato nell'hub IoT da usare dall'ambiente Time Series Insights. Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato non condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori riscontrino errori. Per informazioni dettagliate, vedere la [Guida per gli sviluppatori dell'hub IoT di Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

Le applicazioni usano gruppi di consumer per estrarre i dati dall'hub IoT di Azure. Per leggere in modo affidabile i dati dall'hub IoT, specificare un gruppo di consumer dedicato che viene usato solo per questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer all'hub IoT:

1. Nel portale di Azure individuare e aprire l'hub IoT.

1. Sotto **le impostazioni**, selezionare **endpoint predefiniti**e quindi selezionare il **eventi** endpoint.

   [![Nella pagina di compilazione-in endpoint, selezionare il pulsante eventi](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png#lightbox)

1. In **Gruppi di consumer** immettere un nome univoco per il gruppo di consumer. Usare questo stesso nome al momento di creare una nuova origine evento nell'ambiente Time Series Insights.

1. Selezionare **Salva**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **Tutte le risorse**. Selezionare l'ambiente Time Series Insights.

1. In **Topologia dell'ambiente** selezionare **Origini eventi** e fare clic su **Aggiungi**.

   [![Selezionare le origini evento e quindi selezionare il pulsante Aggiungi](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png#lightbox)

1. Nel riquadro **Nuova origine evento** per univoco per **Nome dell'origine evento** immettere un nome univoco per questo ambiente Time Series Insights, ad esempio **event-stream**.

1. Per **Origine** selezionare **Hub IoT**.

1. Selezionare un valore per **Opzione di importazione**:

   * Se per una delle sottoscrizioni disponibili esiste già un hub IoT, scegliere **Usare l'hub IoT dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.
   
     [![Selezionare le opzioni nel riquadro nuova origine evento](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png#lightbox)

    * La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Usare l'hub IoT dalle sottoscrizioni disponibili**:

       [![Nuovo riquadro dell'origine evento - proprietà da impostare nell'IoT Hub Usa dall'opzione sottoscrizioni disponibili](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png#lightbox)

       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub IoT è stato creato.
       | Nome dell'hub IoT | Selezionare il nome dell'hub IoT.
       | Nome criterio dell'hub IoT | Selezionare i criteri di accesso condiviso. I criteri di accesso condiviso sono disponibili nella scheda Impostazioni hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
       | Chiave criteri hub IoT | La chiave è prepopolata.
       | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

    * Scegliere **Specificare le impostazioni dell'hub IoT manualmente** se l'hub IoT è esterno alle sottoscrizioni o per scegliere opzioni avanzate.

      La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub IoT manualmente**:

       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | La sottoscrizione in cui l'hub IoT è stata creata.
       | Gruppo di risorse | Il nome del gruppo di risorse in cui è stato creato questo hub IoT.
       | Nome dell'hub IoT | Il nome dell'hub IoT. Al momento della creazione, è stato immesso un nome specifico per l'hub IoT.
       | Nome criterio dell'hub IoT | I criteri di accesso condiviso. I criteri di accesso condiviso sono stati creati nella scheda Impostazioni hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
       | Chiave criteri hub IoT | La chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria.
       | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer Time Series Insights dedicato aggiunto all'hub IoT.

1. Selezionare **Create**.

   [![Il pulsante Crea](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png#lightbox)

1. Dopo la creazione dell'origine evento, Time Series Insights inizia automaticamente a trasmettere i dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
