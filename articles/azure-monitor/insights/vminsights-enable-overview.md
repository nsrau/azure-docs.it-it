---
title: Abilitare il monitoraggio di Azure per la panoramica di macchine virtuali (anteprima) | Microsoft Docs
description: Informazioni su come distribuire e configurare monitoraggio di Azure per le macchine virtuali. Informazioni sui requisiti di sistema.
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
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478056"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Abilitare il monitoraggio di Azure per la panoramica di macchine virtuali (anteprima)

Questo articolo offre una panoramica delle opzioni disponibili per configurare monitoraggio di Azure per macchine virtuali. Usare monitoraggio di Azure per le macchine virtuali per monitorare l'integrità e prestazioni. Individuare le dipendenze dell'applicazione in esecuzione su macchine virtuali di Azure (VM) e set di scalabilità di macchine virtuali, in locale le macchine virtuali o le macchine virtuali ospitate in un altro ambiente cloud.  

Configurare monitoraggio di Azure per le macchine virtuali:

* Abilitare una singola macchina virtuale di Azure o una macchina virtuale set di scalabilità selezionando **Insights (anteprima)** direttamente dal VM o macchina virtuale di set di scalabilità.
* Abilitare due o set di scalabilità di macchine virtuali di Azure e macchine virtuali più usando criteri di Azure. Questo metodo assicura che nelle macchine virtuali nuove ed esistenti e i set di scalabilità, le dipendenze necessarie vengono installate e configurate correttamente. Set di scalabilità e le macchine virtuali non conformi vengono segnalate, pertanto è possibile decidere se per abilitarle e monitorarle e aggiornarle.
* Abilitare due o più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.
* Abilitare il monitoraggio di Azure per le macchine virtuali monitorare le macchine virtuali o computer fisici nella rete aziendale o un altro ambiente cloud ospitato.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver compreso quanto illustrato nelle sezioni seguenti.

### <a name="log-analytics"></a>Log Analytics

Monitoraggio per le macchine virtuali di Azure supporta un'area di lavoro di Log Analitica nelle aree seguenti:

- Stati Uniti centro-occidentali
- Stati Uniti occidentali 2<sup>1</sup>
- East US
- Canada centrale
- Regno Unito meridionale
- Europa occidentale
- Asia sud-orientale

<sup>1</sup> Quest'area attualmente non supporta la funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali.

>[!NOTE]
>È possibile distribuire macchine virtuali di Azure da qualsiasi area. Queste macchine virtuali non sono limitate per le aree supportate nell'area di lavoro di Log Analitica.
>

