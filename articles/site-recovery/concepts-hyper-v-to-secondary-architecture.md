---
title: Esaminare l'architettura per la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell'architettura per la replica di macchine virtuali Hyper-V locali in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Replica Hyper-V in un sito secondario

Questo articolo descrive i componenti e i processi coinvolti durante la replica in un sito VMM secondario di macchine virtuali Hyper-V locali presenti in cloud System Center Virtual Machine Manager (VMM) tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella e il grafico seguenti offrono una visualizzazione generale dei componenti usati per la replica Hyper-V in un sito secondario.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azzurro** | Sottoscrizione di Azure | Creare un insieme di credenziali di Servizi di ripristino nella sottoscrizione di Azure per orchestrare e gestire la replica tra i percorsi VMM.
**Server VMM** | È necessario avere a disposizione un percorso VMM primario e uno secondario. | È consigliabile distribuire un server VMM in un sito primario e uno nel sito secondario.
**Server Hyper-V** |  Uno o più server host Hyper-V nei cloud VMM primario e secondario. | I dati vengono replicati tra il server host Hyper-V primario e secondario su LAN o VPN usando l'autenticazione Kerberos o del certificato.  
**VM Hyper-V** | Nel server host Hyper-V. | Il server host di origine deve avere almeno una VM che si vuole replicare.

**Architettura della replica da sito locale a sito locale**

![Da sito locale a sito locale](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo di replica

1. Quando viene attivata la replica iniziale, viene acquisito uno [snapshot della macchina virtuale Hyper-V](https://technet.microsoft.com/library/dd560637.aspx).
2. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno per volta nel sito secondario.
3. Se mentre è in corso la replica iniziale vengono apportate modifiche ai dischi, 
4. Al termine della replica iniziale, viene avviata la replica differenziale. Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (HRL). Questi file di log si trovano nella stessa cartella dei dischi. A ogni disco è associato un file HRL, che viene inviato al sito secondario. Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
5. Nel log le modifiche differenziali al disco vengono sincronizzate e unite al disco padre.
6. 

## <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire il failover di un solo computer o creare piani di ripristino per orchestrare il failover di più computer.
2. È possibile eseguire un failover pianificato o non pianificato tra siti locali. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
    - Se è stato eseguito un failover non pianificato in un sito secondario, dopo il failover le macchine nella posizione secondaria non sono protette.
    - Se è stato eseguito un failover pianificato, dopo il failover i computer della posizione secondaria sono protetti.
3. Dopo aver attivato il failover iniziale, è necessario eseguirne il commit per iniziare ad accedere al carico di lavoro dalla macchina virtuale di replica.

Quando il sito primario è di nuovo disponibile, è possibile eseguire il failback.

1. Avviare la replica inversa, per avviare la replica dal sito secondario a quello primario. La replica inversa porta le macchine virtuali in uno stato protetto, ma per il datacenter secondario resta la posizione attiva.
2. Per rendere di nuovo il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.



## <a name="next-steps"></a>Passaggi successivi

Esaminare la matrice di supporto Seguire l'esercitazione per abilitare la replica Hyper-V tra cloud VMM.
Eseguire un failover e un failback.
