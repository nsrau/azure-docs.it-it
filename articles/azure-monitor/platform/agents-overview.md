---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti disponibili di Azure che supportano il monitoraggio delle macchine virtuali ospitate in Azure o in un ambiente ibrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2020
ms.openlocfilehash: 9c453219638b9586d4ad2cc2a89311b1f2bdeefe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450103"
---
# <a name="overview-of-azure-monitor-agents"></a>Panoramica degli agenti di monitoraggio di Azure

Per le macchine virtuali e altre risorse di calcolo è necessario che un agente raccolga i dati di monitoraggio necessari per misurare le prestazioni e la disponibilità del sistema operativo guest e dei carichi di lavoro. Questo articolo descrive gli agenti usati da monitoraggio di Azure e consente di determinare quale è necessario soddisfare i requisiti per un ambiente specifico.

> [!NOTE]
> Monitoraggio di Azure dispone attualmente di più agenti a causa del consolidamento recente di monitoraggio di Azure e Log Analytics. Sebbene sia possibile che si verifichi una sovrapposizione delle funzionalità, ognuna presenta funzionalità univoche. A seconda dei requisiti, potrebbe essere necessario disporre di uno o più agenti nelle macchine virtuali. 

È possibile che si disponga di un set specifico di requisiti che non possono essere soddisfatti completamente da un singolo agente per una determinata macchina virtuale. Ad esempio, si consiglia di usare gli avvisi delle metriche che richiedono l'estensione diagnostica di Azure, ma anche di sfruttare le funzionalità di Monitoraggio di Azure per le macchine virtuali che richiedono l'agente Log Analytics e Dependency Agent. In casi come questo, è possibile usare più agenti e si tratta di uno scenario comune per i clienti che necessitano di funzionalità.

## <a name="summary-of-agents"></a>Riepilogo degli agenti

Le tabelle seguenti forniscono un rapido confronto degli agenti di monitoraggio di Azure per Windows e Linux. Ulteriori dettagli su ciascuno di essi sono disponibili nella sezione seguente.

> [!NOTE]
> L'agente di monitoraggio di Azure è attualmente in versione di anteprima con funzionalità limitate. Questa tabella verrà aggiornata 

### <a name="windows-agents"></a>Agenti di Windows

| | Agente di monitoraggio di Azure (anteprima) | Diagnostica<br>estensione (WAD) | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|:---|
| **Ambienti supportati** | Azure<br>Altro cloud (Azure Arc)<br>Locale (Azure Arc)  | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale | 
| **Requisiti dell'agente**  | Nessuno | Nessuno | Nessuno | Richiede Log Analytics Agent |
| **Dati raccolti** | Log eventi<br>Prestazioni | Log eventi<br>eventi ETW<br>Prestazioni<br>Log basati su file<br>Log di IIS<br>Log delle app .NET<br>Dump di arresto anomalo<br>Log di diagnostica agente | Log eventi<br>Prestazioni<br>Log basati su file<br>Log di IIS<br>Informazioni dettagliate e soluzioni<br>Altri servizi | Dipendenze dei processi<br>Metriche della connessione di rete |
| **Dati inviati a** | Log di Monitoraggio di Azure<br>Metriche di Monitoraggio di Azure | Archiviazione di Azure<br>Metriche di Monitoraggio di Azure<br>Hub eventi | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure<br>(tramite agente Log Analytics) |
| **Servizi e**<br>**funzionalità**<br>**supportato** | Log Analytics<br>Esplora metriche | Esplora metriche | Monitoraggio di Azure per le macchine virtuali<br>Log Analytics<br>Automazione di Azure<br>Centro sicurezza di Azure<br>Azure Sentinel | Monitoraggio di Azure per le macchine virtuali<br>Elenco dei servizi |

### <a name="linux-agents"></a>Agenti Linux

