---
title: Strumenti della community per la gestione del servizio Azure durante la migrazione di Azure Resource Manager
description: In questo articolo vengono illustrati gli strumenti forniti dalla community per agevolare la migrazione delle risorse IaaS dalla gestione del servizio Azure allo stack di Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 032a7068cce78036caec587bfc53abddf5a36659


---
# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Strumenti della community per la gestione del servizio Azure durante la migrazione di Azure Resource Manager
In questo articolo vengono illustrati gli strumenti forniti dalla community per agevolare la migrazione delle risorse IaaS dalla gestione del servizio Azure allo stack di Azure Resource Manager.

> [!NOTE]
> Questi strumenti non sono supportati ufficialmente dal Supporto tecnico Microsoft. Sono quindi open source in Github e perciò sono graditi PR con suggerimenti per correzioni o altri scenari. Per segnalare un problema, usare l'apposita funzionalità Github.
> 
> La migrazione con questi strumenti comporta tempi di inattività della macchina virtuale classica. Se si è interessati a una modalità di migrazione supportata dalla piattaforma, vedere 
> 
> * [Migrazione di risorse IaaS supportata dallo stack del modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager usando Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
Questo modulo di script PowerShell consente di eseguire la migrazione di una **singola** macchina virtuale (VM) dallo stack della gestione del servizio Azure allo stack di Azure Resource Manager. 

[Collegamento alla documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz è un'opzione aggiuntiva che consente di eseguire la migrazione di un set completo di risorse IaaS della gestione del servizio Azure alle risorse IaaS di Azure Resource Manager. La migrazione può avvenire all'interno della stessa sottoscrizione o fra sottoscrizioni e tipi di sottoscrizione diversi (ad esempio: sottoscrizioni CSP).

[Collegamento alla documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)




<!--HONumber=Nov16_HO3-->


