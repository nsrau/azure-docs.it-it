---
title: Piattaforme supportate nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento offre un elenco di sistemi operativi Windows e Linux supportati nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 3b57cacec729bd2f2dd4acdbb9c15e69ab9f5c85
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="supported-platforms-in-azure-security-center"></a>Piattaforme supportate nel Centro sicurezza di Azure
Il monitoraggio dello stato della sicurezza e le indicazioni in merito sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Windows
Sistemi operativi Windows supportati:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Linux
Sistemi operativi Linux supportati:

* Versioni di Ubuntu 12.04, 14.04, 16.04, 16.10
* Versioni di Debian 7, 8
* Versioni di CentOS 6.\*, 7.*
* Versioni di Red Hat Enterprise Linux (RHEL) 6.\*, 7.*
* Versioni di SUSE Linux Enterprise Server (SLES) 11 SP4+, 12.*
* Versioni di Oracle Linux 6.\*, 7.*

> [!NOTE]
> L'analisi comportamentali della macchina virtuale non è ancora disponibile per i sistemi operativi Linux.
>
>

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli Web e di lavoro dei servizi cloud in esecuzione negli slot di produzione. Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure
- [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis): altre informazioni sull'analisi comportamentale della macchina virtuale e sull'analisi della memoria dump di arresto anomalo del sistema nel Centro sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure
