---
title: Panoramica della piattaforma DSC di Automazione di Azure
description: Panoramica della piattaforma DSC (Desired State Configuration) di Automazione di Azure, dei termini a essa relativi e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-dsc-overview"></a>Panoramica della piattaforma DSC di Automazione di Azure

Automation DSC per Azure è un servizio di Azure che consente di scrivere, gestire e compilare [configurazioni](https://msdn.microsoft.com/powershell/dsc/configurations) PowerShell DSC (Desired State Configuration), importare [risorse DSC](https://msdn.microsoft.com/powershell/dsc/resources) e assegnare configurazioni ai nodi di destinazione, il tutto nel cloud.

## <a name="why-use-azure-automation-dsc"></a>Perché usare Automation DSC per Azure

Automation DSC per Azure offre diversi vantaggi rispetto all'uso di DSC al di fuori di Azure.

### <a name="built-in-pull-server"></a>Server di pull predefinito

Automazione di Azure fornisce un [server di pull DSC](https://msdn.microsoft.com/powershell/dsc/pullserver) in modo che i nodi di destinazione ricevano automaticamente le configurazioni, conformemente allo stato desiderato, e segnalino la propria conformità.
Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull.
Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

Automation DSC per Azure introduce infatti in [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) lo stesso livello di gestione offerto da Automazione di Azure per gli script di PowerShell.

Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione DSC.

![Screenshot del pannello Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importare i dati dei report in Log Analytics

I nodi gestiti con Automation DSC per Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito.
È possibile configurare Automation DSC per Azure per inviare questi dati all'area di lavoro di Log Analytics.
Per informazioni su come inviare i dati sullo stato di DSC all'area di lavoro Log Analytics, vedere [Inoltrare i dati dei report di Automation DSC per Azure a Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Video introduttivo

Si preferisce guardare che leggere? Guardare il video di seguito del maggio 2015, quando Azure Automation DSC è stato annunciato per la prima volta.

>[!NOTE]
>Anche se i concetti e il ciclo di vita descritti in questo video sono corretti, Automation DSC per Azure ha fatto molti progressi dalla registrazione del video.
>È ora disponibile a livello generale, ha un'interfaccia utente molto più estesa nel portale di Azure e supporta molte funzionalità aggiuntive.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come caricare i nodi da gestire con Automation DSC per Azure, vedere [Onboarding di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)
* Per iniziare a usare Automation DSC per Azure, vedere [Introduzione ad Automation DSC per Azure](automation-dsc-getting-started.md)
* Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Automation DSC per Azure](automation-dsc-compile.md)
* Per informazioni di riferimento sui cmdlet di PowerShell per Automation DSC per Azure, vedere [Azure Automation DSC cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet di Automation DSC per Azure)
* Per informazioni sui prezzi, vedere [Prezzi di Automation DSC per Azure](https://azure.microsoft.com/pricing/details/automation/)
* Per vedere un esempio dell'uso di Automation DSC per Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua in macchine virtuali IaaS tramite Automation DSC per Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
