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
ms.openlocfilehash: dd898397b4aaec2e62558d12a3547f7b61d6d3fd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533463"
---
# <a name="azure-automation-state-configuration-overview"></a>Panoramica di Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure è un servizio di Azure che consente di scrivere, gestire e compilare le [configurazioni](/powershell/dsc/configurations) di PowerShell DSC (Desired State Configuration), importare [risorse DSC](/powershell/dsc/resources) e assegnare configurazioni ai nodi di destinazione, il tutto nel cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Perché usare Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure offre diversi vantaggi rispetto all'uso di DSC all'esterno di Azure.

### <a name="built-in-pull-server"></a>Server di pull predefinito

Configurazione stato di Automazione di Azure fornisce un server di pull DSC simile alla [funzionalità di Windows DSC-Service](/powershell/dsc/pullserver) in modo che i nodi di destinazione ricevano automaticamente le configurazioni, siano conformi allo stato desiderato e segnalino la propria conformità. Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull. Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

Configurazione stato di Automazione di Azure introduce in [PowerShell Desired State Configuration](/powershell/dsc/overview) lo stesso livello di gestione offerto da Automazione di Azure per gli script di PowerShell.

Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione DSC.

![Screenshot della pagina di accesso di Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importare i dati di report nei log di monitoraggio di Azure

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro Log Analytics. Per informazioni su come inviare i dati sullo stato della configurazione dello stato nell'area di lavoro Log Analytics, vedere [inoltrare i dati dei report di configurazione dello stato di automazione di Azure ai log di monitoraggio](automation-dsc-diagnostics.md)

## <a name="prerequisites"></a>Prerequisiti

Quando si usa la configurazione dello stato di automazione di Azure (DSC), considerare i requisiti seguenti.

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

Lo SKU del prodotto autonomo [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) non contiene un'implementazione della configurazione dello stato desiderato, quindi non può essere gestito tramite PowerShell DSC o la configurazione dello stato di automazione di Azure.

Per i nodi che eseguono Linux, sono supportate le distribuzioni/versioni seguenti:

L'estensione DSC per Linux supporta tutte le distribuzioni [di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , tranne:

Distribuzione | Versione
-|-
Debian  | tutte le versioni
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>Requisiti DSC

Per tutti i nodi Windows in esecuzione in Azure, [WMF 5,1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) verrà installato durante l'onboarding.  Per i nodi che eseguono Windows Server 2012 e Windows 7, [WinRM sarà abilitato](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Per tutti i nodi Linux in esecuzione in Azure, [PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) verrà installato durante l'onboarding.

### <a name="network-planning"></a>Configurare le reti private

Se i nodi si trovano all'interno di una rete privata, la porta e gli URL seguenti sono necessari per la comunicazione con l'automazione dello stato (DSC):

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

In questo modo si fornisce la connettività di rete per la comunicazione tra il nodo gestito e automazione di Azure.
Se si usano risorse DSC che comunicano tra i nodi, ad esempio le [risorse di aspetter *](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), sarà necessario anche consentire il traffico tra i nodi.
Per informazioni sui requisiti di rete, vedere la documentazione relativa a ogni risorsa DSC.

#### <a name="proxy-support"></a>Supporto proxy

Il supporto del proxy per l'agente DSC è disponibile nella versione 1809 e successive di Windows.
Per configurare questa opzione, impostare il valore di **ProxyURL** e **ProxyCredential** nello [script](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) di metaconfigurazione usato per registrare i nodi.
Il proxy non è disponibile in DSC per le versioni precedenti di Windows.

Per i nodi Linux, l'agente DSC supporta il proxy e utilizzerà la variabile http_proxy per determinare l'URL.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Spazi dei nomi e intervalli di rete di configurazione stato di Azure

È consigliabile usare gli indirizzi elencati quando si definiscono eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| East US   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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

- Per iniziare, vedere [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sull'onboarding dei nodi, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
