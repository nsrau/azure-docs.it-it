---
title: Monitoraggio continuo con Monitoraggio di Azure | Microsoft Docs
description: Descrive i passaggi specifici per l'utilizzo del Monitoraggio di Azure per abilitare il monitoraggio continuo durante i flussi di lavoro.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: 1b86bc015b187fe75e79ba04df60a6bc5257a9b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60497421"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitoraggio continuo con Monitoraggio di Azure

Il monitoraggio continuo si riferisce al processo e alla tecnologia necessari per integrare il monitoraggio in ogni fase di DevOps e dei cicli di vita delle operazioni IT. Consente di garantire costantemente l'integrità, le prestazioni e l'affidabilità dell'applicazione e dell'infrastruttura durante il passaggio dallo sviluppo alla produzione. Il monitoraggio continuo si basa sui concetti di integrazione continua e distribuzione continua (CI/CD) che consentono di sviluppare e offrire software più rapidi e affidabile per offrire un valore continuo agli utenti.

[Monitoraggio di Azure](overview.md) è la soluzione di monitoraggio unificata di Azure che fornisce l'osservabilità dello stack completo nelle applicazioni e nell'infrastruttura nel cloud e in locale. Si integra perfettamente con [Visual Studio e Visual Studio Code](https://visualstudio.microsoft.com/) durante lo sviluppo e il test e si integra con [Azure DevOps](/azure/devops/user-guide/index) per la gestione del rilascio e la gestione degli elementi di lavoro durante la distribuzione e le operazioni. Si integra anche tra gli strumenti di Gestione dei servizi IT e SIEM di propria scelta per tenere traccia dei problemi e degli eventi imprevisti all'interno dei processi IT esistenti.

Questo articolo descrive i passaggi specifici per l'utilizzo del Monitoraggio di Azure per abilitare il monitoraggio continuo durante i flussi di lavoro. Include collegamenti ad altri documenti che forniscono informazioni dettagliate sull'implementazione di funzionalità diverse.


## <a name="enable-monitoring-for-all-your-applications"></a>Abilitare il monitoraggio per tutte le applicazioni
Per ottenere l'osservabilità nell'intero ambiente, è necessario abilitare il monitoraggio in tutte le applicazioni web e nei servizi. In questo modo sarà possibile visualizzare con facilità le transazioni end-to-end e le connessioni tra tutti i componenti.

- [Azure DevOps Projects](../devops-project/overview.md) fornisce un'esperienza semplificata con il codice esistente e il repository Git oppure scegliere una delle applicazioni di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e distribuzione continua (CD, Continuous Delivery) in Azure.
- [Monitoraggio continuo nelle pipeline di rilascio DevOps](../azure-monitor/app/continuous-monitoring.md) consente il controllo o il rollback della distribuzione in base ai dati di monitoraggio.
- [Status Monitor](../azure-monitor/app/monitor-performance-live-website-now.md) consente di instrumentare un'app .NET attiva in Windows con Azure Application Insights senza dover modificare o ridistribuire il codice.
- Se si ha accesso al codice per l'applicazione, abilitare il monitoraggio completo con [Application Insights](../azure-monitor/app/app-insights-overview.md) installando Application Insights SDK del Monitoraggio di Azure per [.NET](../azure-monitor/learn/quick-monitor-portal.md), [Java ](../azure-monitor/learn/java-quick-start.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md), o per [eventuali altri linguaggi di programmazione](../azure-monitor/app/platforms.md). In questo modo è possibile specificare gli eventi personalizzati, le metriche o le visualizzazioni di pagina che sono rilevanti per l'applicazione e l'attività dell'utente.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Abilitare il monitoraggio per l'intera infrastruttura
Le applicazioni sono solo affidabili come la relativa infrastruttura sottostante. Il monitoraggio abilitato per l'intera infrastruttura consente di ottenere l'osservabilità completa e rende più semplice individuare una potenziale causa radice quando qualcosa non funziona. Il Monitoraggio di Azure consente di tenere traccia dell'integrità e delle prestazioni dell'intera infrastruttura ibrida, comprese le risorse quali ad esempio macchine virtuali, contenitori, archiviazione e rete.

- Si ottengono automaticamente [piattaforme metriche, log attività e log di diagnostica](platform/data-sources.md) dalla maggior parte delle risorse di Azure senza alcuna configurazione.
- Abilitare il monitoraggio più profondo per le macchine virtuali con [Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md).
-  Abilitare il monitoraggio più profondo per i cluster del servizio Azure Kubernetes con [Monitoraggio di Azure per contenitori](insights/container-insights-overview.md).
- Aggiungere [soluzioni di monitoraggio](insights/solutions-inventory.md) per applicazioni e servizi diversi nell'ambiente in uso.


[Infrastruttura come codice](/azure/devops/learn/what-is-infrastructure-as-code) è la gestione dell'infrastruttura in un modello descrittivo, usando lo stesso controllo delle versioni usato dai team DevOps per il codice sorgente. Aggiunge affidabilità e scalabilità all'ambiente e consente di sfruttare i processi simili che consentono di gestire le applicazioni.

-  Usare [modelli di Resource Manager](platform/template-workspace-configuration.md) per abilitare il monitoraggio e configurare gli avvisi tramite un ampio set di risorse.
- Usare [Criteri di Azure](../governance/policy/overview.md) per applicare regole diverse in relazione alle risorse. Ciò assicura che tali risorse rimangano conformi con gli standard aziendali e i contratti di servizio. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combinare le risorse nei gruppi di risorse di Azure
Una tipica applicazione in Azure oggi include più risorse, ad esempio le macchine virtuali e servizi App o i microservizi ospitati in servizi Cloud, i cluster del servizio Azure Kubernetes o Service Fabric. Queste applicazioni usano spesso le dipendenze, ad esempio hub eventi, archiviazione, SQL e bus di servizio.

- Combinare le risorse nei gruppi di risorse di Azure per ottenere visibilità completa in tutte le risorse che costituiscono le diverse applicazioni. [Monitoraggio di Azure per i gruppi di risorse](../azure-monitor/insights/resource-group-insights.md) fornisce un modo semplice per tenere traccia dell'integrità e delle prestazioni dell'intera applicazione dello stack completo e abilita il drill-down nei rispettivi componenti per eventuali analisi o debug.

## <a name="ensure-quality-through-continuous-deployment"></a>Garantire la qualità tramite la distribuzione continua
Integrazione continua/distribuzione continua consente di integrare automaticamente e distribuire le modifiche del codice nell'applicazione in base ai risultati dei test automatizzati. Semplifica il processo di distribuzione e assicura la qualità di tutte le modifiche prima di passare alla produzione.


- Usare [Azure Pipelines](/azure/devops/pipelines) per implementare la distribuzione continua e automatizzare l'intero processo dal commit del codice all'ambiente di produzione in base ai test di integrazione continua/distribuzione continua.
- Usare [Gate di qualità](/azure/devops/pipelines/release/approvals/gates) per integrare il monitoraggio al momento della pre-distribuzione o della post-distribuzione. Ciò garantisce che si stiano soddisfacendo le metriche chiave di integrità/prestazioni (KPI) non appena le applicazioni si spostano dallo sviluppo alla produzione e le eventuali differenze nell'ambiente di infrastruttura o che la scalabilità non influisca negativamente sugli indicatori KPI.
- [Mantenere le istanze di monitoraggio separate](../azure-monitor/app/separate-resources.md) tra gli ambienti di distribuzione diversi, ad esempio sviluppo, Test, Canary e produzione. Ciò garantisce che i dati raccolti siano rilevanti per le applicazioni associate e per l'infrastruttura. Se è necessario correlare i dati tra ambienti, è possibile usare [i grafici a più risorse in Esplora metriche](../azure-monitor/platform/metrics-charts.md) oppure creare [query tra risorse in Monitoraggio di Azure](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Creare avvisi azionabili con azioni
Un aspetto critico del monitoraggio è notificare in modo proattivo agli amministratori eventuali problemi attuali e possibili. 

- Creare [gli avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-overview.md) in base ai log e alle metriche per identificare gli stati di errore prevedibili. È necessario avere l'obiettivo di rendere tutti gli avvisi azionabili, vale a dire fare in modo che rappresentino le condizioni critiche effettive e cerchino di ridurre i falsi positivi. Usare [soglie dinamiche](platform/alerts-dynamic-thresholds.md) per calcolare automaticamente le baseline per i dati di metrica, anziché definire soglie statiche personali. 
- Definire le azioni per gli avvisi al fine di utilizzare i mezzi più efficaci di notifica agli amministratori. Le [azioni per la notifica](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) disponibili sono SMS, messaggi di posta elettronica, notifiche push oppure chiamate vocali.
- Usare azioni più avanzate per [connettersi allo strumento Gestione dei servizi IT](platform/itsmc-overview.md) oppure altri sistemi di gestione degli avvisi tramite [webhook](platform/activity-log-alerts-webhook.md).
- Correggere situazioni identificate negli avvisi con i [runbook di Automazione di Azure](../automation/automation-webhooks.md) oppure con le [App per la logica](/connectors/custom-connectors/create-webhook-trigger) che possono essere avviate da un avviso tramite i webhook. 
- Usare la [scalabilità automatica](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) per aumentare e ridurre in modo dinamico le risorse di calcolo basate sulle metriche raccolte.

## <a name="prepare-dashboards-and-workbooks"></a>Preparare i dashboard e le cartelle di lavoro
Assicurare che lo sviluppo e le operazioni abbiano accesso agli stessi dati di telemetria e agli stessi strumenti consente loro di visualizzare i modelli nell'intero ambiente e di ridurre al minimo il tempo medio di rilevamento (MTTD) e il tempo medio di ripristino (MTTR).

- Preparare [dashboard personalizzati](../azure-monitor/learn/tutorial-app-dashboards.md) basati su metriche e log comuni per i diversi ruoli all'interno dell'organizzazione. I dashboard possono combinare dati da tutte le risorse di Azure.
- Preparare [cartelle di lavoro](../azure-monitor/app/usage-workbooks.md) per assicurarsi la condivisione delle conoscenze tra sviluppo e operazioni. Questi possono essere preparati come report dinamici con grafici delle metriche e query dei log, o persino come guide per la risoluzione dei problemi preparate dagli sviluppatori, consentendo operazioni o supporto tecnico in grado di gestire problemi di base.

## <a name="continuously-optimize"></a>Ottimizzare in modo continuativo
 Il monitoraggio è uno degli aspetti fondamentali della popolare filosofia Build-Measure-Learn, che consiglia di rilevare in modo continuo gli indicatori KPI e le metriche di comportamento dell'utente e quindi di sforzarsi per ottimizzarli attraverso iterazioni di pianificazione. Monitoraggio di Azure aiuta a raccogliere le metriche e i log rilevanti per l'azienda e ad aggiungere nuovi punti dati alla successiva distribuzione come richiesto.

- Usare gli strumenti in Application Insights per [tenere traccia del comportamento e dell'engagement degli utenti finali](../azure-monitor/learn/tutorial-users.md).
- Usare [Impact Analysis](../azure-monitor/app/usage-impact.md) per consentire di classificare le aree su cui ci si desidera concentrare per accedere a importanti indicatori KPI.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui componenti di differenza di [Monitoraggio di Azure](overview.md).
- [Aggiungere un monitoraggio continuo](../azure-monitor/app/continuous-monitoring.md) alla pipeline di versione.