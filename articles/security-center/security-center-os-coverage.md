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
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c33e132037d95fa92fd59a8243a9a8a351ae0224
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="supported-platforms-in-azure-security-center"></a>Piattaforme supportate nel Centro sicurezza di Azure
Il monitoraggio dello stato della sicurezza e le indicazioni in merito sono disponibili per le macchine virtuali create usando i modelli di distribuzione classica e Resource Manager.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>
>

## <a name="supported-platforms-for-windows-vms"></a>Piattaforme supportate per le macchine virtuali Windows
Sistemi operativi Windows supportati:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

> [!NOTE]
>
* Le valutazioni delle vulnerabilità del sistema operativo non sono ancora disponibili per Windows Server 2016.
* Il rilevamento dell'analisi di arresto anomalo è supportato solo per Windows Server 2012 e Windows Server 2012 R2.
>
>

## <a name="supported-platforms-for-linux-vms"></a>Piattaforme supportate per le macchine virtuali Linux
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
- [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis): altre informazioni sull'analisi comportamentale della macchina virtuale e sull'analisi della memoria dump di arresto anomalo del sistema nel Centro sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

