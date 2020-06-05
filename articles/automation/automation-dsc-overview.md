---
title: Panoramica di State Configuration di Automazione di Azure
description: Questo articolo offre una panoramica del servizio State Configuration di Automazione di Azure.
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9880915061c0639aebe30bdb33258d7c79e155d7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836890"
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

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Prerequisiti per l'uso di State Configuration di Automazione di Azure

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

Per tutti i nodi Windows in esecuzione in Azure, viene installato [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) quando le macchine vengono abilitate. Per i nodi che eseguono Windows Server 2012 e Windows 7, viene abilitato [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Per tutti i nodi Linux in esecuzione in Azure, viene installato [PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) quando le macchine vengono abilitate.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configurazione di reti private

Se i nodi si trovano in una rete privata, sono necessari la porta e gli URL seguenti. Queste risorse forniscono la connettività di rete per il nodo gestito e consentono a DSC di comunicare con Automazione di Azure.

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: * **.azure-automation.net**
* URL globale di US Gov Virginia: * **.azure-automation.us**
* Servizio agente: **https://\<workspaceId\>.agentsvc.azure-automation.net**

Se si usano risorse DSC che comunicano tra i nodi, ad esempio le [risorse WaitFor*](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), è anche necessario consentire il traffico tra i nodi. Per informazioni su questi requisiti di rete, vedere la documentazione relativa a ogni risorsa DSC.

#### <a name="proxy-support"></a>Supporto del proxy

Il supporto del proxy per l'agente DSC è disponibile nella versione di Windows 1809 e versioni successive. Questa opzione viene abilitata impostando i valori delle proprietà `ProxyURL` e `ProxyCredential` nello script di [metaconfigurazione](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) usato per registrare i nodi. 

>[!NOTE]
>State Configuration di Automazione di Azure non fornisce il supporto del proxy DSC per le versioni precedenti di Windows.

Per i nodi Linux, l'agente DSC supporta il proxy e usa la variabile `http_proxy` per determinare l'URL. Per altre informazioni sul supporto del proxy, vedere [Generare metaconfigurazioni DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Intervalli di rete e spazio dei nomi di State Configuration di Automazione di Azure

È consigliabile usare gli indirizzi elencati di seguito quando si definiscono le eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asia sud-orientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione.
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. 

Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come abilitare i nodi, vedere [Abilitare State Configuration di Automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).