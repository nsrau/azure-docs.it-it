---
title: Eseguire la replica delle macchine virtuali Hyper-V nei cloud VMM in Azure con Azure Site Recovery | Microsoft Docs
description: Viene fornita una panoramica su come replicare le macchine virtuali Hyper-V nei cloud VMM in Azure tramite il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Eseguire la replica di macchine virtuali Hyper-V nei cloud VMM in Azure tramite Site Recovery nel Portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmm-to-azure.md)
> * [Azure classico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell - Classica](site-recovery-deploy-with-powershell.md)


Questo articolo fornisce una panoramica della procedura necessaria per la replica di macchine virtuali Hyper-V locali gestite in cloud System Center Virtual Machine Manager (VMM) in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Passaggio 1: Esaminare l'architettura dello scenario

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e assicurarsi di comprendere quali componenti è necessario distribuire.

Andare a [Passaggio 1: Esaminare l'architettura](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Passaggio 2: Esaminare i prerequisiti e le limitazioni

Assicurarsi di comprendere i prerequisiti e le limitazioni della distribuzione.

**Prerequisiti di Azure**: sono necessari un account Microsoft Azure, reti di Azure e account di archiviazione.
**Server VMM locali e host Hyper-V**: assicurarsi che i server VMM e gli host Hyper-V siano conformi e preparati per la distribuzione di Site Recovery.
**Macchine virtuali replicate**: assicurarsi che la macchine virtuali da replicare siano conformi ai requisiti di Azure.

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Passaggio 3: Pianificare la capacità

Se si sta eseguendo una distribuzione completa, è necessario individuare le risorse di replica richieste. A tale scopo, sono disponibili due strumenti. Se si sta eseguendo una configurazione rapida per testare l'ambiente, è possibile ignorare questo passaggio.

Andare a [Passaggio 3: Pianificare la capacità](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Passaggio 4: Pianificare la rete

È necessario eseguire una pianificazione della rete per garantire la configurazione del mapping di rete quando si distribuisce lo scenario, la connessione delle macchine virtuali di Azure alle reti virtuali di Azure dopo il failover e la relativa assegnazione di indirizzi IP idonei.

Andare a [Passaggio 4: Pianificare la rete](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>Passaggio 5: Preparare le risorse di Azure

Configurare un account Azure, reti e archiviazione. È possibile eseguire queste operazioni durante la distribuzione, ma si consiglia di farlo prima di iniziare.

Andare al [Passaggio 5: Preparare Azure](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Passaggio 6: Preparare VMM e Hyper-V

Preparare i server VMM locali e gli host Hyper-V per la distribuzione di Site Recovery.

Andare al [Passaggio 6: Preparare server locali](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Passaggio 7: Configurare un insieme di credenziali

Configurare un insieme di credenziali dei Servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.

[Passaggio 7: Configurare un insieme di credenziali](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Passaggio 8: Configurare le impostazioni di origine e di destinazione

Configurare le località di replica di origine e di destinazione. Aggiungere i server VMM all'insieme di credenziali e scaricare i file di installazione per i componenti di Site Recovery. Eseguire il programma di installazione del provider di Azure Site Recovery nel server VMM. Il provider viene installato nel server VMM e il server viene registrato nell'insieme di credenziali. Installare l'agente di Servizi di ripristino di Azure in ogni host Hyper-V.

Andare al [Passaggio 8: Configurare le impostazioni di origine e di destinazione](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>Passaggio 9: Configurare il mapping di rete

Mappare le reti della macchina virtuale VMM locali a reti virtuali di Azure. Dopo il failover, le macchine virtuali di Azure vengono create nella rete di Azure mappata alla rete della macchina virtuale locale in cui si trova Hyper-V di origine.

Andare al [Passaggio 9: Configurare il mapping di rete](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>Passaggio 10: Configurare un criterio di replica

Specificare come verranno replicate le macchine virtuali locali in Azure.

Andare al [Passaggio 10: Configurare un criterio di replica](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>Passaggio 11: Abilitare la replica per le macchine virtuali

Selezionare le macchine virtuali da replicare. L'abilitazione di una macchina virtuale per la replica attiva la replica iniziale in Azure, seguita dalla replica differenziale in corso.

Andare a [Passaggio 11: Abilitare la replica](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>Passaggio 12: Eseguire un failover di test

Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

Andare a [Passaggio 12: Eseguire un failover di test](vmm-to-azure-walkthrough-test-failover.md)


