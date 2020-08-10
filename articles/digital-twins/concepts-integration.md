---
title: Integrazione con altri servizi
titleSuffix: Azure Digital Twins
description: Informazioni sui requisiti in ingresso e in uscita quando si distribuiscono i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ca500401a6bff8a00dd9c51eecb29aa93fdbc82b
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042650"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrare i dispositivi gemelli digitali di Azure con altri servizi

I dispositivi gemelli digitali di Azure vengono in genere usati insieme ad altri servizi. Usando le [**route degli eventi**](concepts-route-events.md), i dispositivi gemelli digitali di Azure ricevono i dati dai servizi upstream, ad esempio l' [Hub](../iot-hub/about-iot-hub.md)Internet, che viene usato per fornire telemetria e notifiche. 

I dispositivi gemelli digitali di Azure possono anche instradare i dati ai servizi downstream, ad esempio mappe di Azure ([*procedura: usare i dispositivi gemelli di Azure per aggiornare una mappa interna di Azure Maps*](how-to-integrate-maps.md)) e Time Series Insights ([*procedura: integrazione con Time Series Insights*](how-to-integrate-time-series-insights.md)), per archiviazione, integrazione dei flussi di lavoro, analisi e altro ancora. 

## <a name="data-ingress"></a>Ingresso dei dati

I dispositivi gemelli digitali di Azure possono essere gestiti con dati ed eventi da qualsiasi servizio, ovvero l'hub delle cose, le app per la logica, il servizio personalizzato e altro ancora. In questo modo è possibile raccogliere dati di telemetria da dispositivi fisici nell'ambiente in uso ed elaborarli usando il grafo dei gemelli digitali di Azure nel cloud.

I dispositivi gemelli digitali di Azure non dispongono di un hub Internet integrato. È possibile usare un hub Internet esistente attualmente in produzione o distribuirne uno nuovo. Questo consente l'accesso completo a tutte le funzionalità di gestione dei dispositivi dell'hub Internet.

Per inserire i dati da qualsiasi origine in dispositivi gemelli digitali di Azure, usare una [funzione di Azure](../azure-functions/functions-overview.md). Per altre informazioni su questo modello, vedere [*procedura: inserire dati di telemetria dall'hub*](how-to-ingest-iot-hub-data.md)Internet o provarli nell'esercitazione sui dispositivi gemelli digitali di Azure [*: connettere una soluzione end-to-end*](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Servizi di uscita dati

I dispositivi gemelli digitali di Azure possono inviare dati agli **endpoint**connessi. Gli endpoint supportati possono essere:
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Griglia di eventi](../event-grid/overview.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Gli endpoint sono collegati ai dispositivi gemelli digitali di Azure usando le API di gestione o il portale di Azure. Per altre informazioni su come aggiungere un endpoint ai dispositivi gemelli digitali di Azure [*, vedere Procedura: gestire endpoint e route*](how-to-manage-routes-apis-cli.md).

Sono disponibili molti altri servizi in cui è possibile indirizzare i dati, ad esempio archiviazione di [Azure](../storage/common/storage-introduction.md) o [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Per inviare i dati a servizi come questi, alleghi il servizio di destinazione a un endpoint.

Se ad esempio si usano anche mappe di [Azure](../azure-maps/about-azure-maps.md) e si vuole correlare la località con il [grafo](concepts-twins-graph.md)gemello di Azure Digital gemelli, è possibile usare funzioni di Azure con griglia di eventi per stabilire la comunicazione tra tutti i servizi nella distribuzione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli endpoint e sul routing degli eventi ai servizi esterni:
* [*Concetti: routing degli eventi di Azure Digital gemelli*](concepts-route-events.md)

Vedere come configurare i dispositivi gemelli digitali di Azure per l'inserimento di dati dall'hub Internet:
* [*Procedura: inserire dati di telemetria dall'hub Internet*](how-to-ingest-iot-hub-data.md)
