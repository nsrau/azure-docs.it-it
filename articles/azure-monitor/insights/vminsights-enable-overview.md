---
title: Abilitare il monitoraggio di Azure per la panoramica di macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive come distribuire e configurare monitoraggio di Azure per macchine virtuali e i requisiti di sistema necessari.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 76d18b6a942ed9b8c6871b0ff7cbc1c83917ada4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130477"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Abilitare il monitoraggio di Azure per la panoramica di macchine virtuali (anteprima)

Questo articolo offre una panoramica delle opzioni disponibili per configurare il monitoraggio di Azure per le macchine virtuali monitorare l'integrità, prestazioni e individuare le dipendenze dell'applicazione in esecuzione in macchine virtuali di Azure e set di scalabilità di macchine virtuali, in locale le macchine virtuali o le macchine virtuali ospitate in un altro ambiente cloud.  

Si può abilitare Monitoraggio di Azure per le macchine virtuali usando uno dei metodi seguenti:

* Abilitare una singola macchina virtuale di Azure o la selezione di set di scalabilità di macchine virtuali **Insights (anteprima)** direttamente dal VM o macchina virtuale di set di scalabilità.
* Abilitare due o set di scalabilità di macchine virtuali di Azure e macchine virtuali più usando criteri di Azure. Tramite questo metodo, le dipendenze necessarie del set di scalabilità e le macchine virtuali nuove ed esistenti vengono installate e configurate correttamente. Set di scalabilità e le macchine virtuali non conformi vengono segnalate, è possibile stabilire se si desidera abilitarle e modo in cui si vuole monitorarle e aggiornarle.
* Abilitare due o più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.
* Consentono di monitorare le macchine virtuali o computer fisici nella rete aziendale o un altro ambiente cloud ospitato.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver compreso quanto illustrato nelle sezioni seguenti.

### <a name="log-analytics"></a>Log Analytics

Monitoraggio per le macchine virtuali di Azure supporta un'area di lavoro di Log Analitica nelle aree seguenti:

- Stati Uniti centro-occidentali
- Stati Uniti orientali
- Canada Central<sup>1</sup>
- Regno Unito meridionale<sup>1</sup>
- Europa occidentale
- Asia sud-orientale<sup>1</sup>

<sup>1</sup> Quest'area attualmente non supporta la funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali.

>[!NOTE]
>Le macchine virtuali di Azure possono essere distribuite da qualsiasi area e non solo dalle aree supportate per l'area di lavoro Log Analytics.
>

