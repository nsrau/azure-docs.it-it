---
title: Architettura della replica di macchine virtuali VMware/server fisici in Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di server fisici e macchine virtuali VMware locali o di server Windows/Linux fisici in un sito secondario VMware con Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 97a990aa3ed9043280888900d8fc7b604b6c22b5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="vmware-vmphysical-server-to-vmware-replication-architecture"></a>Architettura della replica di macchine virtuali VMware/server fisici in VMware

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di macchine virtuali (VM) VMware locali o server Windows/Linux fisici in un sito VMware secondario usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azure** | Questo scenario viene distribuito con InMage Scout. | Per ottenere InMage Scout, è necessaria una sottoscrizione di Azure.<br/><br/> Dopo aver creato un insieme di credenziali di Servizi di ripristino, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Server di elaborazione** | Situato nel sito primario | Il server di elaborazione viene distribuito per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati.<br/><br/> Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.
**Server di configurazione** | Situato nel sito secondario | Il server di configurazione gestisce, configura e monitora la distribuzione usando il sito Web di gestione o la console vContinuum.
**Server vContinuum** | facoltativo. Installato nella stessa posizione del server di configurazione. | Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto.
**Server master di destinazione** | Situato nel sito secondario | Questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati.<br/><br/> Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere.<br/><br/> Se si vuole eseguire il failback al sito primario, deve essere disponibile anche un server di destinazione master. Unified Agent viene installato in questo server.
**Server VMware ESX/ESXi e vCenter** |  Le macchine virtuali sono ospitate in host ESX/ESXi. Gli host vengono gestiti con un server vCenter. | È necessaria un'infrastruttura VMware per replicare le VM VMware.
**VM/server fisici** |  Unified Agent installato nei server fisici e nelle macchine virtuali VMware da replicare. | L'agente funge da provider di comunicazioni tra tutti i componenti.

### <a name="replication-process"></a>Processo di replica

1. Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare.
2. Dopo la replica iniziale, l'agente installato in ogni computer invia le modifiche della replica differenziale al server di elaborazione.
3. Il server di elaborazione ottimizza i dati e li trasferisce nel server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.

**Figura 6: Replica da VMware a VMware**

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Passaggi successivi

[Configurare](vmware-physical-secondary-disaster-recovery.md) il ripristino di emergenza di macchine virtuali VMware e server fisici in un sito secondario.
