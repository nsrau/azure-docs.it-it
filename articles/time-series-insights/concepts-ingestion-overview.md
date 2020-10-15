---
title: Panoramica dell'inserimento-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sull'inserimento di dati in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460901"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Panoramica dell'inserimento dati in Azure Time Series Insights Gen2

L'ambiente Gen2 Azure Time Series Insights contiene un *motore* di inserimento per la raccolta, l'elaborazione e l'archiviazione dei dati delle serie temporali di streaming. Con l'arrivo dei dati nelle origini eventi, Azure Time Series Insights Gen2 utilizzerà e memorizzerà i dati quasi in tempo reale.

[![Panoramica dell'inserimento dati](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Argomenti relativi all'inserimento

Gli articoli seguenti illustrano in dettaglio l'elaborazione dei dati, incluse le procedure consigliate da seguire:

* Leggere le informazioni sulle [origini eventi](./concepts-streaming-ingestion-event-sources.md) e informazioni aggiuntive sulla selezione di un timestamp dell'origine evento.

* Esaminare i [tipi di dati](./concepts-supported-data-types.md) supportati

* Informazioni su come il motore di inserimento applicherà un set di [regole](./concepts-json-flattening-escaping-rules.md) alle proprietà JSON per creare le colonne dell'account di archiviazione.

* Esaminare le [limitazioni della velocità effettiva](./concepts-streaming-ingress-throughput-limits.md) dell'ambiente per pianificare le esigenze di scalabilità.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle [origini eventi](./concepts-streaming-ingestion-event-sources.md) per l'ambiente Azure Time Series Insights Gen2, continuare.
