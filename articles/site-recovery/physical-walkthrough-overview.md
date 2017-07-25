---
title: Eseguire la replica di server fisici locali in Azure con Azure Site Recovery | Microsoft Docs
description: Offre una panoramica della procedura di replica in Azure dei carichi di lavoro in esecuzione in server fisici Windows/Linux locali con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Replicare server fisici in Azure con Site Recovery

Questo articolo fornisce una panoramica dei passaggi necessari per eseguire la replica di server fisici Windows/Linux locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


## <a name="step-1-review-architecture-and-prerequisites"></a>Passaggio 1: Esaminare l'architettura e i prerequisiti

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e assicurarsi di comprendere quali componenti sono necessari per configurare la distribuzione.

Andare a [Passaggio 1: Esaminare l'architettura](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Passaggio 2: Esaminare i prerequisiti

Assicurarsi che siano rispettati i prerequisiti per ogni componente della distribuzione:

- **Prerequisiti di Azure**: sono necessari un account Microsoft Azure, reti di Azure e account di archiviazione.
- **Componenti locali di Site Recovery**: è necessario un computer che esegue i componenti locali di Site Recovery.
- **Computer replicati**: i server di cui si vuole eseguire la replica devono essere conformi ai requisiti locali e di Azure.

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Passaggio 3: Pianificare la capacità

Se si sta eseguendo una distribuzione completa, è necessario individuare le risorse di replica richieste. Se si sta eseguendo una configurazione rapida per testare l'ambiente, è possibile ignorare questo passaggio.

Andare a [Passaggio 3: Pianificare la capacità](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Passaggio 4: Pianificare la rete

È necessario eseguire alcune attività di pianificazione per garantire che le VM di Azure siano connesse alle reti dopo il failover e che abbiano gli indirizzi IP appropriati.

Andare a [Passaggio 4: Pianificare la rete](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Passaggio 5: Preparare le risorse di Azure

Configurare le reti e le risorse di archiviazione di Azure prima di iniziare. 

Andare a [Passaggio 5: Preparare Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>Passaggio 6: Configurare un insieme di credenziali

Si configura un insieme di credenziali di Servizi di ripristino per orchestrare e gestire la replica. Quando si configura l'insieme di credenziali, specificare gli elementi da replicare e la posizione in cui eseguire la replica.

Andare a [Passaggio 6: Configurare un insieme di credenziali](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>Passaggio 7: Configurare le impostazioni di origine e di destinazione

Configurare le impostazioni per il sito di origine e di destinazione (Azure). Le impostazioni di origine includono l'esecuzione dell'installazione unificata per installare i componenti di Site Recovery locali.

Andare a [Passaggio 7: Configurare l'origine e la destinazione](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>Passaggio 8: Configurare un criterio di replica

Si configura un criterio per specificare come replicare i server fisici.

Andare a [Passaggio 8: Configurare i criteri di replica](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>Passaggio 9: Installare il servizio Mobility

Il servizio Mobility deve essere installato in ogni server da replicare. È possibile configurare il servizio con l'installazione push o pull.

Andare a [Passaggio 9: Installare il servizio Mobility](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>Passaggio 10: Abilitare la replica

Quando il servizio Mobility è in esecuzione in un server, è possibile abilitare la replica. Dopo l'abilitazione, viene eseguita la replica iniziale delle VM.

Andare a [Passaggio 10: Abilitare la replica](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Passaggio 11: Eseguire un failover di test

Dopo il completamento della replica iniziale e con la replica differenziale in esecuzione, è possibile eseguire un failover di test per verificare che tutto funzioni come previsto.

Andare a [Passaggio 11: Eseguire un failover di test](physical-walkthrough-test-failover.md)