Se non si ha un'area di lavoro, è possibile crearne una con uno dei metodi seguenti:
* [L’interfaccia della riga di comando di Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Il portale di Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Se si sta abilitando il monitoraggio per una singola macchina virtuale di Azure o la scalabilità di macchine virtuali impostate nel portale di Azure, è possibile creare un'area di lavoro durante questo processo.

Per lo scenario di su larga scala usando criteri di Azure, Azure PowerShell o modelli di Azure Resource Manager, all'area di lavoro di Log Analitica deve prima di tutto configurati gli elementi seguenti:

* Installare le soluzioni ServiceMap e InfrastructureInsights. È possibile completare l'installazione usando un modello di Azure Resource Manager che viene fornito o utilizzando il **Configura area di lavoro** opzione TransportCredentialOnly il **iniziare** scheda.
* Configurare l'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni.

Per configurare l'area di lavoro per lo scenario di su larga scala, è possibile configurarlo usando uno dei metodi seguenti:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Dal **dell'area di lavoro configurare** opzione del monitoraggio di Azure per le macchine virtuali [copertura dei criteri](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) pagina

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

La tabella seguente elenca le versioni dei sistemi operativi Windows e Linux supportati con Monitoraggio di Azure per le macchine virtuali. L'elenco completo con le versioni principali e secondarie del kernel e del sistema operativo Linux supportate è disponibile più avanti in questa sezione.

|Versione del sistema operativo |Prestazioni |Mappe |Integrità |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure. Non è disponibile quando si accede direttamente dal riquadro a sinistra della macchina virtuale di Azure.

>[!NOTE]
>Le informazioni seguenti si applicano al supporto del sistema operativo Linux:
> - Sono supportate solo versioni predefinita e SMP del kernel Linux.
> - Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
> - I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportati.
> - Il kernel CentOSPlus è supportato.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. Di conseguenza, in un sistema l'agente di Log Analytics deve essere installato e configurato con Dependency Agent.

Che si abiliti Monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure o si usi il metodo per la distribuzione su larga scala, l'estensione Dependency Agent della macchina virtuale di Azure è comunque necessaria per installare l'agente come parte dell'esperienza.

In un ambiente ibrido, è possibile scaricare e installare Dependency agent in uno dei due modi: manualmente o usando un metodo di distribuzione automatica per le macchine virtuali che sono ospitati all'esterno di Azure.

La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Sì | Oltre all'[agente di Log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | Oltre all'[agente di Log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No  | |

È possibile scaricare Dependency Agent dalle posizioni seguenti:

| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per abilitare e accedere alle funzionalità in Monitoraggio di Azure per le macchine virtuali, è necessario avere i ruoli di accesso seguenti:

- Per abilitarla, è necessario disporre di *collaboratore di Log Analitica* ruolo.

- Per visualizzare i dati su prestazioni, integrità e mappe, è necessario avere il ruolo con autorizzazioni di *lettura dei dati di monitoraggio* per la macchina virtuale di Azure. L'area di lavoro Log Analytics deve essere configurata per Monitoraggio di Azure per le macchine virtuali.

Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Come abilitare il monitoraggio di Azure per le macchine virtuali (anteprima)

Si attiva il monitoraggio di Azure per le macchine virtuali usando uno dei seguenti metodi descritti nella tabella seguente.

| Stato di distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Set di scalabilità di macchine Virtuali di Azure o una macchina virtuale singola | [Direttamente dalla macchina virtuale](vminsights-enable-single-vm.md) | È possibile abilitare una singola macchina virtuale di Azure selezionando **Insights (anteprima)** direttamente dal VM o macchina virtuale di set di scalabilità. |
| Più macchine virtuali di Azure o il set di scalabilità di macchine virtuali | [Criteri di Azure](vminsights-enable-at-scale-policy.md) | È possibile abilitare più macchine virtuali di Azure usando criteri di Azure e le definizioni dei criteri disponibili. |
| Più macchine virtuali di Azure o il set di scalabilità di macchine virtuali | [Modelli di Azure PowerShell o Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | È possibile abilitare più set di scalabilità di macchine virtuali di Azure o una macchina virtuale in un gruppo di risorse o sottoscrizione specificato usando i modelli di Azure PowerShell o Azure Resource Manager. |
| Cloud ibrido | [Abilitare per ambiente ibrido](vminsights-enable-hybrid-cloud.md) | È possibile distribuire le macchine virtuali o computer fisici che sono ospitati nel tuo Data Center o in altri ambienti cloud. |

## <a name="performance-counters-enabled"></a>Contatori delle prestazioni abilitati

Monitoraggio di Azure per le macchine virtuali consente di configurare un'area di lavoro di Log Analitica per raccogliere i contatori delle prestazioni che lo usa. Nella tabella seguente sono elencati gli oggetti e i contatori raccolti ogni 60 secondi.

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

|Nome dell'oggetto |Nome contatore |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Byte da/a disco/sec |
|LogicalDisk |Byte letti da disco/sec  |
|LogicalDisk |Letture disco/sec  |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Byte scritti su disco/sec |
|LogicalDisk |Scritture disco/sec |
|LogicalDisk |Free Megabytes |
|Memoria |MByte disponibili |
|Scheda di rete |Byte ricevuti/sec |
|Scheda di rete |Byte inviati/sec |
|Processore |% di tempo processore |

### <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux

|Nome dell'oggetto |Nome contatore |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Byte letti da disco/sec  |
|Logical Disk |Letture disco/sec  |
|Logical Disk |Disk Transfers/sec |
|Logical Disk |Byte scritti su disco/sec |
|Logical Disk |Scritture disco/sec |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|Memoria |Available MBytes Memory |
|Rete |Total Bytes Received |
|Rete |Total Bytes Transmitted |
|Processore |% di tempo processore |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per garantire e migliorare la qualità, la sicurezza e l'integrità del servizio. Per offrire capacità di risoluzione dei problemi accurate ed efficienti, i dati della funzionalità di mappa includono informazioni sulla configurazione del software, come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

Una volta abilitato il monitoraggio per la macchina virtuale, le informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali. Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
