---
title: Esaminare l'architettura per la replica di VM di Azure tra aree di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dei componenti e dell'architettura usati per la replica di VM di Azure tra aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: f9cd57e47a8463440148b2bcc6c21beacd54382a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Architettura della replica da Azure ad Azure


Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di macchine virtuali (VM) di Azure tra aree di Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>La replica di VM di Azure con il servizio Site Recovery è attualmente disponibile in anteprima.



## <a name="architectural-components"></a>Componenti dell'architettura

Il grafico seguente offre una visualizzazione generale di un ambiente di macchine virtuali di Azure in un'area specifica, in questo esempio l'area Stati Uniti orientali. In un ambiente di VM di Azure:
- Le app possono essere in esecuzione in macchine virtuali con dischi distribuiti tra gli account di archiviazione.
- Le VM possono essere incluse in una o più subnet in una rete virtuale.


**Replica da Azure ad Azure**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processo di replica

### <a name="step-1"></a>Passaggio 1

Quando si abilita la replica delle macchine virtuali di Azure, vengono automaticamente create le risorse mostrate di seguito nell'area di destinazione, in base alle impostazione dell'area di origine. È possibile personalizzare le impostazioni delle risorse di destinazione nel modo necessario. 

![Abilitare il processo di replica - Passaggio 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Risorsa** | **Dettagli**
--- | ---
**Gruppo di risorse di destinazione** | Gruppo di risorse a cui appartengono le VM replicate dopo il failover.
**Rete virtuale di destinazione** | Rete virtuale in cui si trovano le VM replicate dopo il failover. Un mapping di rete viene creato tra le reti virtuali di origine e di destinazione e viceversa.
**Account di archiviazione della cache** | Prima che le modifiche apportate alle macchine virtuali di origine vengano replicate in un account di archiviazione di destinazione, vengono registrate e inviate all'account di archiviazione della cache nella posizione di destinazione. In questo modo si assicura un impatto minimo sulle app di produzione in esecuzione nella VM.
**Account di archiviazione di destinazione**  | Account di archiviazione nel percorso di destinazione in cui vengono replicati i dati.
**Set di disponibilità di destinazione**  | Set di disponibilità in cui si trovano le VM replicate dopo il failover.

### <a name="step-2"></a>Passaggio 2

Poiché la replica è abilitata, il servizio Mobility dell'estensione di Site Recovery viene installato automaticamente nella macchina virtuale:

1. La VM viene registrata in Site Recovery.

2. La replica continua viene configurata per la VM. Le operazioni di scrittura dei dati nei dischi delle macchine virtuali vengono trasferite continuamente nell'account di archiviazione della cache nella posizione di origine.

   ![Abilitare il processo di replica - Passaggio 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Site Recovery non necessita mai della connettività in ingresso per la VM. È necessaria solo la connettività in uscita verso gli URL/indirizzi IP del servizio Site Recovery, gli URL/indirizzi IP di autenticazione di Office 365 e gli indirizzi IP dell'account di archiviazione della cache.

### <a name="step-3"></a>Passaggio 3

Una volta attivata la replica continua, le operazioni di scrittura nei dischi vengono immediatamente trasferite nell'account di archiviazione della cache. Site Recovery elabora i dati e li invia all'account di archiviazione di destinazione. Dopo l'elaborazione dei dati, vengono generati punti di ripristino nell'account di archiviazione di destinazione a intervalli di pochi minuti.

## <a name="failover-process"></a>Processo di failover

Quando si avvia un failover, le macchine virtuali vengono create nel gruppo di risorse di destinazione, nella rete virtuale di destinazione, nella subnet di destinazione e nel set di disponibilità di destinazione. Durante un failover è possibile usare qualsiasi punto di ripristino.

![Processo di failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la matrice di supporto Seguire l'esercitazione per abilitare la replica delle macchine virtuali di Azure in un'area secondaria.
Eseguire un failover e un failback.