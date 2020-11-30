---
title: Gestione del ciclo di vita delle VM della soluzione VMware di Azure
description: Informazioni su come gestire tutti gli aspetti del ciclo di vita delle macchine virtuali della soluzione VMware di Azure con Microsoft Azure strumenti nativi.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: ff1032900fa936895f3adfcb0d8a872f24948aca
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326812"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gestione del ciclo di vita delle VM della soluzione VMware di Azure

Microsoft Azure strumenti nativi consentono di monitorare e gestire le macchine virtuali (VM) nell'ambiente Azure. Ma consentono anche di monitorare e gestire le macchine virtuali in una soluzione VMware di Azure e nelle macchine virtuali locali. In questa panoramica si esaminerà l'architettura di monitoraggio integrata offerta da Azure e come usare gli strumenti nativi per gestire le macchine virtuali della soluzione VMware di Azure per tutto il ciclo di vita.

## <a name="benefits"></a>Vantaggi

- I servizi nativi di Azure possono essere usati per gestire le macchine virtuali in un ambiente ibrido (Azure, soluzione VMware di Azure e in locale).
- Monitoraggio e visibilità integrati di Azure, della soluzione VMware di Azure e delle macchine virtuali locali.
- Con Gestione aggiornamenti di Azure in automazione di Azure, è possibile gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux. 
- Il Centro sicurezza di Azure offre protezione avanzata dalle minacce, tra cui:
    - Monitoraggio dell'integrità dei file
    - Avvisi di sicurezza senza file
    - Valutazione patch del sistema operativo
    - Valutazione degli errori di configurazione della sicurezza
    - Valutazione della protezione endpoint 
- Distribuire facilmente l'agente di Log Analytics usando i server abilitati per Azure Arc supporto dell'estensione VM per macchine virtuali nuove ed esistenti. 
- L'area di lavoro Log Analytics in monitoraggio di Azure consente la raccolta di log e la raccolta dei contatori delle prestazioni usando l'agente Log Analytics o le estensioni. Raccogliere i dati e i log in un singolo punto e presentare i dati a diversi servizi nativi di Azure. 
- I vantaggi aggiuntivi di monitoraggio di Azure includono: 
    - Monitoraggio trasparente 
    - Migliore visibilità dell'infrastruttura 
    - Notifiche immediate 
    - Risoluzione automatica 
    - Efficienza dei costi 

## <a name="integrated-azure-monitoring-architecture"></a>Architettura di monitoraggio integrata di Azure

Il diagramma seguente illustra l'architettura di monitoraggio integrata per le macchine virtuali della soluzione VMware di Azure.

