---
title: Aggiornamenti della terminologia di Monitoraggio di Azure | Microsoft Docs
description: Descrive le modifiche alla terminologia recentemente apportate ai servizi di monitoraggio di Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2019
ms.author: bwren
ms.openlocfilehash: dc7d839c81edd67d69e1525d219f87ee70f17502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300512"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Modifiche di denominazione e terminologia in Monitoraggio di Azure
Di recente sono state apportate modifiche significative a Monitoraggio di Azure, con diversi servizi consolidati per semplificare il servizio di monitoraggio da parte dei clienti di Azure. Questo articolo descrive le recenti modifiche apportate alla terminologia e ai nomi all'interno della documentazione di Monitoraggio di Azure.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Ottobre 2019-log di diagnostica nel log delle risorse
I log di diagnostica sono stati modificati in "log delle risorse" per una migliore corrispondenza tra quelli effettivamente raccolti. Il termine "impostazioni di diagnostica" rimane invariato.  

## <a name="february-2019---log-analytics-terminology"></a>Febbraio 2019 - Terminologia di Log Analytics
Dopo il consolidamento di diversi servizi in Monitoraggio di Azure, stiamo affrontando il passaggio successivo con la modifica della terminologia nella documentazione per descrivere meglio il servizio di Monitoraggio di Azure e i relativi componenti. 

### <a name="log-analytics"></a>Log Analytics
I dati di log di Monitoraggio di Azure rimangono archiviati in un'area di lavoro Log Analytics e vengono ancora raccolti e analizzati dal servizio Log Analytics stesso, tuttavia è in corso, in molte posizioni, la modifica del termine _Log Analytics_, in _Log di Monitoraggio di Azure_ . Questo termine riflette meglio il proprio ruolo in monitoraggio di Azure e offre una maggiore coerenza con le [metriche in monitoraggio di Azure](platform/data-platform-metrics.md).

Il termine _Log Analytics_ si applica ora principalmente alla pagina nel portale di Azure usata per scrivere, eseguire query e analizzare i dati di log. È l'equivalente funzionale di [Esplora metriche](platform/metrics-charts.md), ovvero la pagina nel portale di Azure usata per analizzare i dati delle metriche.

### <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics
Le [aree di lavoro](platform/manage-access.md) che contengono i dati di log in Monitoraggio di Azure si riferiscono ancora alle aree di lavoro di Log Analytics. Il menu **Log Analytics** nel portale di Azure è stato rinominato **Aree di lavoro di Log Analytics** ed è l'area in cui si possono [creare nuove aree di lavoro](learn/quick-create-workspace.md) e configurare le origini dati. Analizzare i log e gli altri dati di monitoraggio in **Monitoraggio di Azure** e configurare l'area di lavoro in **Aree di lavoro di Log Analytics**.

### <a name="management-solutions"></a>Soluzioni di gestione
Le [Soluzioni di gestione](insights/solutions.md) sono state rinominate _Soluzioni di monitoraggio_, il che ne descrive meglio le funzionalità.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto 2018 - Consolidamento dei servizi di monitoraggio in Monitoraggio di Azure
Log Analytics e Application Insights sono stati consolidati in Monitoraggio di Azure per fornire un'unica esperienza integrata per il monitoraggio delle risorse di Azure e degli ambienti ibridi. Nessuna funzionalità è stata rimossa da questi servizi e gli utenti possono eseguire gli stessi scenari come in precedenza senza nessuna perdita o compromissione di funzionalità.

Anche la documentazione di ognuno di questi servizi è stata consolidata in un unico set di contenuti per Monitoraggio di Azure. Il lettore potrà quindi trovare più facilmente tutti i contenuti riguardanti un determinato scenario di monitoraggio in un'unica posizione senza dover fare riferimento a più set di contenuti. Man mano che tale servizio consolidato evolve, il contenuto diventerà più integrato.

Altre funzionalità che erano considerate parti di Log Analytics, ad esempio gli agenti e le visualizzazioni, sono state riposizionate come funzionalità di Monitoraggio di Azure. Il loro funzionamento non è cambiato se non per i potenziali miglioramenti della loro esperienza nel portale di Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Agosto 2018 - Ritiro del marchio Operations Management Suite
Operations Management Suite (OMS) era un bundle dei servizi di gestione di Azure seguenti per finalità di licenza:

- Application Insights
- Automazione di Azure
- Backup di Azure
- Log Analytics
- Site Recovery

[Per questi servizi è stato introdotto un nuovo piano tariffario](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) e il bundle OMS non è più disponibile per i nuovi clienti. Nessuno dei servizi che facevano parte di OMS è stato modificato, fatta eccezione per il consolidamento in Monitoraggio di Azure descritto in precedenza. 




## <a name="next-steps"></a>Passaggi successivi

- Leggere l'articolo [Panoramica di Monitoraggio di Azure](overview.md), che riporta la descrizione dei vari componenti e delle relative funzionalità.
- Informazioni sulla [migrazione del portale di OMS](../log-analytics/log-analytics-oms-portal-transition.md).
