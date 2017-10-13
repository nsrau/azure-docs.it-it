---
title: Eseguire la replica di VM Hyper-V in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come orchestrare la replica, il failover e il ripristino di macchine virtuali locali Hyper-V in Azure.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Eseguire la replica di macchine virtuali Hyper-V (senza VMM) in Azure 

> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure classico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Gestione risorse](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Questo articolo fornisce una panoramica dei passaggi necessari per eseguire la replica di macchine virtuali Hyper-V locali in Azure usando [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure. In questa distribuzione le macchine virtuali Hyper-V non vengono gestite tramite la soluzione Virtual Machine Manager (VMM) di System Center.


Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-architecture-and-prerequisites"></a>Passaggio 1: Esaminare l'architettura e i prerequisiti

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e assicurarsi di comprendere quali componenti è necessario distribuire

Andare a [Passaggio 1: Esaminare l'architettura](hyper-v-site-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Passaggio 2: Esaminare i prerequisiti

Assicurarsi che siano rispettati i prerequisiti per ogni componente della distribuzione:

- **Prerequisiti di Azure**: sono necessari un account Microsoft Azure, reti di Azure e account di archiviazione.
- **Prerequisiti di Hyper-V in locale**: assicurarsi che gli host Hyper-V siano pronti per la distribuzione di Site Recovery.
- **VM replicate**: le VM da replicare devono essere conformi ai requisiti di Azure.

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](hyper-v-site-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Passaggio 3: Pianificare la capacità

Se si sta eseguendo una distribuzione completa, è necessario individuare le risorse di replica richieste. A tale scopo, sono disponibili due strumenti. Andare al passaggio 2. Se si sta eseguendo una configurazione rapida per testare l'ambiente, è possibile ignorare questo passaggio.

Andare a [Passaggio 3: Pianificare la capacità](hyper-v-site-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Passaggio 4: Pianificare la rete

È necessario eseguire alcune attività di pianificazione per garantire che le VM di Azure siano connesse alle reti dopo il failover e che abbiano gli indirizzi IP appropriati.

Andare a [Passaggio 4: Pianificare la rete](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Passaggio 5: Preparare le risorse di Azure

Configurare le reti e le risorse di archiviazione di Azure prima di iniziare. È possibile eseguire queste operazioni durante la distribuzione, ma si consiglia di farlo prima di iniziare.

Andare a [Passaggio 5: Preparare Azure](hyper-v-site-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-hyper-v"></a>Passaggio 6: Preparare Hyper-V

Assicurarsi che i server Hyper-V soddisfino i requisiti di distribuzione di Site Recovery.

Andare a [Passaggio 6: Preparare Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Passaggio 7: Configurare un insieme di credenziali

È necessario configurare un insieme di credenziali di Servizi di ripristino per orchestrare e gestire la replica. Quando si configura l'insieme di credenziali, specificare gli elementi da replicare e la posizione in cui eseguire la replica.

Andare a [Passaggio 7: Creare un insieme di credenziali](hyper-v-site-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Passaggio 8: Configurare le impostazioni di origine e di destinazione

Configurare l'origine e la destinazione usate per la replica. La configurazione delle impostazioni di origine comprende l'aggiunta di host Hyper-V a un sito Hyper-V, l'installazione del provider di Site Recovery e dell'agente di Servizi di ripristino in ogni host Hyper-V e la registrazione del sito nell'insieme di credenziali di Servizi di ripristino.

Andare a [Passaggio 8: Configurare l'origine e la destinazione](hyper-v-site-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Passaggio 9: Configurare i criteri di replica

I criteri di replica vengono configurati per specificare le impostazioni di replica per le VM Hyper-V nell'insieme di credenziali.

Andare a [Passaggio 9: Configurare i criteri di replica](hyper-v-site-walkthrough-replication.md)


## <a name="step-10-enable-replication"></a>Passaggio 10: Abilitare la replica

Una volta creati i criteri di replica, dopo l'abilitazione viene eseguita la replica iniziale delle VM.

Andare a [Passaggio 10: Abilitare la replica](hyper-v-site-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Passaggio 11: Eseguire un failover di test

Dopo il completamento della replica iniziale e con la replica differenziale in esecuzione, è possibile eseguire un failover di test per verificare che tutto funzioni come previsto.

Andare a [Passaggio 11: Eseguire un failover di test](hyper-v-site-walkthrough-test-failover.md)