![Architettura di monitoraggio integrata di Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Prima di iniziare

Se non si ha familiarità con Azure o non si ha familiarità con i servizi citati in precedenza, vedere gli articoli seguenti:

- [Panoramica dell'autenticazione di account di Automazione](../automation/automation-security-overview.md)
- [Progettazione della distribuzione dei log di monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md) e di monitoraggio di [Azure](../azure-monitor/overview.md)
- [Pianificazione](../security-center/security-center-planning-and-operations-guide.md) e [piattaforme supportate](../security-center/security-center-os-coverage.md) per il Centro sicurezza di Azure
- [Abilitare Monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-enable-overview.md)
- [Che cosa sono i server abilitati per Azure Arc?](../azure-arc/servers/overview.md) e [che cos'è abilitato per Azure Arc Kubernetes?](../azure-arc/kubernetes/overview.md)
- [Panoramica di Gestione aggiornamenti](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrazione e distribuzione dei servizi nativi di Azure

### <a name="enable-azure-update-management"></a>Abilita Gestione aggiornamenti di Azure

Azure Gestione aggiornamenti in automazione di Azure gestisce gli aggiornamenti del sistema operativo per i computer Windows e Linux in un ambiente ibrido. Monitora la conformità delle patch e invia gli avvisi di deviazione delle patch a monitoraggio di Azure per la correzione. Azure Gestione aggiornamenti deve connettersi all'area di lavoro di Log Analytics per usare i dati archiviati per valutare lo stato degli aggiornamenti nelle VM.

1.  Prima di poter aggiungere Log Analytics al Gestione aggiornamenti di Azure, è prima di tutto necessario [creare un account di automazione di Azure](../automation/automation-create-standalone-account.md). Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](../automation/quickstart-create-automation-account-template.md).

2. **Log Analytics area di lavoro** consente la raccolta di log e i contatori delle prestazioni utilizzando l'agente log Analytics o le estensioni. Per creare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md). Se si preferisce, è anche possibile creare un'area di lavoro tramite l' [interfaccia](../azure-monitor/learn/quick-create-workspace-cli.md)della riga di comando, [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)o [Azure Resource Manager modello](../azure-monitor/samples/resource-manager-workspace.md).

3. Per abilitare Gestione aggiornamenti di Azure per le macchine virtuali, vedere [abilitare Gestione aggiornamenti da un account di automazione](../automation/update-management/enable-from-automation-account.md). Nel processo si collegherà l'area di lavoro Log Analytics con l'account di automazione. 
 
4. Dopo aver aggiunto le VM in Azure Gestione aggiornamenti, è possibile [distribuire gli aggiornamenti nelle VM ed esaminare i risultati](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Abilitare il Centro sicurezza di Azure

Il Centro sicurezza di Azure offre protezione avanzata dalle minacce tra i carichi di lavoro ibridi nel cloud e in locale. Valuterà la vulnerabilità delle macchine virtuali della soluzione VMware di Azure e genererà avvisi in base alle esigenze. Questi avvisi di sicurezza possono essere trasmessi a monitoraggio di Azure per la risoluzione.

Il Centro sicurezza di Azure non richiede la distribuzione. Per ulteriori informazioni, vedere l'elenco delle [funzionalità supportate per le macchine virtuali](../security-center/security-center-services.md).

1. Per aggiungere macchine virtuali della soluzione VMware di Azure e macchine virtuali non di Azure al centro sicurezza, vedere [Guida introduttiva: configurazione del Centro sicurezza di Azure](../security-center/security-center-get-started.md). 

2. Dopo l'aggiunta di VM o macchine virtuali della soluzione VMware di Azure da un ambiente non Azure, abilitare Azure Defender nel centro sicurezza. Il Centro sicurezza valuterà le macchine virtuali per individuare potenziali problemi di sicurezza. Vengono inoltre forniti suggerimenti nella scheda Panoramica. Per altre informazioni, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).

3. È possibile definire i criteri di sicurezza nel centro sicurezza di Azure. Per informazioni sulla configurazione dei criteri di sicurezza, vedere [utilizzo dei criteri di sicurezza](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Caricare le macchine virtuali nei server abilitati per Azure Arc

Azure Arc estende la gestione di Azure a qualsiasi infrastruttura, tra cui la soluzione VMware di Azure, locale o altre piattaforme cloud.

- Vedere [connettere macchine ibride ad Azure su larga scala](../azure-arc/servers/onboard-service-principal.md) per abilitare i server abilitati per Azure Arc per più macchine virtuali Windows o Linux.

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Caricare cluster Kubernetes ibridi con Arc abilitato Kubernetes

È possibile aggiungere un cluster Kubernetes ospitato nell'ambiente della soluzione VMware di Azure con Azure Arc Enabled Kubernetes. 

- Vedere [creare un'entità servizio di onboarding abilitata per Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Distribuire l'agente di Log Analytics

Le macchine virtuali della soluzione VMware di Azure possono essere monitorate tramite l'agente di Log Analytics (noto anche come Microsoft Monitoring Agent (MMA) o agente Linux OMS). È già stata creata un'area di lavoro Log Analytics durante l'abilitazione di automazione di Azure Gestione aggiornamenti.

- Distribuire l'agente di Log Analytics usando il [supporto dell'estensione VM per i server abilitati per Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Abilita monitoraggio di Azure

Monitoraggio di Azure è una soluzione completa per la raccolta, l'analisi e la gestione dei dati di telemetria dagli ambienti cloud e locali. Non richiede alcuna distribuzione. Con monitoraggio di Azure è possibile monitorare le prestazioni del sistema operativo guest e individuare ed eseguire il mapping delle dipendenze delle applicazioni per la soluzione VMware di Azure o le macchine virtuali locali.

- Monitoraggio di Azure consente di raccogliere dati da origini diverse per il monitoraggio e l'analisi. Per altre informazioni, vedere [origini dei dati di monitoraggio per monitoraggio di Azure](../azure-monitor/platform/data-sources.md).

- Raccogliere tipi diversi di dati per l'analisi, la visualizzazione e gli avvisi. Per altre informazioni, vedere [piattaforma dati di monitoraggio di Azure](../azure-monitor/platform/data-platform.md).

- Per configurare monitoraggio di Azure con l'area di lavoro Log Analytics, vedere [configurare l'area di lavoro log Analytics per monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-configure-workspace.md).

- È possibile creare regole di avviso per identificare i problemi nell'ambiente, ad esempio l'utilizzo elevato delle risorse, le patch mancanti, lo spazio su disco insufficiente e l'heartbeat delle macchine virtuali. È anche possibile impostare una risposta automatica per gli eventi rilevati inviando un avviso agli strumenti di gestione dei servizi IT (ITSM). La notifica di rilevamento degli avvisi può essere inviata anche tramite posta elettronica. Per creare regole di questo tipo, vedere:
    - [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md).
    - [Creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure](../azure-monitor/platform/alerts-log.md).
    - [Regole di azione](../azure-monitor/platform/alerts-action-rules.md) per impostare le azioni e le notifiche automatiche.
    - [Connettere Azure agli strumenti ITSM usando IT Service Management Connector](../azure-monitor/platform/itsmc-overview.md).