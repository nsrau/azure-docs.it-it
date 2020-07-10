---
title: Panoramica di State Configuration di Automazione di Azure
description: Questo articolo offre una panoramica del servizio State Configuration di Automazione di Azure.
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 06/22/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6a1c6bb39e743a96ad110a60e41cc59306e7a2ae
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186385"
---
# <a name="azure-automation-state-configuration-overview"></a>Panoramica di State Configuration di Automazione di Azure

State Configuration di Automazione di Azure è un servizio di gestione della configurazione di Azure che consente di scrivere, gestire e compilare le [configurazioni](/powershell/scripting/dsc/configurations/configurations) di PowerShell DSC (Desired State Configuration) per i nodi di qualsiasi cloud o data center locale. Il servizio importa anche le [risorse DSC](/powershell/scripting/dsc/resources/resources) e assegna le configurazioni ai nodi di destinazione, tutto nel cloud. È possibile accedere al servizio State Configuration di Automazione di Azure nel portale di Azure selezionando **State configuration (DSC)** in **Gestione della configurazione**.

È possibile usare State Configuration di Automazione di Azure per gestire diversi tipi di computer/macchine:

- Macchine virtuali di Azure
- Macchine virtuali di Azure (classica)
- Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure (incluse le istanze EC2 di AWS)
- Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Se non si è pronti a gestire la configurazione delle macchine dal cloud, è possibile usare State Configuration di Automazione di Azure come endpoint solo report. Questa funzionalità consente di impostare (push) le configurazioni tramite DSC e visualizzare i dettagli dei report in Automazione di Azure.

> [!NOTE]
> La gestione delle macchine virtuali di Azure con State Configuration di Automazione di Azure è inclusa senza costi aggiuntivi se la versione installata dell'estensione DSC (Desired State Configuration) della macchina virtuale di Azure è superiore alla versione 2.70. Per altre informazioni, vedere la [**pagina dei prezzi di Automazione**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Perché usare Configurazione stato di Automazione di Azure

State Configuration di Automazione di Azure offre diversi vantaggi rispetto all'uso di DSC all'esterno di Azure. Questo servizio consente la scalabilità tra migliaia di macchine in modo rapido e semplice da una posizione centrale e sicura. È possibile abilitare facilmente le macchine, assegnare alle stesse configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni macchina con lo stato desiderato specificato.

Il servizio State Configuration di Automazione di Azure è per DSC quello che i runbook di Automazione di Azure sono per gli script di PowerShell. In altre parole, Automazione di Azure consente di gestire gli script di PowerShell e le configurazioni DSC. 

### <a name="built-in-pull-server"></a>Server di pull predefinito

State Configuration di Automazione di Azure fornisce un server di pull DSC simile alla [funzionalità di Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). I nodi di destinazione possono ricevere automaticamente le configurazioni, conformemente allo stato desiderato, e segnalare la propria conformità. Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull. Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

State Configuration di Automazione di Azure introduce in [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) lo stesso livello di gestione offerto per gli script di PowerShell. Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione DSC.

![Screenshot della pagina di accesso di Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importazione di dati di report nei log di Monitoraggio di Azure

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro Log Analytics. Vedere [Inoltrare i dati di report di State Configuration di Automazione di Azure nei log di Monitoraggio di Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Prerequisiti

Prendere in considerazione i requisiti di questa sezione quando si usa State Configuration di Automazione di Azure.

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo

Per i nodi che eseguono Windows sono supportate le versioni seguenti:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Lo SKU del prodotto autonomo [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) non contiene un'implementazione di DSC. Non può quindi essere gestito da PowerShell DSC o da State Configuration di Automazione di Azure.

Per i nodi che eseguono Linux, l'estensione DSC per Linux supporta tutte le distribuzioni Linux elencate in [Distribuzioni Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisiti di DSC

Per tutti i nodi Windows in esecuzione in Azure, viene installato [WMF 5.1](/powershell/scripting/wmf/setup/install-configure) quando le macchine vengono abilitate. Per i nodi che eseguono Windows Server 2012 e Windows 7, viene abilitato [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Per tutti i nodi Linux in esecuzione in Azure, viene installato [PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) quando le macchine vengono abilitate.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configurazione di reti private

Se i nodi si trovano in una rete privata, sono necessari la porta e gli URL seguenti. Queste risorse forniscono la connettività di rete per il nodo gestito e consentono a DSC di comunicare con Automazione di Azure.

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: * **.azure-automation.net**
* URL globale di US Gov Virginia: * **.azure-automation.us**
* Servizio Agent: **https:// \<workspaceId\> . agentsvc.Azure-Automation.NET**

Se si usano risorse DSC che comunicano tra i nodi, ad esempio le [risorse WaitFor*](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), è anche necessario consentire il traffico tra i nodi. Per informazioni su questi requisiti di rete, vedere la documentazione relativa a ogni risorsa DSC.

Per informazioni sui requisiti dei client per TLS 1,2, vedere [tls 1,2 Enforcement for Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="proxy-support"></a>Supporto del proxy

Il supporto del proxy per l'agente DSC è disponibile nella versione di Windows 1809 e versioni successive. Questa opzione viene abilitata impostando i valori delle proprietà `ProxyURL` e `ProxyCredential` nello script di [metaconfigurazione](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) usato per registrare i nodi. 

>[!NOTE]
>State Configuration di Automazione di Azure non fornisce il supporto del proxy DSC per le versioni precedenti di Windows.

Per i nodi Linux, l'agente DSC supporta il proxy e usa la variabile `http_proxy` per determinare l'URL. Per altre informazioni sul supporto del proxy, vedere [Generare metaconfigurazioni DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Record DNS per area

Quando si definiscono le eccezioni, è consigliabile usare gli indirizzi elencati nella tabella [record DNS per area](how-to/automation-region-dns-records.md) .

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come abilitare i nodi, vedere [Abilitare State Configuration di Automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
