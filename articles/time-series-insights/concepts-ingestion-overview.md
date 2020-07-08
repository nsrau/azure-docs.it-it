---
title: Panoramica dell'inserimento-Azure Time Series Insights | Microsoft Docs
description: Informazioni sull'inserimento di dati in Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049957"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Panoramica dell'inserimento dei dati Azure Time Series Insights

L'ambiente di Azure Time Series Insights contiene un *motore* di inserimento per la raccolta, l'elaborazione e l'archiviazione dei dati delle serie temporali di streaming. Con l'arrivo dei dati nelle origini eventi, Azure Time Series Insights utilizzerà e memorizzerà i dati quasi in tempo reale.

[![Panoramica dell'inserimento](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Argomenti relativi all'inserimento

Gli articoli seguenti illustrano in dettaglio l'elaborazione dei dati, incluse le procedure consigliate da seguire:

* Leggere le informazioni sulle [origini eventi](concepts-streaming-ingestion-event-sources.md) e informazioni aggiuntive sulla selezione di un timestamp dell'origine evento.

* Esaminare i [tipi di dati](concepts-supported-data-types.md) supportati

* Informazioni su come il motore di inserimento applicherà un set di [regole](./concepts-json-flattening-escaping-rules.md) alle proprietà JSON per creare le colonne dell'account di archiviazione.

* Esaminare le [limitazioni della velocità effettiva](concepts-streaming-throughput-limitations.md) dell'ambiente per pianificare le esigenze di scalabilità.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle [origini eventi](concepts-streaming-ingestion-event-sources.md) per l'ambiente di Azure Time Series Insights, continuare. 
