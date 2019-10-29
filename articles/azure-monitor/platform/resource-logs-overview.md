---
title: Panoramica dei log delle risorse di Azure | Microsoft Docs
description: Informazioni sui servizi e sullo schema di eventi supportati per i log delle risorse di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989613"
---
# <a name="azure-resource-logs-overview"></a>Panoramica dei log delle risorse di Azure
I log delle risorse di Azure sono [log della piattaforma](platform-logs-overview.md) emessi dalle risorse di Azure che descrivono l'operazione interna. Tutti i log delle risorse condividono uno schema di primo livello comune con la flessibilità per ogni servizio di creare proprietà univoche per i rispettivi eventi.

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica.

## <a name="collecting-resource-logs"></a>Raccolta dei log delle risorse
I log delle risorse vengono generati automaticamente dalle risorse di Azure supportate, ma non vengono raccolti a meno che non vengano configurati usando un' [impostazione di diagnostica](diagnostic-settings.md). Creare un'impostazione di diagnostica per ogni risorsa di Azure per l'invio dei log alle destinazioni seguenti:

| Destinazione | Scenario |
|:---|:---|:---|
| [area di lavoro Log Analytics](resource-logs-collect-workspace.md) | Analizzare i log con altri dati di monitoraggio e sfruttare le funzionalità di monitoraggio di Azure, ad esempio query di log e avvisi del log. |
| [Archiviazione di Azure](resource-logs-collect-storage.md) | Archiviare i log per il controllo o il backup. |
| [Hub eventi](resource-logs-stream-event-hubs.md) | Trasmettere i log a sistemi di registrazione e telemetria di terze parti.  |

## <a name="compute-resources"></a>Risorse di calcolo
I log delle risorse sono diversi dai log a livello del sistema operativo guest nelle risorse di calcolo di Azure. Per le risorse di calcolo è necessario che un agente raccolga log e metriche dal sistema operativo guest, inclusi i dati come log eventi, syslog e i contatori delle prestazioni. Usare l' [estensione di diagnostica](agents-overview.md#azure-diagnostic-extension) per instradare i dati di log dalle macchine virtuali di Azure e l' [agente di log Analytics](agents-overview.md#log-analytics-agent) per raccogliere i log e le metriche dalle macchine virtuali in Azure, in altri cloud e in locale in un'area di lavoro di log Analytics. Per informazioni dettagliate, vedere [origini dei dati di monitoraggio per monitoraggio di Azure](data-sources.md) .

## <a name="resource-logs-schema"></a>Schema dei log delle risorse
Per altre informazioni sullo schema e sulle categorie dei log delle risorse, vedere [schema del log delle risorse](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Fasi successive

* [Altre informazioni su altri log della piattaforma Azure](platform-logs-overview.md) che è possibile usare per monitorare Azure.
