---
title: Approfondimento tecnico della migrazione dal modello classico al Azure Resource Manager
description: Approfondimento tecnico sulla migrazione supportata dalla piattaforma di risorse dal modello di distribuzione classica a Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 75732cf58a68948bf225e15e9227a3fa7592e738
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914926"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager

> [!IMPORTANT]
> Attualmente, circa il 90% delle VM IaaS USA [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partire dal 28 febbraio 2020, le macchine virtuali classiche sono state deprecate e saranno completamente ritirate il 1 ° marzo 2023. [Scopri di più]( https://aka.ms/classicvmretirement) su questa deprecazione e sul [modo in cui influiscono sull'utente](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Sono descritte informazioni approfondite sulla migrazione del modello di distribuzione classica di Azure al modello di distribuzione di Azure Resource Manager. Le risorse vengono esaminate a livello di risorsa e di funzionalità per aiutare a comprendere come la piattaforma Azure esegue la migrazione di risorse tra i due modelli di distribuzione. Per altre informazioni, leggere l'articolo relativo all'annuncio del servizio, [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
