---
title: Panoramica di Monitoraggio di Azure per macchine virtualiEnable Azure Monitor for VMs overview
description: Informazioni su come distribuire e configurare Monitoraggio di Azure per le macchine virtuali. Scopri i requisiti di sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2020
ms.openlocfilehash: 26ed33e967aff1714d2a6fb174eab623e71534c2
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382733"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Panoramica di Monitoraggio di Azure per macchine virtualiEnable Azure Monitor for VMs overview

Questo articolo offre una panoramica delle opzioni disponibili per abilitare Monitoraggio di Azure per le macchine virtuali nelle macchine virtuali per il monitoraggio dell'integrità e delle prestazioni. Individuare le dipendenze delle applicazioni eseguite in macchine virtuali di Azure (VM) e set di scalabilità di macchine virtuali, macchine virtuali locali o macchine virtuali ospitate in un altro ambiente cloud.  

Per configurare Monitoraggio di Azure per le macchine virtuali:To set up Azure Monitor for VMs:

* Abilitare una singola macchina virtuale di Azure o un set di scalabilità di macchine virtuali selezionando **Informazioni dettagliate** direttamente dalla macchina virtuale o dal set di scalabilità della macchina virtuale.
* Abilitare due o più macchine virtuali di Azure e set di scalabilità di macchine virtuali usando Criteri di Azure.Enable two or more Azure VMs and virtual machine scale sets by using Azure Policy. Questo metodo garantisce che nelle macchine virtuali e nei set di scalabilità esistenti e nuove le dipendenze necessarie vengano installate e configurate correttamente. Vengono segnalate macchine virtuali e set di scalabilità non conformi, pertanto è possibile decidere se abilitarle e corremetterle in modo da corremetterle in modo che possano essere corretti.
* Abilitare due o più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.
* Abilitare Monitoraggio di Azure per le macchine virtuali per monitorare le macchine virtuali o i computer fisici ospitati nella rete aziendale o in un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver compreso quanto illustrato nelle sezioni seguenti. 

>[!NOTE]
>Le informazioni seguenti descritte in questa sezione sono applicabili anche alla [soluzione Mappa dei](service-map.md)servizi.  

### <a name="log-analytics"></a>Log Analytics

Monitoraggio di Azure per le macchine virtuali supporta un'area di lavoro di Log Analytics nelle aree seguenti:Azure Monitor for VMs supports a Log Analytics workspace in the following regions:

- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti orientali
- Stati Uniti Orientali 2
- Stati Uniti centrali
- Stati Uniti centro-settentrionali
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
>È possibile monitorare le macchine virtuali di Azure in qualsiasi area. Le macchine virtuali stesse non sono limitate alle aree supportate dall'area di lavoro di Log Analytics.The VMs themselves aren't limited to the regions supported by the Log Analytics workspace.
>

