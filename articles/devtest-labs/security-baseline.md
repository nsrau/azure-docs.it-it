---
title: Baseline della sicurezza di Azure per Azure DevTest Labs
description: Baseline della sicurezza di Azure per Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098514"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Baseline della sicurezza di Azure per Azure DevTest Labs

La linea di base di sicurezza di Azure per Azure DevTest Labs contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate
**Linee guida:** Microsoft gestisce le origini temporali per le risorse di Azure. Tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

Vedere l'articolo seguente per informazioni sulla configurazione della sincronizzazione dell'ora per le risorse di calcolo di Azure: [sincronizzazione dell'ora per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

Per altre informazioni, vedere [creare le impostazioni di diagnostica per inviare i log e le metriche della piattaforma a destinazioni diverse](../azure-monitor/platform/diagnostic-settings.md).

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

Per altre informazioni, vedere [creare le impostazioni di diagnostica per inviare i log e le metriche della piattaforma a destinazioni diverse](../azure-monitor/platform/diagnostic-settings.md).

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi
**Linee guida:** Azure DevTest Labs le macchine virtuali vengono create e di proprietà del cliente. Quindi, è responsabilità dell'organizzazione monitorarlo. È possibile usare il Centro sicurezza di Azure per monitorare il sistema operativo di calcolo. I dati raccolti dal centro sicurezza dal sistema operativo includono il tipo di sistema operativo e la versione, il sistema operativo (registri eventi di Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. L'agente di Log Analytics raccoglie anche i file di dump di arresto anomalo del sistema.

Per altre informazioni, vedere gli articoli seguenti: 

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)
- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza
***Linee guida:** In monitoraggio di Azure impostare il periodo di conservazione del log per le aree di lavoro Log Analytics associate alle istanze di Azure DevTest Labs in base alle normative di conformità dell'organizzazione.

Per altre informazioni, vedere l'articolo seguente: [come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività che potrebbero essere stati raccolti per Azure DevTest Labs.

Per altre informazioni, vedere gli articoli seguenti:

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale
**Linee guida:** Usare l'area di lavoro di Azure Log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale nei registri di sicurezza e negli eventi correlati al Azure DevTest Labs.

Per altre informazioni, vedere l'articolo seguente: [come ricevere un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware
**Linee guida:** Non applicabile. Azure DevTest Labs non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS
**Linee guida:** Non applicabile. Azure DevTest Labs non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando
**Linee guida:** Azure DevTest Labs crea computer di calcolo di Azure che sono di proprietà e gestiti dal cliente. Usare Microsoft Monitoring Agent in tutte le macchine virtuali Windows di Azure supportate per registrare l'evento di creazione del processo e il campo CommandLine. Per le macchine virtuali Linux di Azure supportate, è possibile configurare manualmente la registrazione della console in base ai singoli nodi e usare syslog per archiviare i dati. Usare anche l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati registrati dalle macchine virtuali di Azure.

- [Raccolta dati nel Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Come eseguire query personalizzate in monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)
- [Origini dati Syslog in Monitoraggio di Azure](../azure-monitor/platform/data-sources-syslog.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente:

- [Avvisi di sicurezza per gli ambienti in Azure DevTest Labs](environment-security-alerts.md)