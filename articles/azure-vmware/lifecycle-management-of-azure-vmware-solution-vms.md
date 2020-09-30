---
title: Gestione del ciclo di vita delle VM della soluzione VMware di Azure
description: Informazioni su come gestire tutti gli aspetti del ciclo di vita delle macchine virtuali della soluzione VMware di Azure con Microsoft Azure strumenti nativi.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 10e721996b2e3186417a702d2d7473b36b11237f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575478"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gestione del ciclo di vita delle VM della soluzione VMware di Azure

Microsoft Azure strumenti nativi consentono di monitorare e gestire le macchine virtuali (VM) nell'ambiente Azure. Ma consentono anche di monitorare e gestire le macchine virtuali in una soluzione VMware di Azure e nelle macchine virtuali locali. In questa panoramica si esaminerà l'architettura di monitoraggio integrata offerta da Azure e come usare gli strumenti nativi per gestire le macchine virtuali della soluzione VMware di Azure per tutto il ciclo di vita.

## <a name="benefits"></a>Vantaggi

- I servizi nativi di Azure possono essere usati per gestire le macchine virtuali in un ambiente ibrido (Azure, soluzione VMware di Azure e in locale).
- Monitoraggio e visibilità integrati di Azure, della soluzione VMware di Azure e delle macchine virtuali locali.
- Con Gestione aggiornamenti di Azure in automazione di Azure, è possibile gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux. 
- Il Centro sicurezza di Azure offre protezione avanzata dalle minacce, tra cui:
    - Monitoraggio dell'integrità dei file
    - Avvisi di sicurezza non file
    - Valutazione patch del sistema operativo
    - Valutazione delle configurazioni errate della sicurezza
    - Valutazione di Endpoint Protection 
- Distribuisci con facilità il Microsoft Monitoring Agent (MMA) usando Azure ARC per le nuove macchine virtuali. 
- L'area di lavoro Log Analytics in monitoraggio di Azure consente la raccolta di log e la raccolta di contatori delle prestazioni con MMA o estensioni. Raccogliere i dati e i log in un singolo punto e presentare i dati a diversi servizi nativi di Azure. 
- I vantaggi aggiuntivi di monitoraggio di Azure includono: 
    - Monitoraggio trasparente 
    - Migliore visibilità dell'infrastruttura 
    - Notifiche immediate 
    - Risoluzione automatica 
    - Efficienza dei costi 

## <a name="integrated-azure-monitoring-architecture"></a>Architettura di monitoraggio integrata di Azure

Il diagramma seguente illustra l'architettura di monitoraggio integrata per le macchine virtuali della soluzione VMware di Azure.

