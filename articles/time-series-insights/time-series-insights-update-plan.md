---
title: Pianificare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs
description: Pianificare un ambiente di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: b3fab86b2b2f0ad892e02cd089dbd7c45ce601d6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205764"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Pianificare un ambiente di anteprima di Azure Time Series Insights

Questo articolo descrive le procedure consigliate per pianificare e iniziare a usare rapidamente l'anteprima di Azure Time Series Insights.

> [!NOTE]
> Visualizzare [pianificare l'ambiente di versione GA di Azure ora Series Insights](time-series-insights-environment-planning.md), per le procedure consigliate pianificare un'istanza di Time Series Insights disponibilità generale.

## <a name="best-practices-for-planning-and-preparation"></a>Procedure consigliate per la pianificazione e preparazione

Per iniziare a usare Time Series Insights, è consigliabile conoscere:

* Che cosa si ottiene quando si [effettuare il provisioning di un ambiente di versione di anteprima di tempo Series Insights](#the-preview-environment).
* Che cosa le [sono proprietà ID serie ora e Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Il nuovo [modello Time Series è](#understand-the-time-series-model)e su come crearne una personalizzata.
* Saper [inviare eventi in modo efficiente in JSON](#shape-your-events).
* Time Series Insights [opzioni di ripristino di emergenza business](#business-disaster-recovery).

Azure Time Series Insights Usa un modello con pagamento a consumo. Per altre informazioni sui costi e la capacità, vedere [Time Series Insights pricing](https://azure.microsoft.com/pricing/details/time-series-insights/) (Prezzi di Time Series Insights).

## <a name="the-preview-environment"></a>L'ambiente di anteprima

Quando si esegue il provisioning di un ambiente di anteprima di Time Series Insights, vengono create due risorse di Azure:

* Un ambiente Anteprima di Azure Time Series Insights
* Un account di archiviazione di Azure per utilizzo generico V1

Per iniziare, sono necessari altri tre elementi:

* Oggetto [modello Time Series](./time-series-insights-update-tsm.md).
* Un' [origine evento connessa a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md).
* [Gli eventi che passano nell'origine evento](./time-series-insights-send-events.md) che sono entrambi mappati al modello e sono in formato JSON valido.

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurare le proprietà ID serie ora e Timestamp

Per creare un nuovo ambiente Time Series Insights, selezionare una **ID serie ora**. Tale operazione funge da partizione logica per i dati. Come accennato, assicurarsi di disporre dell'ID Time Series a portata di mano.

> [!IMPORTANT]
> Gli ID Time Series sono *immutabili* e *non possono essere modificati in un secondo momento*. Verificare ognuno di essi prima della selezione finale e del primo uso.

È possibile selezionare fino a tre (3) chiavi per distinguere le risorse in modo univoco. Per altre informazioni, leggere [Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md) (Procedure consigliate per la scelta di un ID Time Series) e [Storage and ingress](./time-series-insights-update-storage-ingress.md) (Archiviazione e ingresso).

Anche la proprietà Timestamp è importante. È possibile definire questa proprietà quando si aggiungono origini evento. Ogni origine evento include una proprietà Timestamp facoltativa, usata per tenere traccia delle origini evento nel tempo. I valori di Timestamp fanno distinzione tra maiuscole/minuscole e devono essere formattati in base alle singole specifiche di ogni origine evento.

> [!TIP]
> Verificare i requisiti di formattazione e analisi per le origini evento.

Se lasciato vuoto, come timestamp dell'evento viene usato il tempo di accodamento dell'evento di un origine evento. Se si inviano dati cronologici o eventi in batch, la personalizzazione della proprietà Timestamp è più utile rispetto al tempo di accodamento dell'evento. Per altre informazioni, leggere [How to add event sources in IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) (Procedura di aggiunta di origini evento nell'hub IoT).

## <a name="understand-the-time-series-model"></a>Comprendere il modello Time Series

È ora possibile configurare il modello Time Series dell'ambiente Time Series Insights. Il nuovo modello semplifica l'individuazione e l'analisi dei dati IoT, consentendo la cura, la manutenzione e l'arricchimento dei dati Time Series e la preparazione di set di dati di livello consumer. Il modello usa **ID Time Series**, che esegue il mapping a un'istanza che associa la risorsa univoca a variabili, note come tipi, e gerarchie. Informazioni sul nuovo [modello Time Series](./time-series-insights-update-tsm.md).

Il modello è dinamico, in modo da poter essere compilato in qualsiasi momento. Per iniziare rapidamente, compilarlo e caricarlo prima di eseguire il push dei dati in Time Series Insights. Per compilare il modello, vedere [Use the Time Series Model](./time-series-insights-update-how-to-tsm.md) (Usare il modello Time Series).

Per molti clienti, il modello Time Series esegue il mapping a un modello di risorse esistente o al sistema ERP già in uso. Se non si dispone di un modello esistente, viene [offerta](https://github.com/Microsoft/tsiclient) un'esperienza utente predefinita per ottenerlo rapidamente. Per conoscere i vantaggi di un modello, visualizzare l'[ambiente demo di esempio](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Dare forma agli eventi

È possibile verificare il modo in cui si inviano eventi a Time Series Insights. In teoria, gli eventi sono denormalizzati in modo corretto ed efficiente.

Una buona norma:

* Archiviare i metadati del modello Time Series
* Modalità di serie temporale, i campi di istanza e gli eventi includono solo le informazioni necessarie, ad esempio un **ID serie tempo** oppure **Timestamp**.

Per altre informazioni, vedere [Shape events](./time-series-insights-send-events.md#json) (Dare forma agli eventi).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni, vedere [in ingresso e in archiviazione](./time-series-insights-update-storage-ingress.md) nell'anteprima del tempo Series Insights.

- Scopri [modellazione dei dati](./time-series-insights-update-tsm.md) nell'anteprima del tempo Series Insights.