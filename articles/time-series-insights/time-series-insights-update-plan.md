---
title: Plan your Preview environment - Azure Time Series Insights | Microsoft Docs
description: Learn how to plan your Azure Time Series Insights Preview environment.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9fb2dcf2c05b709340f8e9ae549bab5756e6abf2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420318"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Pianificare un ambiente di anteprima di Azure Time Series Insights

This article describes best practices to plan and get started quickly by using Azure Time Series Insights Preview.

> [!NOTE]
> For best practices to plan a general availability Time Series Insights instance, see [Plan your Azure Time Series Insights general availability environment](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Procedure consigliate per la pianificazione e preparazione

Best practices surrounding planning for and preparing your environment are described further in the following articles:

* What you get when you [provision a Time Series Insights Preview environment](#the-preview-environment).
* What your [Time Series IDs and Timestamp properties are](#configure-time-series-ids-and-timestamp-properties).
* What the new [Time Series Model is](#understand-the-time-series-model), and how to build your own.
* How to [send events efficiently in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights employs a pay-as-you-go business model. Per altre informazioni sui costi e la capacità, vedere [Time Series Insights pricing](https://azure.microsoft.com/pricing/details/time-series-insights/) (Prezzi di Time Series Insights).

## <a name="the-preview-environment"></a>The preview environment

Quando si esegue il provisioning di un ambiente di anteprima di Time Series Insights, vengono create due risorse di Azure:

* Un ambiente Anteprima di Azure Time Series Insights
* Un account di archiviazione di Azure per utilizzo generico V1

As part of the provisioning process, you specify whether you want to enable a warm store. Warm store provides you with a tiered query experience. When enabled, you must specify a retention period between 7 and 30 days. Queries executed within the warm store retention period generally provide faster response times. When a query spans over the warm store retention period, it's served from cold store.

Queries on warm store are free, while queries on cold store incur costs. It's important to understand your query patterns and plan your warm store configuration accordingly. We recommend that interactive analytics on the most recent data reside in your warm store and pattern analysis and long-term trends reside in cold.

> [!NOTE]
> We currently support a maximum of 1,000 properties with warm store.

Per iniziare, sono necessari altri tre elementi:

* Un [Modello Time Series](./time-series-insights-update-tsm.md)
* Un [origine evento connessa a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventi che passino nell'origine evento](./time-series-insights-send-events.md) e che siano entrambi mappati al modello e abbiano un formato JSON valido

## <a name="review-preview-limits"></a>Review preview limits

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure Time Series IDs and Timestamp properties

Per creare un nuovo ambiente Time Series Insights, selezionare un ID Time Series. Tale operazione funge da partizione logica per i dati. Come accennato, assicurarsi di disporre dell'ID Time Series a portata di mano.

> [!IMPORTANT]
> Time Series IDs *can't be changed later*. Verificare ognuno di essi prima della selezione finale e del primo uso.

You can select up to three keys to uniquely differentiate your resources. Per altre informazioni, leggere [Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md) (Procedure consigliate per la scelta di un ID Time Series) e [Storage and ingress](./time-series-insights-update-storage-ingress.md) (Archiviazione e ingresso).

The **Timestamp** property is also important. È possibile definire questa proprietà quando si aggiungono origini evento. Ogni origine evento include una proprietà Timestamp facoltativa, usata per tenere traccia delle origini evento nel tempo. I valori di Timestamp fanno distinzione tra maiuscole/minuscole e devono essere formattati in base alle singole specifiche di ogni origine evento.

> [!TIP]
> Verificare i requisiti di formattazione e analisi per le origini evento.

Se lasciato vuoto, come timestamp dell'evento viene usato il tempo di accodamento dell'evento di un origine evento. Se si inviano dati cronologici o eventi in batch, la personalizzazione della proprietà Timestamp è più utile rispetto al tempo di accodamento dell'evento. For more information, read about how to [add event sources in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendere il modello Time Series

È ora possibile configurare il modello Time Series dell'ambiente Time Series Insights. Il nuovo modello semplifica l'individuazione e l'analisi dei dati IoT, consentendo la cura, la manutenzione e l'arricchimento dei dati Time Series e la preparazione di set di dati di livello consumer. The model uses Time Series IDs, which map to an instance that associates the unique resource with variables, known as types, and hierarchies. Informazioni sul nuovo [modello Time Series](./time-series-insights-update-tsm.md).

Il modello è dinamico, in modo da poter essere compilato in qualsiasi momento. Per iniziare rapidamente, compilarlo e caricarlo prima di eseguire il push dei dati in Time Series Insights. Per compilare il modello, vedere [Use the Time Series Model](./time-series-insights-update-how-to-tsm.md) (Usare il modello Time Series).

Per molti clienti, il modello Time Series esegue il mapping a un modello di risorse esistente o al sistema ERP già in uso. Se non si dispone di un modello esistente, viene [offerta](https://github.com/Microsoft/tsiclient) un'esperienza utente predefinita per ottenerlo rapidamente. Per conoscere i vantaggi di un modello, visualizzare l'[ambiente demo di esempio](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Dare forma agli eventi

È possibile verificare il modo in cui si inviano eventi a Time Series Insights. In teoria, gli eventi sono denormalizzati in modo corretto ed efficiente.

Una buona norma:

* Store metadata in your Time Series Model.
* Ensure that Time Series Mode, instance fields, and events include only necessary information, such as a Time Series ID or Timestamp property.

Per altre informazioni, vedere [Shape events](./time-series-insights-send-events.md#supported-json-shapes) (Dare forma agli eventi).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Review [Azure Advisor](../advisor/advisor-overview.md) to plan out your business recovery configuration options.
- Read more about [storage and ingress](./time-series-insights-update-storage-ingress.md) in the Time Series Insights Preview.
- Learn about [data modeling](./time-series-insights-update-tsm.md) in the Time Series Insights Preview.