![Architettura di monitoraggio integrata di Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrazione e distribuzione dei servizi nativi di Azure

**Azure Arc** estende la gestione di Azure a qualsiasi infrastruttura, tra cui la soluzione VMware di Azure, locale o altre piattaforme cloud. Azure ARC può essere distribuito installando un cluster Azure Kubernetes Service (AKS) nell'ambiente della soluzione VMware di Azure. Per altre informazioni, vedere [connettere un cluster Kubernetes abilitato per Azure Arc](../azure-arc/kubernetes/connect-cluster.md).

Le macchine virtuali della soluzione VMware di Azure possono essere monitorate tramite MMA (anche noto come agente Log Analytics o agente Linux OMS). MMA può essere installato automaticamente quando viene eseguito il provisioning delle macchine virtuali tramite flussi di lavoro del ciclo di vita delle VM ARC. È possibile installare MMA anche quando si distribuiscono macchine virtuali da un modello in vCenter; anche in questo caso, con le VM di cui è stato effettuato il provisioning tramite flussi Tutte le VM con provisioning della soluzione VMware di Azure possono quindi installare MMA e inviare i log all'area di lavoro di Azure Log Analytics. Per ulteriori informazioni, vedere [Panoramica di log Analytics Agent](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics area di lavoro** consente la raccolta di log e i contatori delle prestazioni con MMA o estensioni. Per creare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).
- Per aggiungere macchine virtuali Windows all'area di lavoro di log Analytics, vedere [Install log Analytics Agent on Windows computers](../azure-monitor/platform/agent-windows.md).
- Per aggiungere macchine virtuali Linux all'area di lavoro di log Analytics, vedere [Install log Analytics Agent on Linux Computers](../azure-monitor/platform/agent-linux.md).

**Azure gestione aggiornamenti** in automazione di Azure gestisce gli aggiornamenti del sistema operativo per i computer Windows e Linux in un ambiente ibrido. Monitora la conformità delle patch e invia gli avvisi di deviazione delle patch a monitoraggio di Azure per la correzione. Azure Gestione aggiornamenti deve connettersi all'area di lavoro di Log Analytics per usare i dati archiviati per valutare lo stato degli aggiornamenti nelle VM.
- Per aggiungere Log Analytics al Gestione aggiornamenti di Azure, è prima di tutto necessario [creare un account di automazione di Azure](../automation/automation-create-standalone-account.md).
- Per collegare l'area di lavoro Log Analytics all'account di automazione, vedere [area di lavoro log Analytics e account di automazione](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Per abilitare Gestione aggiornamenti di Azure per le macchine virtuali, vedere [abilitare Gestione aggiornamenti da un account di automazione](../automation/update-management/update-mgmt-enable-automation-account.md).
- Dopo aver aggiunto le VM in Azure Gestione aggiornamenti, è possibile [distribuire gli aggiornamenti nelle VM ed esaminare i risultati](../automation/update-management/update-mgmt-deploy-updates.md). 

Il **Centro sicurezza di Azure** offre protezione avanzata dalle minacce tra i carichi di lavoro ibridi nel cloud e in locale. Valuterà la vulnerabilità delle macchine virtuali della soluzione VMware di Azure e genererà avvisi in base alle esigenze. Questi avvisi di sicurezza possono essere trasmessi a monitoraggio di Azure per la risoluzione.
- Il Centro sicurezza di Azure non richiede la distribuzione. Per ulteriori informazioni, vedere l'elenco delle [funzionalità supportate per le macchine virtuali](../security-center/security-center-services.md).
- Per aggiungere macchine virtuali della soluzione VMware di Azure e macchine virtuali non di Azure al centro sicurezza di Azure, vedere [onboarding Windows computers to Azure Security Center](../security-center/quick-onboard-windows-computer.md) e [onboarding Linux computers to Azure Security Center](../security-center/quick-onboard-linux-computer.md).
- Dopo l'aggiunta di macchine virtuali, il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Vengono inoltre forniti suggerimenti nella scheda Panoramica. Per altre informazioni, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).
- È possibile definire i criteri di sicurezza nel centro sicurezza di Azure. Per informazioni sulla configurazione dei criteri di sicurezza, vedere [utilizzo dei criteri di sicurezza](../security-center/tutorial-security-policy.md).

**Monitoraggio di Azure** è una soluzione completa per la raccolta, l'analisi e la gestione dei dati di telemetria dagli ambienti cloud e locali. Non richiede alcuna distribuzione.
- Monitoraggio di Azure consente di raccogliere dati da origini diverse per il monitoraggio e l'analisi. Per altre informazioni, vedere [origini dei dati di monitoraggio per monitoraggio di Azure](../azure-monitor/platform/data-sources.md).
- È anche possibile raccogliere tipi diversi di dati per l'analisi, la visualizzazione e gli avvisi. Per altre informazioni, vedere [piattaforma dati di monitoraggio di Azure](../azure-monitor/platform/data-platform.md).
- Per configurare monitoraggio di Azure con l'area di lavoro Log Analytics, vedere [configurare l'area di lavoro log Analytics per monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-configure-workspace.md).
- È possibile creare regole di avviso per identificare i problemi nell'ambiente, ad esempio l'utilizzo elevato delle risorse, le patch mancanti, lo spazio su disco insufficiente e l'heartbeat delle macchine virtuali. È anche possibile impostare una risposta automatica per gli eventi rilevati inviando un avviso agli strumenti di gestione dei servizi IT (ITSM). La notifica di rilevamento degli avvisi può essere inviata anche tramite posta elettronica. Per creare regole di questo tipo, vedere:
    - [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md).
    - [Creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure](../azure-monitor/platform/alerts-log.md).
    - [Regole di azione](../azure-monitor/platform/alerts-action-rules.md) per impostare le azioni e le notifiche automatiche.
    - [Connettere Azure agli strumenti ITSM usando IT Service Management Connector](../azure-monitor/platform/itsmc-overview.md).

**Piattaforma distribuita come servizio (PaaS) di Azure** è un ambiente di sviluppo e distribuzione nel cloud, con risorse che consentono di distribuire applicazioni basate su cloud. Ad esempio, è possibile integrare il database SQL di Azure con le macchine virtuali della soluzione VMware di Azure. Gli avvisi SQL possono quindi essere correlati con gli avvisi della macchina virtuale della soluzione VMware di Azure. Ad esempio, supponiamo che la parte del database SQL dell'applicazione si trovi all'interno della PAAS di Azure e che il livello applicazione Web della stessa applicazione sia ospitato nelle VM della soluzione VMware di Azure. Gli avvisi del database possono quindi essere correlati con gli avvisi dell'applicazione Web. La risoluzione dei problemi è semplificata grazie a una singola visibilità integrata di Azure, alla soluzione VMware di Azure e alle macchine virtuali locali.
