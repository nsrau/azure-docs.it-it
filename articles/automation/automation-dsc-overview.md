---
title: Panoramica di Configurazione stato di Automazione di Azure
description: Panoramica di Configurazione stato di Automazione di Azure (DSC), dei relativi termini e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1f28f642d1a5fc30055c73a4b7d60c076c83d204
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250102"
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

### <a name="import-reporting-data-into-log-analytics"></a>Importare i dati dei report in Log Analytics

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro di Log Analytics. Per informazioni su come inviare i dati sullo stato di Configurazione stato all'area di lavoro Log Analytics, vedere [Inoltrare i dati di report di Configurazione stato di Automazione di Azure a Log Analytics](automation-dsc-diagnostics.md).

## <a name="network-planning"></a>Configurare la rete

La porta e gli URL seguenti sono necessari affinché Configurazione stato (DSC) comunichi con Automazione:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

È consigliabile usare gli indirizzi elencati quando si definiscono eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
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

## <a name="introduction-video"></a>Video introduttivo

Si preferisce guardare che leggere? Guardare il video seguente del mese di maggio 2015, pubblicato quando Configurazione stato di Automazione di Azure è stato annunciato per la prima volta.

> [!NOTE]
> Anche se i concetti e il ciclo di vita descritti in questo video sono corretti, Configurazione stato di Automazione di Azure ha compiuto molti progressi da quando il video è stato registrato. È ora disponibile a livello generale, ha un'interfaccia utente molto più estesa nel portale di Azure e supporta molte funzionalità aggiuntive.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sull'onboarding dei nodi, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)