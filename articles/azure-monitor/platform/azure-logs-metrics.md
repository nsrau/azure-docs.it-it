---
title: Log e metriche in Azure | Microsoft Docs
description: Panoramica dei log di diagnostica in Azure che forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262362"
---
# <a name="logs-and-metrics-in-azure"></a>Log e metriche in Azure
Esistono diversi [log](data-platform-logs.md) e [metriche](data-platform-metrics.md)

Il monitoraggio di applicazioni e servizi in Azure può essere suddiviso in archiviati nella [piattaforma dati di Azure](data-platform.md). 

I log e le metriche possono essere suddivisi in due categorie

- Log della piattaforma e metriche generate automaticamente senza alcuna configurazione necessaria solo se 



| Livello | Log della piattaforma | Metriche della piattaforma | Log personalizzato | Metriche personalizzate |
|:---|:---|:---|:---|:---|
| Applicazione  | | | | |
| Sistema operativo guest     | Heartbeat |  | Estensione di diagnostica<br>Agente di Log Analytics | Estensione di diagnostica |
| Resource     | [Log delle risorse](resource-logs-overview.md)<br>(specifico per ogni servizio) | [Metriche delle risorse](metrics-supported.md)<br>(specifico per ogni servizio) | | [Metriche personalizzate](metrics-custom-overview.md) |
| Sottoscrizione | [Log attività](activity-logs-overview.md) | | | |
| Tenant       | 

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](resource-logs-stream-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/)
* [Analizzare i log di Archiviazione di Azure con Monitoraggio di Azure](collect-azure-metrics-logs.md)
