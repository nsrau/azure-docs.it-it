---
title: Abilita Panoramica Monitoraggio di Azure per le macchine virtuali
description: Informazioni su come distribuire e configurare Monitoraggio di Azure per le macchine virtuali. Individuare i requisiti di sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982311"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Abilita Panoramica Monitoraggio di Azure per le macchine virtuali

In questo articolo viene fornita una panoramica delle opzioni disponibili per abilitare Monitoraggio di Azure per le macchine virtuali sulle macchine virtuali per monitorare l'integrità e le prestazioni. Individuare le dipendenze delle applicazioni eseguite in macchine virtuali (VM) di Azure e set di scalabilità di macchine virtuali, macchine virtuali locali o macchine virtuali ospitate in un altro ambiente cloud.  

Per configurare Monitoraggio di Azure per le macchine virtuali:

* Abilitare una singola VM di Azure o un set di scalabilità di macchine virtuali selezionando **informazioni dettagliate** direttamente dalla VM o dal set di scalabilità di macchine virtuali.
* Abilitare due o più macchine virtuali di Azure e set di scalabilità di macchine virtuali usando criteri di Azure. Questo metodo garantisce che nelle VM nuove e esistenti e nei set di scalabilità siano installate e configurate correttamente le dipendenze necessarie. Le macchine virtuali non conformi e i set di scalabilità vengono segnalati, pertanto è possibile decidere se abilitarli e risolverli.
* Abilitare due o più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.
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
* [Interfaccia della riga di comando di Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

È anche possibile creare un'area di lavoro mentre si Abilita il monitoraggio per una singola VM di Azure o un set di scalabilità di macchine virtuali nel portale di Azure.

Per configurare uno scenario su larga scala che usa i modelli di criteri, Azure PowerShell o Azure Resource Manager di Azure, nell'area di lavoro Log Analytics:

* Installare le soluzioni *ServiceMap* e *InfrastructureInsights* . È possibile completare questa installazione usando un modello di Azure Resource Manager fornito. In alternativa, nella scheda attività **iniziali** della portale di Azure fare clic su **Configura area di lavoro**.
* Configurare l'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni.

Per configurare l'area di lavoro per lo scenario in scala, usare uno dei metodi seguenti:

* Usare [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Nella pagina [**copertura criteri**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) monitoraggio di Azure per le macchine virtuali selezionare **Configura area di lavoro**. 

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi Windows e Linux supportati da Monitoraggio di Azure per le macchine virtuali. Più avanti in questa sezione è presente un elenco completo che descrive in dettaglio la versione principale e secondaria del sistema operativo Linux e le versioni del kernel supportate.

|Versione sistema operativo |Prestazioni |Mappe |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.

>[!NOTE]
>Nel sistema operativo Linux:
> - Sono supportate solo versioni predefinita e SMP del kernel Linux.
> - Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
> - I kernel personalizzati, incluse le ricompilazioni dei kernel standard, non sono supportati.
> - Il kernel CentOSPlus è supportato.
> - È necessario applicare patch al kernel Linux per la vulnerabilità di Spectre. Per ulteriori informazioni, consultare il fornitore della distribuzione Linux.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 18,04 | 5,0 (include il kernel ottimizzato per Azure)<br>4,18*<br>4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione sistema operativo | Versione del kernel |
|:--|:--|
|12 SP4 | 4,12. * (include il kernel ottimizzato per Azure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La funzionalità map in Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. Il sistema deve quindi avere l'agente di Log Analytics installato e configurato con Dependency Agent.

Se si abilita Monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure o si usa il metodo di distribuzione su larga scala, usare l'estensione dell'agente di dipendenza di VM di Azure per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) o [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) per installare l'agente come parte dell'esperienza.

>[!NOTE]
>Le seguenti informazioni descritte in questa sezione sono applicabili anche alla [soluzione mapping dei servizi](service-map.md).  

In un ambiente ibrido è possibile scaricare e installare l'agente di dipendenza manualmente o usando un metodo automatizzato.

La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | Insieme all' [agente log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Windows necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | Insieme all' [agente log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Linux necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

È possibile scaricare Dependency Agent da questi percorsi:

| File | Sistema operativo | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0B |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per abilitare e accedere alle funzionalità di Monitoraggio di Azure per le macchine virtuali, è necessario avere il ruolo di *collaboratore log Analytics* . Per visualizzare le prestazioni, l'integrità e la mappa dei dati, è necessario avere il ruolo di *lettore di monitoraggio* per la macchina virtuale di Azure. L'area di lavoro Log Analytics deve essere configurata per Monitoraggio di Azure per le macchine virtuali.

Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Come abilitare Monitoraggio di Azure per le macchine virtuali

Abilitare Monitoraggio di Azure per le macchine virtuali usando uno dei metodi descritti in questa tabella:

| Stato distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Singola VM di Azure o set di scalabilità di macchine virtuali | [Abilita dalla macchina virtuale](vminsights-enable-single-vm.md) | È possibile abilitare una singola macchina virtuale di Azure selezionando **informazioni dettagliate** direttamente dalla VM o dal set di scalabilità di macchine virtuali. |
| Più macchine virtuali di Azure o set di scalabilità di macchine virtuali | [Abilita tramite criteri di Azure](vminsights-enable-at-scale-policy.md) | È possibile abilitare più macchine virtuali di Azure usando i criteri di Azure e le definizioni dei criteri disponibili. |
| Più macchine virtuali di Azure o set di scalabilità di macchine virtuali | [Abilita tramite modelli di Azure PowerShell o Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | È possibile abilitare più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specifico usando Azure PowerShell o modelli Azure Resource Manager. |
| Cloud ibrido | [Abilitare per l'ambiente ibrido](vminsights-enable-hybrid-cloud.md) | È possibile eseguire la distribuzione in macchine virtuali o computer fisici ospitati nel Data Center o in altri ambienti cloud. |

## <a name="management-packs"></a>Management Pack

Quando Monitoraggio di Azure per le macchine virtuali è abilitata e configurata con un'area di lavoro Log Analytics, un Management Pack viene inviato a tutti i computer Windows che inviano report all'area di lavoro. Se il [gruppo di gestione System Center Operations Manager](../../azure-monitor/platform/om-agents.md) è stato integrato con l'area di lavoro log Analytics, il mapping dei servizi Management Pack viene distribuito dal gruppo di gestione ai computer Windows che inviano report al gruppo di gestione.  

Il Management Pack è denominato *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. La cartella scritta `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` nella cartella. L'origine dati utilizzata dall'Management Pack è `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio. 

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, la funzionalità map include i dati sulla configurazione del software. I dati forniscono informazioni come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Ora che è stato abilitato il monitoraggio per la macchina virtuale, le informazioni di monitoraggio sono disponibili per l'analisi in Monitoraggio di Azure per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di monitoraggio delle prestazioni, vedere [visualizzare le prestazioni monitoraggio di Azure per le macchine virtuali](vminsights-performance.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
