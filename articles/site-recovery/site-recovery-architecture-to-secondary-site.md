---
title: Funzionamento della replica di computer locali in un sito locale secondario in Azure Site Recovery | Documentazione Microsoft
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di server fisici e macchine virtuali locali in un sito secondario con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Funzionamento della replica di computer locali in un sito secondario in Site Recovery

Questo articolo illustra i componenti e i processi interessati durante la replica di server fisici e macchine virtuali locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

È possibile replicare gli elementi seguenti in un sito locale secondario:
- Macchine virtuali Hyper-V locali, macchine virtuali Hyper-V in cluster Hyper-V e host autonomi gestiti nei cloud System Center Virtual Machine Manager (VMM).
- Macchine virtuali VMware locali e server fisici Windows/Linux. In questo scenario la replica è gestita da Scout.

Per inviare commenti è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Replicare macchine virtuali Hyper-V in un sito locale secondario


### <a name="architectural-components"></a>Componenti dell'architettura

Di seguito sono indicati i componenti necessari per la replica di macchine virtuali Hyper-V in un sito secondario.

**Componente** | **Posizione** | **Dettagli**
--- | --- | ---
**Azzurro** | È necessario un account Microsoft. |
**Server VMM** | È consigliabile distribuire un server VMM in un sito primario e uno nel sito secondario. | Ogni server VMM deve essere connesso a Internet.<br/><br/> Ogni server deve avere almeno un cloud privato VMM, con il profilo funzionalità di Hyper-V impostato.<br/><br/> Il provider di Azure Site Recovery viene installato nel server VMM. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. Le comunicazioni tra il provider e Azure sono protette e crittografate.
**Server Hyper-V** |  Uno o più server host Hyper-V nei cloud VMM primario e secondario.<br/><br/> I server devono essere connessi a Internet.<br/><br/> I dati vengono replicati tra il server host Hyper-V primario e secondario su LAN o VPN usando l'autenticazione Kerberos o del certificato.  
**VM Hyper-V** | Situata nel server host Hyper-V di origine. | Il server host di origine deve avere almeno una VM che si vuole replicare.

### <a name="replication-process"></a>Processo di replica

1. Si configura l'account Azure.
2. Si crea un insieme di credenziali dei servizi di replica per Site Recovery e si configurano le impostazioni dell'insieme di credenziali, tra cui:

    - L'origine e la destinazione della replica (siti primario e secondario).
    - Installazione del provider di Azure Site Recovery e dell'agente Servizi di ripristino di Microsoft Azure. Il provider viene installato nei server VMM e l'agente in ogni host Hyper-V.
    - Si crea un criterio di replica per il cloud VMM di origine. Il criterio viene applicato a tutte le VM negli host del cloud.
    - Si abilita la replica per le VM Hyper-V. La replica iniziale viene eseguita in base alle impostazioni del criterio di replica.
4. Le modifiche ai dati vengono rilevate e la replica delle modifiche differenziali viene avviata al termine della replica iniziale. Le modifiche rilevate per un elemento vengono salvate in un file HRL.
5. Si esegue un failover di test per verificare che tutto funzioni correttamente.

**Figura 1: Replica da VMM a VMM**

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire un [failover](site-recovery-failover.md) pianificato o non pianificato tra siti locali. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per orchestrare il failover di più computer.
4. Se si esegue un failover non pianificato in un sito secondario, dopo il failover i computer della posizione secondaria non sono abilitati per la protezione o la replica. Se è stato eseguito un failover pianificato, dopo il failover i computer della posizione secondaria sono protetti.
5. Si esegue quindi il commit del failover per avviare l'accesso al carico di lavoro dalla VM di replica.
6. Quando il sito primario è nuovamente disponibile, si avvia la replica inversa dal sito secondario a quello primario. La replica inversa porta le macchine virtuali in uno stato protetto, ma per il datacenter secondario resta la posizione attiva.
7. Per rendere di nuovo il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicare VM VMware/server fisici in un sito secondario

Per la replica di server fisici o macchine virtuali VMware in un sito secondario tramite InMage Scout, si usano i componenti dell'architettura seguenti:


### <a name="architectural-components"></a>Componenti dell'architettura

**Componente** | **Posizione** | **Dettagli**
--- | --- | ---
**Azzurro** | InMage Scout | Per ottenere InMage Scout, è necessaria una sottoscrizione di Azure.<br/><br/> Dopo aver creato un insieme di credenziali di Servizi di ripristino, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Server di elaborazione** | Situato nel sito primario | Il server di elaborazione viene distribuito per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati.<br/><br/> Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.
**Server di configurazione** | Situato nel sito secondario | Il server di configurazione gestisce, configura e monitora la distribuzione usando il sito Web di gestione o la console vContinuum.
**Server vContinuum** | Facoltativo. Installato nella stessa posizione del server di configurazione. | Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto.
**Server master di destinazione** | Situato nel sito secondario | Questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati.<br/><br/> Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere.<br/><br/> Se si vuole eseguire il failback al sito primario, deve essere disponibile anche un server di destinazione master. Unified Agent viene installato in questo server.
**Server VMware ESX/ESXi e vCenter** |  Le macchine virtuali sono ospitate in host ESX/ESXi. Gli host vengono gestiti con un server vCenter. | È necessaria un'infrastruttura VMware per replicare le VM VMware.
**VM/server fisici** |  Unified Agent installato nei server fisici e nelle macchine virtuali VMware da replicare. | L'agente funge da provider di comunicazioni tra tutti i componenti.


### <a name="replication-process"></a>Processo di replica

1. Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare.
2. Dopo la replica iniziale, l'agente installato in ogni computer invia le modifiche della replica differenziale al server di elaborazione.
3. Il server di elaborazione ottimizza i dati e li trasferisce nel server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.

**Figura 2: Replica da VMware a VMware**

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Passaggi successivi

Rivedere la [matrice di supporto](site-recovery-support-matrix-to-sec-site.md)

