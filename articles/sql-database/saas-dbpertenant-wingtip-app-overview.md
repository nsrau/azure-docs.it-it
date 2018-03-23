---
title: Esempio di app multi-tenant di database SQL di Azure - SaaS Wingtip | Microsoft Docs
description: Uso dell'esempio SaaS Wingtip, un'applicazione multi-tenant di esempio basata sul database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 451e1fc87fc5f626e78760d8cd5c4115ea02bb0d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introduzione a un'app SaaS multi-tenant che usa il modello di database per tenant con il database SQL

L'applicazione *SaaS Wingtip* applicazione è un'app multi-tenant di esempio. L'app usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'app presenta le funzionalità del database SQL di Azure che supportano gli scenari SaaS, usando diversi modelli di progettazione e gestione SaaS. Per iniziare, è sufficiente distribuire l'app SaaS Wingtip, operazione che richiede meno di cinque minuti.

Il codice sorgente dell'applicazione e gli script di gestione sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Prima di iniziare, leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script di gestione di Wingtip Tickets.

## <a name="application-architecture"></a>Architettura dell'applicazione

L'app SaaS Wingtip usa il modello con un database per tenant e i pool elastici SQL per ottimizzare l'efficienza. Per il provisioning e il mapping dei tenant ai dati, viene usato un database di catalogo. L'applicazione SaaS Wingtip principale usa un pool con tre tenant di esempio, oltre al database del catalogo. Completando molte delle esercitazioni sull'app SaaS Wingtip si aggiungono componenti alla distribuzione iniziale. Vengono infatti introdotti database di analisi, funzioni di gestione dello schema tra database e così via.


![Architettura SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Mentre si eseguono le esercitazioni e si utilizza l'app, è importante concentrarsi sugli schemi SaaS perché sono correlati al livello dati. In altre parole, concentrarsi sul livello dati senza dedicare troppo tempo all'analisi dell'app stessa. Comprendere l'implementazione di questi modelli SaaS è cruciale per implementare gli stessi modelli nelle proprie applicazioni, tenendo naturalmente conto delle eventuali modifiche necessarie per soddisfare requisiti aziendali specifici.

## <a name="sql-database-wingtip-saas-tutorials"></a>Esercitazioni su SaaS con l'applicazione Wingtip e database SQL

Dopo avere distribuito l'app, esplorare le esercitazioni seguenti che si basano sulla distribuzione iniziale. Queste esercitazioni illustrano i comuni modelli SaaS che sfruttano le funzionalità predefinite del database SQL, di SQL Data Warehouse e di altri servizi di Azure. Le esercitazioni includono script di PowerShell, con spiegazioni dettagliate che semplificano notevolmente la comprensione e l'implementazione degli stessi modelli di gestione SaaS nelle proprie applicazioni.


| Esercitazione | DESCRIZIONE |
|:--|:--|
| [Materiale sussidiario e suggerimenti per l'esempio di app SaaS multi-tenant di database SQL di Azure](saas-tenancy-wingtip-app-guidance-tips.md) | **INIZIARE DA QUI.** Scaricare ed eseguire gli script di PowerShell per preparare le parti dell'applicazione. |
|[Distribuire ed esplorare l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)|  Distribuire ed esplorare l'applicazione SaaS Wingtip nella sottoscrizione di Azure. |
|[Effettuare il provisioning di tenant e registrarli nel catalogo](saas-dbpertenant-provision-and-catalog.md)| Informazioni su come l'applicazione si connette ai tenant usando un database di catalogo e su come il catalogo esegue il mapping dei tenant ai dati. |
|[Monitorare e gestire le prestazioni](saas-dbpertenant-performance-monitoring.md)| Informazioni su come usare le funzionalità di monitoraggio del database SQL e su come impostare avvisi quando vengono superate le soglie per le prestazioni. |
|[Eseguire il monitoraggio con Log Analytics (OMS)](saas-dbpertenant-log-analytics.md) | Informazioni su come usare [Log Analytics](../log-analytics/log-analytics-overview.md) per monitorare grandi quantità di risorse in più pool. |
|[Ripristinare un tenant singolo](saas-dbpertenant-restore-single-tenant.md)| Informazioni su come eseguire un ripristino temporizzato di un database tenant. È inclusa anche la procedura per ripristinare un database parallelo, lasciando il database esistente online. |
|[Gestire lo schema del database tenant](saas-tenancy-schema-management.md)| Informazioni su come aggiornare lo schema e i dati di riferimento in tutti i database tenant. |
|[Eseguire query distribuite tra tenant](saas-tenancy-cross-tenant-reporting.md) | Creare un database di analisi ad-hoc ed eseguire query distribuite in tempo reale su tutti i tenant.  |
|[Eseguire analisi sui dati del tenant estratti](saas-tenancy-tenant-analytics.md) | Estrarre i dati del tenant in un database di analisi o in un data warehouse per query di analisi offline. |


## <a name="next-steps"></a>Passaggi successivi

- [Indicazioni generali e suggerimenti per la distribuzione e l'uso dell'esempio di app SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)

- [Distribuire l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
