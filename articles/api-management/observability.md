---
title: Osservabilità in gestione API di Azure | Microsoft Docs
description: Panoramica di tutte le opzioni di osservabilità in gestione API di Azure.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097827"
---
# <a name="observability-in-azure-api-management"></a>Osservabilità in gestione API di Azure

L'osservabilità è la capacità di comprendere lo stato interno di un sistema dai dati che produce e la possibilità di esplorare i dati per rispondere a domande su cosa è successo e perché. 

Gestione API di Azure consente alle organizzazioni di centralizzare la gestione di tutte le API. Poiché funge da singolo punto di ingresso di tutto il traffico API, rappresenta una posizione ideale per osservare le API. 

## <a name="observability-tools"></a>Strumenti di osservabilità

La tabella seguente riepiloga tutti gli strumenti supportati da gestione API per osservare le API, ciascuno dei quali è utile per uno o più scenari:

| Strumento        | Utile per    | Ritardo dati | Conservazione | campionamento | Tipo di dati | Attivato|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[Controllo API](api-management-howto-api-inspector.md)** | Test e debug | Adesso | Ultime 100 tracce | Attivato per richiesta | Tracce delle richieste | Sempre
| **Analisi predefinita** | Monitoraggio e creazione di report | Minuti | Durata | 100% | Report e log | Sempre |
| **[Metriche di Monitoraggio di Azure](api-management-howto-use-azure-monitor.md)** | Monitoraggio e creazione di report | Minuti | 93 giorni (aggiornamento da estendere) | 100% | Metriche | Sempre |
| **[Log di monitoraggio di Azure](api-management-howto-use-azure-monitor.md)** | Creazione di report, monitoraggio e debug | Minuti | 31 giorni/5 GB (aggiornamento da estendere) | 100% (regolabile) | Log | Facoltativo |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | Creazione di report, monitoraggio e debug | Secondi | 90 giorni/5 GB (aggiornamento da estendere) | Personalizzato | Log, metriche | Facoltativo |
| **[Registrazione tramite hub eventi di Azure](api-management-howto-log-event-hubs.md)** | Scenari personalizzati | Secondi | Gestiti dall'utente | Personalizzato | Personalizzato | Facoltativo |

### <a name="self-hosted-gateway"></a>Gateway self-hosted

Tutti gli strumenti indicati in precedenza sono supportati dal gateway gestito nel cloud. Il [gateway self-hosted](self-hosted-gateway-overview.md) attualmente non invia i log di diagnostica a monitoraggio di Azure. Tuttavia, è possibile configurare e salvare in modo permanente i log in locale in cui viene distribuito il gateway self-hosted. Per altre informazioni, vedere [configurazione di metriche e log cloud per il gateway self-hosted](how-to-configure-cloud-metrics-logs.md) e [configurazione di metriche e log locali per il gateway self-hosted](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Segui le esercitazioni per scoprire di più su gestione API](import-and-publish.md)
