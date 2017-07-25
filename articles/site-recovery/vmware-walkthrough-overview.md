---
title: Eseguire la replica di VM VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Viene fornita una panoramica dei passaggi per la replica in Azure di carichi di lavoro in esecuzione in VM VMware
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>Replicare VM VMware in Azure con Site Recovery

Questo articolo fornisce una panoramica dei passaggi necessari per eseguire la replica di macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


![Processo di distribuzione](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**Figura 1: Riepilogo del processo di distribuzione**

## <a name="step-1-review-architecture-and-prerequisites"></a>Passaggio 1: Esaminare l'architettura e i prerequisiti

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e assicurarsi di comprendere quali componenti è necessario distribuire

Andare a [Passaggio 1: Esaminare l'architettura](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Passaggio 2: Esaminare i prerequisiti

Assicurarsi che siano rispettati i prerequisiti per ogni componente della distribuzione:

- **Prerequisiti di Azure**: sono necessari un account Microsoft Azure, reti di Azure e account di archiviazione.
- **Componenti locali di Site Recovery**: è necessario un computer che esegue i componenti locali di Site Recovery.
- **Prerequisiti di VMware locale**: è necessario configurare gli account in modo che Site Recovery possa accedere ai server VMware e alle VM.
- **VM replicate**: le VM da replicare devono essere conformi ai requisiti di Azure e avere il componente servizio Mobility installato.

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Passaggio 3: Pianificare la capacità

Se si sta eseguendo una distribuzione completa, è necessario individuare le risorse di replica richieste. A tale scopo, sono disponibili due strumenti. Andare al passaggio 2. Se si sta eseguendo una configurazione rapida per testare l'ambiente, è possibile ignorare questo passaggio.

Andare a [Passaggio 3: Pianificare la capacità](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Passaggio 4: Pianificare la rete

È necessario eseguire alcune attività di pianificazione per garantire che le VM di Azure siano connesse alle reti dopo il failover e che abbiano gli indirizzi IP appropriati.

Andare a [Passaggio 4: Pianificare la rete](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Passaggio 5: Preparare le risorse di Azure

Configurare le reti e le risorse di archiviazione di Azure prima di iniziare. È possibile eseguire queste operazioni durante la distribuzione, ma si consiglia di farlo prima di iniziare.

Andare a [Passaggio 5: Preparare Azure](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>Passaggio 6: Preparare VMware

È necessario configurare gli account che verranno usati da Site Recovery per:

- Accedere ai server di virtualizzazione VMware per rilevare automaticamente le VM.
- Accedere alle VM per installare il servizio Mobility. Ogni VM che si vuole replicare deve avere l'agente del servizio Mobility installato affinché sia possibile abilitare la replica.

Andare a [Passaggio 6: Preparare VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>Passaggio 7: Configurare un insieme di credenziali

È necessario configurare un insieme di credenziali di Servizi di ripristino per orchestrare e gestire la replica. Quando si configura l'insieme di credenziali, specificare gli elementi da replicare e la posizione in cui eseguire la replica.

Andare a [Passaggio 7: Configurare un insieme di credenziali](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Passaggio 8: Configurare le impostazioni di origine e di destinazione

Configurare l'origine e la destinazione usate per la replica. La configurazione delle impostazioni di origine include l'esecuzione dell'installazione unificata per installare i componenti di Site Recovery locali.

Andare a [Passaggio 8: Configurare l'origine e la destinazione](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Passaggio 9: Configurare i criteri di replica

I criteri di replica vengono configurati per specificare le impostazioni di replica per le VM VMware nell'insieme di credenziali.

Andare a [Passaggio 9: Configurare i criteri di replica](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>Passaggio 10: Installare il servizio Mobility

Il servizio Mobility deve essere installato in ogni VM da replicare. È possibile configurare il servizio con l'installazione push o pull.

Andare a [Passaggio 10: Installare il servizio Mobility](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>Passaggio 11: Abilitare la replica

Quando il servizio Mobility è in esecuzione in una VM, è possibile abilitare la replica. Dopo l'abilitazione, viene eseguita la replica iniziale delle VM.

Andare a [Passaggio 11: Abilitare la replica](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>Passaggio 12: Eseguire un failover di test

Dopo il completamento della replica iniziale e con la replica differenziale in esecuzione, è possibile eseguire un failover di test per verificare che tutto funzioni come previsto.

Andare a [Passaggio 12: Eseguire un failover di test](vmware-walkthrough-test-failover.md)

