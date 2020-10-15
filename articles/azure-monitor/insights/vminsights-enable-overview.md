---
title: Abilitare Monitoraggio di Azure per le macchine virtuali
description: Informazioni su come distribuire e configurare Monitoraggio di Azure per le macchine virtuali. Individuare i requisiti di sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.openlocfilehash: 4041b824d099edbefcc45c68163257ca69ddf7de
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995689"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Abilitare Monitoraggio di Azure per le macchine virtuali

In questo articolo viene fornita una panoramica delle opzioni disponibili per consentire a Monitoraggio di Azure per le macchine virtuali di monitorare l'integrità e le prestazioni dei seguenti elementi:

- Macchine virtuali di Azure 
- Set di scalabilità di macchine virtuali di Azure
- Macchine virtuali ibride connesse ad Azure Arc
- Macchine virtuali locali
- Macchine virtuali ospitate in un altro ambiente cloud.  

Per configurare Monitoraggio di Azure per le macchine virtuali:

* Abilitare una singola macchina virtuale di Azure, Azure VMSS o Azure Arc selezionando **Insights** direttamente dal menu nel portale di Azure.
* Abilitare più macchine virtuali di Azure, Azure VMSS o macchine Azure ARC usando criteri di Azure. Questo metodo garantisce che nelle VM nuove e esistenti e nei set di scalabilità siano installate e configurate correttamente le dipendenze necessarie. Le macchine virtuali non conformi e i set di scalabilità vengono segnalati, pertanto è possibile decidere se abilitarli e risolverli.
* Abilitare più macchine virtuali di Azure, macchine virtuali di Azure Arc, Azure VMSS o macchine di Azure Arc in una sottoscrizione o un gruppo di risorse specifico usando PowerShell.
* Abilitare Monitoraggio di Azure per le macchine virtuali per monitorare le macchine virtuali o i computer fisici ospitati nella rete aziendale o in un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver compreso quanto illustrato nelle sezioni seguenti. 