Se non si dispone di un'area di lavoro di Log Analytics, è possibile crearne una usando una delle risorse:
* [Interfaccia della riga di comando di AzureAzure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Powershell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Portale di Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

È anche possibile creare un'area di lavoro mentre si abilita il monitoraggio per una singola macchina virtuale di Azure o set di scalabilità di macchine virtuali nel portale di Azure.You can also create a workspace while you're enabling monitoring for a single Azure VM or virtual machine scale set in the Azure portal.

Per configurare uno scenario a livello di scalabilità che usa modelli di Criteri di Azure, Azure PowerShell o Azure Resource Manager nell'area di lavoro di Log Analytics:To set up an at-scale scenario that uses Azure Policy, Azure PowerShell, or Azure Resource Manager templates, in your Log Analytics workspace:

* Installare le soluzioni ServiceMap e *InfrastructureInsights.Install* the *ServiceMap* and InfrastructureInsights solutions. È possibile completare questa installazione usando un modello di Azure Resource Manager fornito. In alternativa, nella scheda **Introduzione** nel portale di Azure selezionare **Configura area di lavoro**.
* Configurare l'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni.

Per configurare l'area di lavoro per lo scenario su larga scala, utilizzare uno dei seguenti metodi:

* Usare [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Nella pagina [**Copertura criteri**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) di Monitoraggio di Azure per le macchine virtuali selezionare **Configura area di lavoro**. 

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi Windows e Linux supportati da Monitoraggio di Azure per macchine virtuali. Più avanti in questa sezione, troverete un elenco completo che descrive in dettaglio la versione principale e secondaria del sistema operativo Linux e le versioni del kernel supportate.

|Versione del sistema operativo |Prestazioni |Mappe |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1 (in questo</sup> modo) | |

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.It isn't available directly from the left pane of the Azure VM.

>[!NOTE]
>Nel sistema operativo Linux:
> - Sono supportate solo versioni predefinita e SMP del kernel Linux.
> - Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
> - I kernel personalizzati, incluse le ricompilazioni dei kernel standard, non sono supportati.
> - Il kernel CentOSPlus è supportato.
> - Il kernel Linux deve essere patchato per la vulnerabilità Spectre. Per ulteriori dettagli, consultare il fornitore della distribuzione Linux.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 18,04 | 5.0 (include il kernel tonizzato da Azure)5.0 (includes Azure-tuned kernel)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
|12 SP4 | 4.12. (include kernel toned Azure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La funzionalità Mappa in Monitoraggio di Azure per le macchine virtuali ottiene i dati dall'agente di dipendenza Microsoft.The Map feature in Azure Monitor for VMs gets its data from the Microsoft Dependency agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. Pertanto nel sistema deve essere installato e configurato l'agente Log Analytics con l'agente di dipendenza.

Se si abilita Monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure o si usa il metodo di distribuzione a scalabilità implementata, usare l'estensione dell'agente di dipendenza della macchina virtuale di Azure per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) o [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) per installare l'agente come parte dell'esperienza.

>[!NOTE]
>Le informazioni seguenti descritte in questa sezione sono applicabili anche alla [soluzione Mappa dei](service-map.md)servizi.  

In un ambiente ibrido, è possibile scaricare e installare l'agente di dipendenza manualmente o utilizzando un metodo automatizzato.

La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | Insieme [all'agente di Log Analytics per Windows,](../../azure-monitor/platform/log-analytics-agent.md)gli agenti windows necessitano dell'agente di dipendenza. Per ulteriori informazioni, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | Insieme [all'agente Log Analytics per Linux,](../../azure-monitor/platform/log-analytics-agent.md)gli agenti Linux hanno bisogno dell'agente di dipendenza. Per ulteriori informazioni, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

È possibile scaricare l'agente di dipendenza da queste posizioni:You can download the Dependency agent from these locations:

| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | WINDOWS | 9.10.2.9060 | B7725B6B205CF8C336D9AAD87956336C816412740E9D6499BCACB6F862AE3896  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.2.9060 | C6995A67A7782AEC312647D74A99C3C823F68F5FFA490FD4BB6006A2FF2941B0 |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per abilitare e accedere alle funzionalità di Monitoraggio di Azure per le macchine virtuali, è necessario disporre del ruolo di *collaboratore* di Log Analytics.To enable and access the features in Azure Monitor for VMs, you must have the Log Analytics contributor role. Per visualizzare le prestazioni, l'integrità e i dati di mapping, è necessario disporre del ruolo di lettore di monitoraggio per la macchina virtuale di Azure.To view performance, health, and map *data,* you must have the monitoring reader role for the Azure VM. L'area di lavoro Log Analytics deve essere configurata per Monitoraggio di Azure per le macchine virtuali.

Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Come abilitare Monitoraggio di Azure per le macchine virtualiHow to enable Azure Monitor for VMs

Abilitare Monitoraggio di Azure per le macchine virtuali usando uno dei metodi descritti in questa tabella:Enable Azure Monitor for VMs by using one of the methods described in this table:

| Stato distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Singolo set di scalabilità di macchine virtuali o macchine virtuali di AzureSingle Azure VM or virtual machine scale set | [Abilitare dalla macchina virtualeEnable from the VM](vminsights-enable-single-vm.md) | È possibile abilitare una singola macchina virtuale di Azure selezionando **Insights** direttamente dalla macchina virtuale o dal set di scalabilità della macchina virtuale. |
| Più macchine virtuali di Azure o set di scalabilità di macchine virtualiMultiple Azure VMs or virtual machine scale sets | [Abilitare tramite i criteri di AzureEnable through Azure Policy](vminsights-enable-at-scale-policy.md) | È possibile abilitare più macchine virtuali di Azure usando Criteri di Azure e le definizioni dei criteri disponibili. |
| Più macchine virtuali di Azure o set di scalabilità di macchine virtualiMultiple Azure VMs or virtual machine scale sets | [Abilitare i modelli di Azure PowerShell o Azure Resource ManagerEnable through Azure PowerShell or Azure Resource Manager templates](vminsights-enable-at-scale-powershell.md) | È possibile abilitare più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato usando i modelli di Azure PowerShell o Azure Resource Manager.You can enable multiple Azure VMs or virtual machine scale sets across a specified subscription or resource group by using Azure PowerShell or Azure Resource Manager templates. |
| Cloud ibrido | [Abilitare per l'ambiente ibridoEnable for the hybrid environment](vminsights-enable-hybrid-cloud.md) | È possibile eseguire la distribuzione in macchine virtuali o computer fisici ospitati nel data center o in altri ambienti cloud. |

## <a name="management-packs"></a>Management Pack

Quando Monitoraggio di Azure per le macchine virtuali è abilitato e configurato con un'area di lavoro di Log Analytics, viene inoltrato un Management Pack a tutti i computer Windows che fanno riferimento a tale area di lavoro. Se il gruppo di gestione di [System Center Operations Manager](../../azure-monitor/platform/om-agents.md) è stato integrato con l'area di lavoro di Log Analytics, il Management Pack della mappa dei servizi viene distribuito dal gruppo di gestione ai computer Windows che segnalano il gruppo di gestione.  

Il Management Pack è denominato *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. La sua `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` scritta nella cartella. L'origine dati utilizzata dal `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`Management Pack è .

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft utilizza questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio. 

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, la funzionalità Mappa include dati sulla configurazione del software. I dati forniscono informazioni quali il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Dopo aver abilitato il monitoraggio per la macchina virtuale, le informazioni sul monitoraggio sono disponibili per l'analisi in Monitoraggio di Azure per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di monitoraggio delle prestazioni, vedere [Visualizzare Monitoraggio di Azure per le prestazioni delle macchine virtuali.](vminsights-performance.md) Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
