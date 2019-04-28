---
title: Eseguire la migrazione di macchine virtuali di Azure a Managed Disks | Microsoft Docs
description: Eseguire la migrazione di macchine virtuali di Azure create usando dischi non gestiti negli account di archiviazione per l'uso in Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12cd1caa4cb96dbd5862776589d4a34aeb294ca1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763750"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Eseguire la migrazione di macchine virtuali di Azure a Managed Disks in Azure

Il servizio Azure Managed Disks semplifica la gestione dell'archiviazione, eliminando la necessità di gestire separatamente gli account.  È anche possibile eseguire la migrazione delle macchine virtuali di Azure esistenti a Managed Disks per trarre vantaggio dalla maggiore affidabilità delle macchine virtuali in un set di disponibilità. Assicura che i dischi di macchine virtuali diverse in un set di disponibilità siano sufficientemente isolati tra loro per evitare singoli punti di errore. Colloca automaticamente i dischi di macchine virtuali differenti in un set di disponibilità in diverse unità di scala di archiviazione (indicatori) per limitare l'impatto degli errori di una singola unità causati da anomalie hardware e software.
In base alle esigenze specifiche, è possibile scegliere tra quattro tipi di opzioni di archiviazione. Per informazioni sui tipi di dischi disponibili, vedere l'articolo [Selezionare un tipo di disco](disks-types.md)

## <a name="migrate-scenarios"></a>Eseguire la migrazione di scenari

È possibile eseguire la migrazione a Managed Disks negli scenari seguenti:

| **Eseguire la migrazione...**                                            | **Link alla documentazione**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Conversione di macchine virtuali autonome e incluse in un set di disponibilità in dischi gestiti   | [Convertire le macchine virtuali per usare i dischi gestiti](convert-unmanaged-to-managed-disks.md) |
| Una singola macchina virtuale dal modello di distribuzione classica a Resource Manager su dischi gestiti     | [Creare una macchina virtuale da un disco rigido virtuale classico](create-vm-specialized-portal.md)  | 
| Tutte le macchine virtuali in una rete virtuale dal modello di distribuzione classica a Resource Manager su dischi gestiti     | [Eseguire la migrazione di risorse IaaS dal modello di distribuzione classico a Resource Manager](migration-classic-resource-manager-ps.md) e quindi [convertire una macchina virtuale da dischi non gestiti in dischi gestiti](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Managed Disks](managed-disks-overview.md)
- Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
