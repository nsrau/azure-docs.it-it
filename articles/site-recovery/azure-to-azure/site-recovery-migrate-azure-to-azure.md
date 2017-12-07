---
title: Eseguire la migrazione di macchine virtuali IaaS di Azure tra aree di Azure | Documentazione Microsoft
description: "Utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery

Seguire le indicazioni di questo articolo se si vuole eseguire la migrazione di macchine virtuali (VM, Virtual Machine) di Azure da un'area di Azure a un'altra tramite il servizio [Site Recovery](../site-recovery-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Sono necessarie le VM IaaS di cui eseguire la migrazione.

## <a name="deployment-steps"></a>Passaggi di distribuzione

1. [Creare un insieme di credenziali](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Abilitare la replica](azure-to-azure-tutorial-enable-replication.md#enable-replication) per le VM di cui eseguire la migrazione e scegliere Azure come origine. La replica nativa di VM di Azure tramite dischi gestiti non è attualmente supportata.
3. [Eseguire un failover](../site-recovery-failover.md). Dopo il completamento della replica iniziale, è possibile eseguire un failover da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](../site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altri scenari di replica, vedere [Informazioni su Site Recovery](../site-recovery-overview.md)
