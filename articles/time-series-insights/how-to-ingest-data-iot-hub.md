---
title: Come aggiungere un'origine evento dell'hub Internet delle cose-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come aggiungere un'origine evento dell'hub Internet all'ambiente Azure Time Series Insight.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 28a1990155e1406bf9b79572b3e5307cadfb8076
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665923"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Aggiungere un'origine evento dell'hub Internet all'ambiente Azure Time Series Insight

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati dall'hub IoT.

> [!NOTE]
> Le istruzioni riportate in questo articolo si applicano sia a Azure Time Series Insights generazione 1 che agli ambienti Azure Time Series Insights di generazione 2.

## <a name="prerequisites"></a>Prerequisiti

* Creare un [ambiente Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Creare un [hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md).
* L'hub IoT deve ricevere eventi messaggio attivi.
* Creare un gruppo di consumer dedicato nell'hub Internet per l'uso da parte dell'ambiente Azure Time Series Insight. Ogni origine evento Azure Time Series Insight deve avere un proprio gruppo di consumer dedicato che non è condiviso con altri consumer. Se più lettori utilizzano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori mostrino errori. Per informazioni dettagliate, vedere la guida per gli [sviluppatori dell'hub Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

Le applicazioni usano gruppi di consumer per estrarre i dati dall'hub IoT di Azure. Per leggere in modo affidabile i dati dall'hub Internet delle cose, fornire un gruppo di consumer dedicato usato solo da questo ambiente Azure Time Series Insight.

Per aggiungere un nuovo gruppo di consumer all'hub IoT:

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'hub Internet.

1. In **Impostazioni**selezionare **endpoint predefiniti**, quindi selezionare l'endpoint **eventi** .

   [![Nella pagina Endpoint predefiniti selezionare il pulsante Eventi.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. In **Gruppi di consumer** immettere un nome univoco per il gruppo di consumer. Usare lo stesso nome nell'ambiente Azure Time Series Insight quando si crea una nuova origine evento.

1. Selezionare **Salva**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **Tutte le risorse**. Selezionare l'ambiente Azure Time Series Insights.

1. In **Impostazioni**selezionare **origini eventi**, quindi selezionare **Aggiungi**.

   [![Selezionare le origini eventi e quindi selezionare il pulsante Aggiungi.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Nel riquadro **nuovo origine evento** , per **nome origine evento**, immettere un nome univoco per questo ambiente Azure Time Series Insight. ad esempio **event-stream**.

1. Per **Origine** selezionare **Hub IoT**.

1. Selezionare un valore per **Opzione di importazione**:

   * Se per una delle sottoscrizioni disponibili esiste già un hub IoT, scegliere **Usare l'hub IoT dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.

     [![Selezionare le opzioni nel riquadro Nuova origine evento.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Usare l'hub IoT dalle sottoscrizioni disponibili**:

       [![Riquadro Nuova origine evento - Proprietà da impostare nell'opzione Usare l'hub IoT dalle sottoscrizioni disponibili](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Proprietà | Descrizione |
       | --- | --- |
       | Subscription | Sottoscrizione a cui appartiene l'hub Internet. |
       | Nome dell'hub IoT | Nome dell'hub di Internet delle cose selezionato. |
       | Nome criterio dell'hub IoT | Selezionare i criteri di accesso condiviso. È possibile trovare i criteri di accesso condiviso nella scheda Impostazioni hub Internet. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**. |
       | Chiave criteri hub IoT | La chiave è prepopolata. |

   * Scegliere **Specificare le impostazioni dell'hub IoT manualmente** se l'hub IoT è esterno alle sottoscrizioni o per scegliere opzioni avanzate.

      La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub IoT manualmente**:

       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | Sottoscrizione a cui appartiene l'hub Internet. |
       | Resource group | Il nome del gruppo di risorse in cui è stato creato questo hub IoT. |
       | Nome dell'hub IoT | Il nome dell'hub IoT. Al momento della creazione, è stato immesso un nome specifico per l'hub IoT. |
       | Nome criterio dell'hub IoT | Criteri di accesso condiviso. È possibile creare i criteri di accesso condiviso nella scheda Impostazioni hub Internet. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**. |
       | Chiave criteri hub IoT | La chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria. |

   * Entrambe le opzioni condividono le opzioni di configurazione seguenti:

       | Proprietà | Descrizione |
       | --- | --- |
       | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento. |
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer Azure Time Series Insight dedicato aggiunto all'hub Internet.

1. Selezionare **Crea**.

1. Dopo aver creato l'origine evento, Azure Time Series Insight avvia automaticamente lo streaming dei dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Accedere all'ambiente in [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).