Se non si dispone di un'area di lavoro, è possibile creare una con una di queste risorse:
* [L’interfaccia della riga di comando di Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Il portale di Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

È anche possibile creare un'area di lavoro mentre si sta abilitando il monitoraggio per una singola macchina virtuale di Azure o una macchina virtuale set di scalabilità nel portale di Azure.

Per configurare uno scenario su larga scala che usa i modelli di criteri di Azure, Azure PowerShell o Azure Resource Manager, nell'area di lavoro di Log Analitica:

* Installare le soluzioni ServiceMap e InfrastructureInsights. È possibile completare l'installazione utilizzando un modello di Azure Resource Manager specificato. O scegliere il **iniziare a usare** scheda, seleziona **dell'area di lavoro configura**.
* Configurare l'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni.

Per configurare l'area di lavoro per lo scenario di su larga scala, usare uno dei metodi seguenti:

* Uso [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Del monitoraggio di Azure di macchine virtuali [ **copertura dei criteri** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) pagina, selezionare **Configura area di lavoro**. 

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

La tabella seguente elenca i sistemi operativi Windows e Linux che supporta il monitoraggio di Azure per le macchine virtuali. Più avanti in questa sezione, troverai un elenco completo che descrive in dettaglio il principale e secondaria del sistema operativo Linux versione e versioni del kernel supportate.

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
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.

>[!NOTE]
>La funzionalità integrità del monitoraggio di Azure per le macchine virtuali non supporta [virtualizzazione nidificata](../../virtual-machines/windows/nested-virtualization.md) in una VM di Azure.
>

>[!NOTE]
>Nel sistema operativo Linux:
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

#### <a name="centosplus"></a>CentOSPlus
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Kernel ottimizzati su Azure |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La funzionalità di mappa in Monitoraggio di Azure per le macchine virtuali Ottiene i dati da Microsoft Dependency agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. In modo che l'agente di Log Analitica installati e configurati con l'agente di dipendenza nel sistema.

Se si abilita il monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure oppure usare il metodo di distribuzione su larga scala, usare l'estensione dell'agente di dipendenza di macchina virtuale di Azure per installare l'agente come parte dell'esperienza.

In un ambiente ibrido, è possibile scaricare e installare manualmente l'agente di dipendenza. Se le macchine virtuali sono ospitate all'esterno di Azure, usare un metodo di distribuzione automatica.

La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Yes | Con il [agente di Log Analitica per Windows](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Windows devono Dependency agent. Per altre informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Yes | Con il [agente di Log Analitica per Linux](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Linux devono Dependency agent. Per altre informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

È possibile scaricare l'agente di dipendenza da queste posizioni:

| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per abilitare e accedere alle funzionalità in Monitoraggio di Azure per le macchine virtuali, è necessario disporre di *collaboratore di Log Analitica* ruolo. Per visualizzare le prestazioni, integrità ed eseguire il mapping dei dati, è necessario disporre di *lettore di monitoraggio* ruolo per la macchina virtuale di Azure. L'area di lavoro Log Analytics deve essere configurata per Monitoraggio di Azure per le macchine virtuali.

Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Come abilitare il monitoraggio di Azure per le macchine virtuali (anteprima)

Abilitare il monitoraggio di Azure per le macchine virtuali usando uno dei metodi descritti in questa tabella:

| Stato di distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Set di scalabilità di macchine Virtuali di Azure o una macchina virtuale singola | [Abilitare dalla macchina virtuale](vminsights-enable-single-vm.md) | È possibile abilitare una singola macchina virtuale di Azure selezionando **Insights (anteprima)** direttamente dal VM o macchina virtuale di set di scalabilità. |
| Più macchine virtuali di Azure o il set di scalabilità di macchine virtuali | [Abilitare tramite criteri di Azure](vminsights-enable-at-scale-policy.md) | È possibile abilitare più macchine virtuali di Azure usando criteri di Azure e le definizioni dei criteri disponibili. |
| Più macchine virtuali di Azure o il set di scalabilità di macchine virtuali | [Abilitare attraverso i modelli di Azure PowerShell o Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | È possibile abilitare più set di scalabilità di macchine virtuali di Azure o una macchina virtuale attraverso una sottoscrizione specificata o un gruppo di risorse con i modelli di Azure PowerShell o Azure Resource Manager. |
| Cloud ibrido | [Abilitare per l'ambiente ibrido](vminsights-enable-hybrid-cloud.md) | È possibile distribuire in macchine virtuali o computer fisici che sono ospitati nel tuo Data Center o in altri ambienti cloud. |

## <a name="performance-counters-enabled"></a>Contatori delle prestazioni abilitati 

Monitoraggio di Azure per le macchine virtuali consente di configurare un'area di lavoro di Log Analitica per raccogliere i contatori delle prestazioni che lo usa. Le tabelle seguenti elencano gli oggetti e i contatori raccolti ogni 60 secondi.

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

|Nome dell'oggetto |Nome contatore |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Byte da/a disco/sec |
|LogicalDisk |Byte letti da disco/sec |
|LogicalDisk |Letture disco/sec |
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
|Logical Disk |Byte letti da disco/sec |
|Logical Disk |Letture disco/sec |
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

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft Usa questi dati per migliorare la qualità, sicurezza e l'integrità del servizio. 

Per fornire capacità di risoluzione dei problemi accurate ed efficienti, la funzionalità mappa include i dati sulla configurazione del software. I dati forniscono informazioni quali il sistema operativo e versione indirizzo IP, nome DNS e nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Ora che è stato abilitato il monitoraggio per la macchina virtuale, le informazioni di monitoraggio sono disponibile per l'analisi in Monitoraggio di Azure per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
