---
title: Eseguire la replica di macchine virtuali Hyper-V in un sito VMM secondario con Azure Site Recovery | Microsoft Docs
description: Fornisce una panoramica della replica di VM Hyper-V in un sito VMM secondario tramite il portale di Azure.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Eseguire la replica di macchine virtuali Hyper-V in cloud VMM in un sito VMM secondario

> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmm-to-vmm.md)
> * [Portale classico](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Gestione risorse](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Questo articolo fornisce una panoramica della procedura necessaria per la replica di macchine virtuali Hyper-V locali gestite in cloud System Center Virtual Machine Manager (VMM) in un sito VMM secondario tramite [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Passaggio 1: Esaminare l'architettura dello scenario

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e assicurarsi di comprendere quali componenti è necessario distribuire.

Andare al [Passaggio 1: Esaminare l'architettura](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Passaggio 2: Esaminare i prerequisiti e le limitazioni

Assicurarsi di comprendere i prerequisiti e le limitazioni della distribuzione.

**Prerequisiti di Azure**: per orchestrare e gestire la replica sono necessari una sottoscrizione di Microsoft Azure e un insieme di credenziali di Servizi di ripristino di Azure.
**Server VMM locali e host Hyper-V**: assicurarsi che i server VMM e gli host Hyper-V siano conformi e preparati per la distribuzione di Site Recovery.

Andare al [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>Passaggio 3: Pianificare la rete

È necessario pianificare le risorse di rete, in modo da assicurare che sia possibile configurare il mapping di rete tra reti di macchine virtuali VMM durante la distribuzione dello scenario.

Andare al [Passaggio 3: Pianificare la rete](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Passaggio 4: Preparare VMM e Hyper-V

Preparare i server VMM e gli host Hyper-V per la distribuzione di Site Recovery.

Andare al [Passaggio 4: Preparare VMM e Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>Passaggio 5: Configurare un insieme di credenziali

Configurare un insieme di credenziali dei Servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.

[Passaggio 5: Configurare un insieme di credenziali](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>Passaggio 6: Configurare le impostazioni di origine e destinazione

Configurare le posizioni VMM di replica di origine e di destinazione. Aggiungere i server VMM all'insieme di credenziali e scaricare i file di installazione per i componenti di Site Recovery. Eseguire il programma di installazione del provider di Azure Site Recovery nel server VMM. Il provider viene installato nel server VMM e il server viene registrato nell'insieme di credenziali. Installare l'agente di Servizi di ripristino di Azure in ogni host Hyper-V.

Andare al [Passaggio 6: Configurare le impostazioni di origine e destinazione](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>Passaggio 7: Configurare il mapping di rete

Mappare le reti di macchine virtuali VMM nelle posizioni di origine e di destinazione. Dopo il failover, le VM vengono create nella rete di destinazione mappata alla rete di VM di origine in cui si trova la macchina virtuale Hyper-V di origine.

Andare al [Passaggio 7: Configurare il mapping di rete](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>Passaggio 8: Configurare un criterio di replica

Specificare in che modo le macchine virtuali verranno replicate tra posizioni di VMM.

Andare al [Passaggio 8: Configurare un criterio di replica](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>Passaggio 9: Abilitare la replica per le VM

Selezionare le VM da replicare. L'abilitazione di una VM per la replica attiva la replica iniziale nel sito secondario, seguita dalla replica differenziale in corso.

Andare al [Passaggio 9: Abilitare la replica](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>Passaggio 10: Eseguire un failover di test

Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

Andare al [Passaggio 10: Eseguire un failover di test](vmm-to-vmm-walkthrough-test-failover.md).