| | Agente di monitoraggio di Azure (anteprima) | Diagnostica<br>estensione (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|:---|:---|
| **Ambienti supportati** | Azure<br>Altro cloud (Azure Arc)<br>Locale (Arc Arc) | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale |
| **Requisiti dell'agente**  | Nessuno | Nessuno | Nessuno | Nessuno | Richiede Log Analytics Agent |
| **Dati raccolti** | syslog<br>Prestazioni | syslog<br>Prestazioni | Prestazioni | syslog<br>Prestazioni| Dipendenze dei processi<br>Metriche della connessione di rete |
| **Dati inviati a** | Log di Monitoraggio di Azure<br>Metriche di Monitoraggio di Azure | Archiviazione di Azure<br>Hub eventi | Metriche di Monitoraggio di Azure | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure<br>(tramite agente Log Analytics) |
| **Servizi e**<br>**funzionalità**<br>**supportato** | Log Analytics<br>Esplora metriche | | Esplora metriche | Monitoraggio di Azure per le macchine virtuali<br>Log Analytics<br>Automazione di Azure<br>Centro sicurezza di Azure<br>Azure Sentinel | Monitoraggio di Azure per le macchine virtuali<br>Elenco dei servizi |


## <a name="azure-monitor-agent-preview"></a>Agente di monitoraggio di Azure (anteprima)
L' [agente di monitoraggio di Azure](azure-monitor-agent-overview.md) è attualmente in versione di anteprima e sostituirà l'agente di log Analytics e l'agente di Telegraf per le macchine virtuali Windows e Linux. Può inviare dati ai log di monitoraggio di Azure e alle metriche di monitoraggio di Azure e USA [le regole di raccolta dati (DCR)](data-collection-rule-overview.md) che forniscono un metodo più scalabile per la configurazione della raccolta dati e delle destinazioni per ogni agente.

Usare l'agente di monitoraggio di Azure se è necessario:

- Raccogliere i log e le metriche Guest da qualsiasi macchina virtuale in Azure, in altri cloud o in locale. Azure Arc è necessario per le macchine virtuali esterne ad Azure. 
- Inviare dati ai log di monitoraggio di Azure e alle metriche di monitoraggio di Azure per l'analisi con monitoraggio di Azure. 
- Inviare i dati ad archiviazione di Azure per l'archiviazione.
- Inviare dati a strumenti di terze parti usando [Hub eventi di Azure](diagnostics-extension-stream-event-hubs.md).
- Gestire la sicurezza delle macchine virtuali usando il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)  o [Azure Sentinel](../../sentinel/overview.md). (Non disponibile in anteprima).

Le limitazioni dell'agente di monitoraggio di Azure includono:

