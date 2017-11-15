---
title: Consigli di Azure Advisor sulle prestazioni | Microsoft Docs
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: e32723cd3ef13829890a630f4bff308164e17674
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-performance-recommendations"></a>Consigli di Advisor sulle prestazioni

Grazie ai consigli di Azure Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Migliorare le prestazioni del database con Advisor per database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure. Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre quindi consigli ideali per l'esecuzione del carico di lavoro tipico del database. 

> [!NOTE]
> Per ottenere consigli, è necessario che un database sia stato in uso per circa una settimana, nel corso della quale deve essersi verificata attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per altre informazioni su Advisor per database SQL, vedere [Advisor per database SQL](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Cache Redis

Advisor identifica le istanze di Cache Redis in cui le prestazioni potrebbero aver subito effetti negativi a causa di uso di memoria, carico del server, larghezza di banda di rete e numero di connessioni client elevati. Advisor fornisce inoltre consigli sulle procedure consigliate che consentono di evitare potenziali problemi. Per altre informazioni relative ai consigli su Cache Redis, vedere [Redis Cache Advisor](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Servizi app

Azure Advisor integra consigli sulle procedure consigliate per migliorare l'esperienza di Servizi app e scoprire le funzionalità della piattaforma pertinente. Esempi di consigli per Servizi app sono:
* Rilevamento delle istanze in cui le risorse di CPU o memoria vengono esaurite dai runtime delle app con opzioni di migrazione.
* Rilevamento delle istanze in cui la collocazione delle risorse come App Web e database può aumentare le prestazioni e ridurre il costo. 

Per altre informazioni sui consigli per Servizi app, vedere [Procedure consigliate per Servizio app di Azure](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Prestazioni**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