>[!NOTE]
>Le seguenti informazioni descritte in questa sezione sono applicabili anche alla [soluzione mapping dei servizi](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Monitoraggio di Azure per le macchine virtuali supporta un'area di lavoro Log Analytics nelle aree seguenti:

- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti orientali
- Stati Uniti Orientali 2
- Stati Uniti centrali
- Stati Uniti centro-settentrionali
- US Gov AZ
- US Gov va
- Canada centrale
- Regno Unito meridionale
- Europa settentrionale
- Europa occidentale
- Asia orientale
- Asia sud-orientale
- India centrale
- Giappone orientale
- Australia orientale
- Australia sud-orientale

>[!NOTE]
>È possibile monitorare le macchine virtuali di Azure in qualsiasi area. Le macchine virtuali non sono limitate alle aree supportate dall'area di lavoro Log Analytics.
>

Se non si dispone di un'area di lavoro Log Analytics, è possibile crearne una utilizzando una delle risorse seguenti:
* [Interfaccia della riga di comando di Azure](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Monitoraggio di Azure per le macchine virtuali supporta tutti i sistemi operativi che supportano Log Analytics Agent e Dependency Agent. Per un elenco completo, vedere [Panoramica degli agenti di monitoraggio di Azure ](../platform/agents-overview.md#supported-operating-systems) .

Vedere l'elenco seguente di considerazioni sul supporto Linux di Dependency Agent che supporta Monitoraggio di Azure per le macchine virtuali:

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
- I kernel personalizzati, incluse le ricompilazioni dei kernel standard, non sono supportati.
- Per le distribuzioni Debian diverse dalla versione 9,4, la funzionalità mappa non è supportata e la funzionalità prestazioni è disponibile solo dal menu monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.
- Il kernel CentOSPlus è supportato.
- È necessario applicare patch al kernel Linux per la vulnerabilità di Spectre. Per ulteriori informazioni, consultare il fornitore della distribuzione Linux.



## <a name="supported-azure-arc-machines"></a>Computer Azure Arc supportati
Monitoraggio di Azure per le macchine virtuali è disponibile per i server abilitati per Azure Arc in aree in cui è disponibile il servizio estensione ARC. È necessario eseguire la versione 0,9 o successiva dell'agente Arc.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | Insieme all' [agente log Analytics per Windows](../platform/log-analytics-agent.md), gli agenti Windows necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](../platform/agents-overview.md#supported-operating-systems). |
| Agenti Linux | Sì | Insieme all' [agente log Analytics per Linux](../platform/log-analytics-agent.md), gli agenti Linux necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

## <a name="agents"></a>Agenti
Monitoraggio di Azure per le macchine virtuali richiede l'installazione dei due agenti seguenti in ogni macchina virtuale o set di scalabilità di macchine virtuali da monitorare. Installare questi agenti e connetterli all'area di lavoro è l'unico requisito per eseguire l'onboarding della risorsa.

- [Agente log Analytics](../platform/log-analytics-agent.md). Raccoglie gli eventi e i dati sulle prestazioni dalla macchina virtuale o dal set di scalabilità di macchine virtuali e li recapita all'area di lavoro Log Analytics. I metodi di distribuzione per l'agente di Log Analytics nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency Agent. Raccoglie i dati individuati relativi ai processi in esecuzione nella macchina virtuale e alle dipendenze del processo esterno, che viene utilizzata dalla [funzionalità mappa in monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). Dependency Agent si basa sull'agente di Log Analytics per recapitare i dati a monitoraggio di Azure. I metodi di distribuzione per l'agente di dipendenza nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> L'agente di Log Analytics è lo stesso agente usato da System Center Operations Manager. Monitoraggio di Azure per le macchine virtuali possibile monitorare gli agenti monitorati anche da Operations Manager se sono connessi direttamente e si installa l'agente di dipendenza su di essi. Gli agenti connessi a monitoraggio di Azure tramite una [connessione al gruppo di gestione](../tform/../platform/om-agents.md) non possono essere monitorati da monitoraggio di Azure per le macchine virtuali.

Di seguito sono riportati diversi metodi per la distribuzione di questi agenti. 

| Metodo | Descrizione |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Installare entrambi gli agenti in un'unica macchina virtuale, un set di scalabilità di macchine virtuali o macchine virtuali ibride connesse ad Azure Arc. |
| [Modelli di Gestione risorse](vminsights-enable-powershell.md) | Installare entrambi gli agenti usando uno dei metodi supportati per distribuire un modello di Gestione risorse, ad esempio l'interfaccia della riga di comando e PowerShell. |
| [Criteri di Azure](./vminsights-enable-policy.md) | Assegnare ad Azure Policy Initiative per installare automaticamente gli agenti quando viene creata una macchina virtuale o un set di scalabilità di macchine virtuali. |
| [Installazione manuale](./vminsights-enable-hybrid.md) | Installare gli agenti nel sistema operativo guest nei computer ospitati all'esterno di Azure, inclusi nel Data Center o in altri ambienti cloud. |




## <a name="management-packs"></a>Management Pack
Quando un'area di lavoro Log Analytics è configurata per Monitoraggio di Azure per le macchine virtuali, due Management Pack vengono trasmessi a tutti i computer Windows connessi a tale area di lavoro. I Management Pack sono denominati *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* e *Microsoft. IntelligencePacks. VMInsights* e vengono scritti in *%programmi%\Microsoft Monitoring Agent\Agent\Health Service state\management Packs Packs \* . 

L'origine dati utilizzata dal Management Pack *ApplicationDependencyMonitor* è **% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. L'origine dati utilizzata dal *VMInsights* Management Pack è *% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio. 

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, la funzionalità map include i dati sulla configurazione del software. I dati forniscono informazioni come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di monitoraggio delle prestazioni, vedere [visualizzare le prestazioni monitoraggio di Azure per le macchine virtuali](vminsights-performance.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).