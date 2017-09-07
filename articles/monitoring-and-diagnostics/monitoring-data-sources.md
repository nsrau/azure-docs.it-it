---
title: Uso di dati di monitoraggio in Azure | Microsoft Docs
description: Informazioni su tutte le origini dati di monitoraggio disponibili in Azure al momento.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6586cad908e6527b874dc3f0ee256c6b2cea8c49
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="consume-monitoring-data-from-azure"></a>Uso di dati di monitoraggio in Azure

Nella piattaforma di Azure il monitoraggio dati e la pipeline di Monitoraggio di Azure sono stati unificati in un unica posizione, ma è noto che attualmente non tutti i dati di monitoraggio sono disponibili nella pipeline. In questo articolo vengono riepilogati i vari metodi per accedere a livello di programmazione ai dati di monitoraggio dai servizi di Azure.

## <a name="options-for-data-consumption"></a>Opzioni per l'uso di dati

| Tipo di dati | Categoria | Servizi supportati | Metodi di accesso |
| --- | --- | --- | --- |
| Metriche a livello di piattaforma di Monitoraggio di Azure | Metrica | [Vedere l'elenco di seguito](monitoring-supported-metrics.md) | <ul><li>**API REST:** [API per la metrica di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Archiviazione BLOB o Hub eventi:** [impostazioni di diagnostica](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Metriche del sistema operativo guest di calcolo (ad esempio contatori delle prestazioni) | Metrica | Macchine virtuali di [Windows](../virtual-machines-dotnet-diagnostics.md) e Linux (v2), [servizi cloud](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabella di archiviazione o BLOB:** [diagnostica di Azure per Windows o Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub eventi:** [diagnostica di Azure per Windows](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Metriche personalizzate o dell'applicazione | Metrica | Un'applicazione qualsiasi dotata di Application Insights | <ul><li>**API REST:** [API REST di Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Metriche di archiviazione | Metrica | Archiviazione di Azure | <ul><li>**Tabella di archiviazione:** [Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dati di fatturazione | Metrica | Tutti i servizi di Azure | <ul><li>**API REST:** [Introduzione all'API di uso delle risorse di Azure e all’API RateCard](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Log attività | Eventi | Tutti i servizi di Azure | <ul><li>**API REST:** [API per gli eventi di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Archiviazione BLOB o Hub eventi:** [profilo log](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Log di diagnostica di Monitoraggio di Azure | Eventi | [Vedere l'elenco di seguito](monitoring-diagnostic-logs-schema.md) | <ul><li>**Archiviazione BLOB o Hub eventi:** [impostazioni di diagnostica](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Log del sistema operativo guest di calcolo (ad esempio IIS, ETW, SysLog) | Eventi | Macchine virtuali di [Windows](../virtual-machines-dotnet-diagnostics.md) e Linux (v2), [servizi cloud](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabella di archiviazione o BLOB:** [diagnostica di Azure per Windows o Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub eventi:** [diagnostica di Azure per Windows](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Log del servizio app | Eventi | Servizi app | <ul><li>**File, tabella o archiviazione BLOB:** [diagnostica app Web](../app-service-web/web-sites-enable-diagnostic-log.md)</li></ul> |
| Log di archiviazione | Eventi | Archiviazione di Azure | <ul><li>**Tabella di archiviazione:** [Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Avvisi del Centro sicurezza | Eventi | Centro sicurezza di Azure | <ul><li>**API REST:** [avvisi di sicurezza](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Creazione di report in Active Directory | Eventi | Azure Active Directory | <ul><li>**API REST:** [API Graph di Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Stato delle risorse del Centro sicurezza | Status | [Tutte le risorse supportate](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST:** [stati di sicurezza](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Integrità delle risorse | Status | Servizi supportati | <ul><li>**API REST:** [API REST per l'integrità delle risorse](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Avvisi delle metriche di Monitoraggio di Azure | Notifiche | [Vedere l'elenco di seguito](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [avvisi delle metriche di Azure](insights-webhooks-alerts.md)</li></ul> |
| Avvisi di Log attività di Monitoraggio di Azure | Notifiche | Tutti i servizi di Azure | <ul><li>**Webhook:** avvisi di Log attività di Azure</li></ul> |
| Notifiche relative alla scalabilità automatica | Notifiche | [Vedere l'elenco di seguito](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [schema di payload del webhook di notifica di scalabilità automatica](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Avvisi di query di ricerca log OMS | Notifiche | Log Analytics OMS | <ul><li>**Webhook:** [Avvisi Log Analytics](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Avvisi relativi alle metriche in Application Insights | Notifiche | Application Insights | <ul><li>**Webhook:** [avvisi in Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Test Web in Application Insights | Notifiche | Application Insights | <ul><li>**Webhook:** [avvisi in Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche di Monitoraggio di Azure](monitoring-overview-metrics.md)
- Altre informazioni su [Log attività di Azure](monitoring-overview-activity-logs.md)
- Altre informazioni sui [Log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)

