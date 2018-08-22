---
title: Panoramica di Configurazione stato di Automazione di Azure
description: Panoramica di Configurazione stato di Automazione di Azure (DSC), dei relativi termini e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006062"
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

![Screenshot del pannello Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importare i dati dei report in Log Analytics

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro di Log Analytics. Per informazioni su come inviare i dati sullo stato di Configurazione stato all'area di lavoro Log Analytics, vedere [Inoltrare i dati di report di Configurazione stato di Automazione di Azure a Log Analytics](automation-dsc-diagnostics.md).

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