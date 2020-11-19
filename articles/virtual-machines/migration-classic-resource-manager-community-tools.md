---
title: Strumenti della community-spostare le risorse classiche in Azure Resource Manager
description: Questo articolo illustra gli strumenti forniti dalla community per semplificare la migrazione di risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 75ca87836a429a81f28b133e329291143c03ea68
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904775"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Strumenti della community per la migrazione delle risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager

> [!IMPORTANT]
> Attualmente, circa il 90% delle VM IaaS USA [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partire dal 28 febbraio 2020, le macchine virtuali classiche sono state deprecate e saranno completamente ritirate il 1 ° marzo 2023. [Scopri di più]( https://aka.ms/classicvmretirement) su questa deprecazione e sul [modo in cui influiscono sull'utente](classic-vm-deprecation.md#how-does-this-affect-me).

Questo articolo illustra gli strumenti forniti dalla community per supportare la migrazione di risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager.

> [!NOTE]
> Questi strumenti non sono supportati ufficialmente dal Supporto tecnico Microsoft. Sono quindi open source in GitHub e perciò sono graditi PR con suggerimenti per correzioni o altri scenari. Per segnalare un problema, usare l'apposita funzionalità GitHub.
> 
> La migrazione con questi strumenti comporta tempi di inattività della macchina virtuale classica. Se si è interessati a una modalità di migrazione supportata dalla piattaforma, vedere 
> 
>   * [Migrazione di risorse IaaS supportata dallo stack del modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager usando Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Si tratta di una raccolta di strumenti di supporto creata come parte di migrazioni enterprise da Azure Service Management ad Azure Resource Manager. Questo strumento consente di replicare l'infrastruttura in un'altra sottoscrizione che può essere usata per testare la migrazione e risolvere eventuali problemi prima di eseguire la migrazione nella sottoscrizione di produzione.

[Collegamento alla documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz è un'opzione aggiuntiva che consente di eseguire la migrazione di un set completo di risorse IaaS classiche alle risorse IaaS di Azure Resource Manager. La migrazione può avvenire all'interno della stessa sottoscrizione o fra sottoscrizioni e tipi di sottoscrizione diversi (ad esempio: sottoscrizioni CSP).

[Collegamento alla documentazione dello strumento](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md)
