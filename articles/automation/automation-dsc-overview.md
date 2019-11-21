---
title: Panoramica di Configurazione stato di Automazione di Azure
description: Panoramica di Configurazione stato di Automazione di Azure (DSC), dei relativi termini e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ed18f8a8bbd8bd323dec54ca3f700c7ce168dde
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231638"
---
# <a name="azure-automation-state-configuration-overview"></a>Panoramica di Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure è un servizio di Azure che consente di scrivere, gestire e compilare le [configurazioni](/powershell/scripting/dsc/configurations/configurations) di PowerShell DSC (Desired State Configuration), importare [risorse DSC](/powershell/scripting/dsc/resources/resources) e assegnare configurazioni ai nodi di destinazione, il tutto nel cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Perché usare Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure offre diversi vantaggi rispetto all'uso di DSC all'esterno di Azure.

### <a name="built-in-pull-server"></a>Server di pull predefinito

Configurazione stato di Automazione di Azure fornisce un server di pull DSC simile alla [funzionalità di Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver) in modo che i nodi di destinazione ricevano automaticamente le configurazioni, siano conformi allo stato desiderato e segnalino la propria conformità. Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull. Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

Configurazione stato di Automazione di Azure introduce in [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) lo stesso livello di gestione offerto da Automazione di Azure per gli script di PowerShell.

Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione DSC.

![Screenshot della pagina di accesso di Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Import reporting data into Azure Monitor logs

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro Log Analytics. To learn how to send State Configuration status data to your Log Analytics workspace, see [Forward Azure Automation State Configuration reporting data to Azure Monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Prerequisiti

Please consider the following requirements when using Azure Automation State Configuration (DSC).

### <a name="operating-system-requirements"></a>Operating System Requirements

For nodes running Windows, the following versions are supported:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

The [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) standalone product sku does not contain an implementation of Desired State Configuraion so it cannot be managed by PowerShell DSC or Azure Automation State Configuration.

For nodes running Linux, the following distros/versions are supported:

The DSC Linux extension supports all the Linux distributions listed under [Supported Linux Distributions](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>DSC requirements

For all Windows nodes running in Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) will be installed during onboarding.  For nodes running Windows Server 2012 and Windows 7, [WinRM will be enabled](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

For all Linux nodes running in Azure, [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) will be installed during onboarding.

### <a name="network-planning"></a>Configure private networks

If your nodes are located within a private network, the following port and URLs are required for State Configuration (DSC) to communicate with Automation:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

This provides network connectivity for the managed node to communicate with Azure Automation.
If you are using DSC resources that communicate between nodes, such as the [WaitFor* resources](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), you will also need to allow traffic between nodes.
See the documentation for each DSC resource to understand those network requirements.

#### <a name="proxy-support"></a>Proxy Support

Proxy support for the DSC agent is available in Windows version 1809 and later.
To configure this option, set the value for **ProxyURL** and **ProxyCredential** in the [metaconfiguration script](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) used to register nodes.
Proxy is not available in DSC for previous versions of Windows.

For Linux nodes, the DSC agent supports proxy and will utilize the http_proxy variable to determine the url.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure State Configuration network ranges and namespace

È consigliabile usare gli indirizzi elencati quando si definiscono eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti Orientali   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione.
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.
>
> È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione alla configurazione dello stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sull'onboarding dei nodi, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