- Attualmente disponibile in anteprima pubblica. Vedere le [limitazioni correnti](azure-monitor-agent-overview.md#current-limitations) per un elenco di limitazioni durante l'anteprima pubblica.

## <a name="log-analytics-agent"></a>Agente di Log Analytics

L' [agente di log Analytics](log-analytics-agent.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali in Azure, in altri provider di servizi cloud e in locale. Invia i dati a un'area di lavoro Log Analytics. L'agente di Log Analytics è lo stesso agente usato da System Center Operations Manager ed è possibile usare i computer multihome Agent per comunicare simultaneamente con il gruppo di gestione e con monitoraggio di Azure. Questo agente è richiesto anche da alcune informazioni dettagliate in monitoraggio di Azure e in altri servizi in Azure.


> [!NOTE]
> L'agente di Log Analytics per Windows è spesso indicato come Microsoft Monitoring Agent (MMA). L'agente di Log Analytics per Linux è spesso definito agente OMS.



Usare l'agente di Log Analytics se è necessario:

* Raccogliere i log e i dati sulle prestazioni da macchine virtuali o fisiche all'interno o all'esterno di Azure. 
* Inviare dati a un'area di lavoro di Log Analytics per sfruttare le funzionalità supportate dai [log di monitoraggio di Azure](data-platform-logs.md) , ad esempio le query di [log](../log-query/log-query-overview.md).
* Usare [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) , che consente di monitorare le macchine virtuali su larga scala e monitora i processi e le dipendenze da altre risorse e processi esterni.  
* Gestire la sicurezza delle macchine virtuali usando il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)  o [Azure Sentinel](../../sentinel/overview.md).
* Usare la [gestione degli aggiornamenti di automazione](../../automation/update-management/overview.md)di Azure, la [configurazione dello stato di automazione](../../automation/automation-dsc-overview.md)di Azure o [rilevamento modifiche e l'inventario di automazione di Azure](../../automation/change-tracking/overview.md) per offrire una gestione completa delle VM di Azure
* Utilizzare diverse [soluzioni](../monitor-reference.md#insights-and-core-solutions) per monitorare un servizio o un'applicazione specifica.

Le limitazioni dell'agente Log Analytics includono:

- Non è possibile inviare dati a metriche di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.
- Difficile configurare definizioni di monitoraggio univoche per singoli agenti.
- Difficile da gestire su larga scala poiché ogni macchina virtuale ha una configurazione univoca.

## <a name="azure-diagnostics-extension"></a>Estensione Diagnostica di Azure

L' [estensione diagnostica di Azure](diagnostics-extension-overview.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Raccoglie principalmente i dati in archiviazione di Azure, ma consente anche di definire i sink di dati per inviare dati ad altre destinazioni, ad esempio le metriche di monitoraggio di Azure e hub eventi di Azure.

Usare l'estensione diagnostica di Azure se è necessario:

- Inviare i dati ad archiviazione di Azure per l'archiviazione o analizzarli con strumenti come [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Inviare dati alle [metriche di monitoraggio di Azure](data-platform-metrics.md) per analizzarli con [Esplora metriche](metrics-getting-started.md) e per sfruttare i vantaggi offerti dalle funzionalità, ad esempio gli [avvisi delle metriche](./alerts-metric-overview.md) near Real Time e la [scalabilità](autoscale-overview.md) automatica (solo Windows).
- Inviare dati a strumenti di terze parti usando [Hub eventi di Azure](diagnostics-extension-stream-event-hubs.md).
- Raccogliere la [diagnostica di avvio](../../virtual-machines/troubleshooting/boot-diagnostics.md) per esaminare i problemi di avvio della macchina virtuale.

Le limitazioni dell'estensione diagnostica di Azure includono:

- Può essere usato solo con le risorse di Azure.
- Possibilità limitata di inviare dati ai log di monitoraggio di Azure.

## <a name="telegraf-agent"></a>Agente Telegraf

L' [agente Telegraf InfluxData](collect-custom-metrics-linux-telegraf.md) viene usato per raccogliere i dati sulle prestazioni dai computer Linux alle metriche di monitoraggio di Azure.

Usare l'agente Telegraf se è necessario:

* Inviare dati alle [metriche di monitoraggio di Azure](data-platform-metrics.md) per analizzarli con [Esplora metriche](metrics-getting-started.md) e per sfruttare le funzionalità, ad esempio gli [avvisi delle metriche](./alerts-metric-overview.md) near Real Time e la [scalabilità](autoscale-overview.md) automatica (solo Linux). 



## <a name="dependency-agent"></a>Dependency Agent

Dependency Agent raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze del processo esterno. 

Usare Dependency Agent se è necessario:

* Usare la funzione map [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) o la soluzione [mapping dei servizi](../insights/service-map.md) .

Quando si usa Dependency Agent, tenere presente quanto segue:

- Dependency Agent richiede l'installazione dell'agente Log Analytics nella stessa macchina virtuale.
- Nelle macchine virtuali Linux è necessario installare l'agente Log Analytics prima dell'estensione diagnostica di Azure.

## <a name="virtual-machine-extensions"></a>Estensioni macchina virtuale

L'estensione Log Analytics per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) installa l'agente di log Analytics in macchine virtuali di Azure. L'estensione di dipendenza di monitoraggio di Azure per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installa Dependency Agent in macchine virtuali di Azure. Si tratta degli stessi agenti descritti sopra, ma consentono di gestirli tramite le [estensioni delle macchine virtuali](../../virtual-machines/extensions/overview.md). È consigliabile utilizzare le estensioni per installare e gestire gli agenti quando possibile.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le tabelle seguenti elencano i sistemi operativi supportati dagli agenti di monitoraggio di Azure. Vedere la documentazione relativa a ogni agente per considerazioni univoche e per il processo di installazione. Vedere la documentazione di Telegraf per i sistemi operativi supportati. Si presuppone che tutti i sistemi operativi siano x64. x86 non è supportato per alcun sistema operativo.

### <a name="windows"></a>Windows

| Sistema operativo | Agente di Monitoraggio di Azure | Agente di Log Analytics | Dependency Agent | Estensione di Diagnostica | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(incluse la funzionalità multisessione) e Pro<br>(Solo scenari server)  | X | X | X | X |
| Windows 8 Enterprise e Pro<br>(Solo scenari server)  |   | X | X |   |
| Windows 7 SP1<br>(Solo scenari server)                 |   | X | X |   |


### <a name="linux"></a>Linux

| Sistema operativo | Agente di Monitoraggio di Azure | Agente di Log Analytics | Dependency Agent | Estensione di Diagnostica | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7,8                                         | X | X | X | X |
| CentOS Linux 7,6                                         | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5 +                                        |   | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1+                                           |   |   |   | X |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4+                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 +                     |   | X | X | X |
| SUSE Linux Enterprise Server 15,1                        |   | X |   |   |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20.04 LTS                                         |   | X |   |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14,04 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Supporto del kernel Linux di Dependency Agent
Poiché Dependency Agent funziona a livello di kernel, il supporto dipende anche dalla versione del kernel. La tabella seguente elenca la versione principale e secondaria del sistema operativo Linux e le versioni del kernel supportate per Dependency Agent.

| Distribuzione | Versione sistema operativo | Versione del kernel |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 18,04   | 5.3.0-1020<br>5,0 (include il kernel ottimizzato per Azure)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15. * |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4,12. * (include il kernel ottimizzato per Azure) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ciascuno degli agenti, fare quanto segue:

- [Panoramica dell'agente di Log Analytics](log-analytics-agent.md)
- [Panoramica dell'estensione Diagnostica di Azure](diagnostics-extension-overview.md)
- [Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
