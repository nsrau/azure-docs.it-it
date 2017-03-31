---
title: Funzionamento di Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell&quot;architettura di Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4674985363bc1267449e018ab15a53757a8fd32d
ms.lasthandoff: 03/15/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Funzionamento di Azure Site Recovery

Questo articolo illustra l'architettura sottostante del servizio [Azure Site Recovery](site-recovery-overview.md) e i componenti necessari per il suo funzionamento.

Per inviare commenti è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="replicate-to-azure"></a>Replicare in Azure

È possibile replicare gli elementi seguenti in Azure:

- **VMware**: macchine virtuali VMware locali in esecuzione in un [host supportato](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). È possibile replicare macchine virtuali VMware che eseguono i [sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: macchine virtuali Hyper-V locali in esecuzione negli [host supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- **Macchine fisiche**: server fisici locali che eseguono Windows o Linux nei [sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). È possibile replicare macchine virtuali Hyper-V che eseguono qualsiasi sistema operativo guest [supportato da Hyper-V e Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="vmware-to-azure"></a>Da VMware ad Azure

Di seguito sono indicati i componenti necessari per la replica di macchine virtuali VMware in Azure.

Area | Componente | Dettagli
--- | --- | ---
**Azzurro** | In Azure sono necessari un account Azure, un account di archiviazione di Azure e una rete di Azure. | La risorsa di archiviazione e la rete possono essere account Resource Manager o account classici.<br/><br/>  I dati replicati vengono salvati nell'account di archiviazione e,quando si verifica un failover dal sito locale, vengono create VM di Azure con i dati replicati. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server di configurazione** | Un singolo server di gestione (VM VMWare) esegue tutti i componenti locali: server di configurazione, server di elaborazione, server master di destinazione. | Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
 **Server di elaborazione**  | Installato per impostazione predefinita nel server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware.<br/><br/> Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.
 **Server master di destinazione** | Installato per impostazione predefinita nel server di configurazione locale. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Se i volumi del traffico di failback sono elevati, è possibile distribuire un server di destinazione master separato per il failback.
**Server VMware** | Le macchine virtuali VMware sono ospitate nei server vSphere ESXi e si consiglia un server vCenter per la gestione degli host. | È possibile aggiungere server VMware all'insieme di credenziali di Servizi di ripristino.<br/><br/> I
**Computer replicati** | Il servizio Mobility verrà installato in ogni macchina virtuale VMware da replicare. Può essere installato manualmente in ogni computer o con un'installazione push dal server di elaborazione.

**Figura 1: Componenti da VMware ad Azure**

![Componenti](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Processo di replica

1. Si configurano la distribuzione, inclusi i componenti di Azure, e un insieme di credenziali di Servizi di ripristino. Nell'insieme di credenziali si specificano l'origine e la destinazione della replica, si imposta il server di configurazione, si aggiungono i server VMware, si crea un criterio di replica, si distribuisce il servizio Mobility, si abilita la replica e si esegue un failover di test.
2.  I computer avviano la replica in base al criterio di replica e una copia iniziale dei dati viene replicata in Archiviazione di Azure.
4. La replica delle modifiche differenziali in Azure viene avviata al termine della replica iniziale. Le modifiche rilevate vengono salvate in un file HRL.
    - I computer di replica comunicano con il server di configurazione tramite la porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - I computer di replica inviano i dati delle repliche al server di elaborazione tramite la porta HTTPS 9443 in ingresso (configurabile).
    - Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.
    - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad Archiviazione di Azure tramite la porta 443 in uscita.
    - Se si abilita la coerenza tra più VM, i computer del gruppo di replica comunicano tra loro tramite la porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. È utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
5. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.

**Figura 2: Replica da VMware ad Azure**

![Avanzato](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>Failover e failback

1. Dopo aver verificato che il failover di test funzioni come previsto, è possibile eseguire failover non pianificati in Azure in base alle esigenze. Il failover pianificato non è supportato.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per il failover di più macchine virtuali.
3. Quando si esegue un failover, vengono create VM di replica in Azure. Si esegue il commit di un failover per avviare l'accesso al carico di lavoro dalla VM di Azure di replica.
4. Quando il sito locale primario è di nuovo disponibile, è possibile effettuare il failback. Si configura un'infrastruttura di failback, si avvia la replica del computer dal sito secondario a quello primario e si esegue un failover non pianificato dal sito secondario. Dopo il commit di questo failover, i dati saranno di nuovo locali e sarà necessario abilitare ancora la replica in Azure. [Altre informazioni](site-recovery-failback-azure-to-vmware.md)

Esistono alcuni requisiti per il failback:


- **Server di elaborazione temporaneo in Azure**: se si vuole eseguire il failback da Azure dopo il failover, è necessario impostare una VM di Azure configurata come server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.
- **Server di destinazione master locale distinto**: il server di destinazione master locale gestisce il failback. Il server di destinazione master viene installato per impostazione predefinita nel server di gestione, ma se si esegue il failback di volumi di traffico più grandi, è consigliabile configurare un server di destinazione master locale distinto a questo scopo.
- **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si crea il criterio di replica.

**Figura 3: Failback VMware/fisico**

![Failback](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-to-azure"></a>Da fisico ad Azure

Quando si esegue la replica di server fisici locali in Azure, vengono usati gli stessi componenti e processi della replica da [VMware ad Azure](#vmware-replication-to-azure), con le differenze seguenti:

- È possibile usare un server fisico per il server di configurazione, anziché una VM VMware.
- Sarà necessaria un'infrastruttura VMware locale per il failback. Non è possibile eseguire il failback a un computer fisico.

## <a name="hyper-v-to-azure"></a>Da Hyper-V ad Azure

Di seguito sono indicati i componenti necessari per la replica di macchine virtuali Hyper-V in Azure.

**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azzurro** | In Azure sono necessari un account Microsoft Azure, un account di archiviazione di Azure e una rete di Azure. | La risorsa di archiviazione e la rete possono essere account basati su Resource Manager o classici.<br/><br/> I dati replicati vengono salvati nell'account di archiviazione e,quando si verifica un failover dal sito locale, vengono create VM di Azure con i dati replicati.<br/><br/> Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server VMM** | Host Hyper-V situati in cloud VMM | Se gli host Hyper-V sono gestiti in cloud VMM, il server VMM viene registrato nell'insieme di credenziali di Servizi di ripristino.<br/><br/> Nel server VMM è necessario installare il provider di Site Recovery per orchestrare la replica con Azure.<br/><br/> È necessaria la configurazione di reti logiche e VM per configurare il mapping di rete. È necessario che una rete VM sia collegata a una rete logica associata al cloud.
**Host Hyper-V** | I server Hyper-V possono essere distribuiti con o senza server VMM. | In assenza di un server VMM, il provider di Site Recovery è installato nell'host per orchestrare la replica con Site Recovery tramite Internet. Se è presente un server VMM, il provider è installato in esso e non nell'host.<br/><br/> L'agente di Servizi di ripristino viene installato nell'host per gestire la replica dei dati.<br/><br/> Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.
**VM Hyper-V** | È necessaria una o più macchine virtuali nel server host Hyper-V. | Non è necessario installare esplicitamente alcun componente nelle macchine virtuali.


### <a name="replication-process"></a>Processo di replica

1. Si configurano i componenti di Azure. È consigliabile configurare gli account di archiviazione e di rete prima di iniziare la distribuzione di Site Recovery.
2. Si crea un insieme di credenziali dei servizi di replica per Site Recovery e si configurano le impostazioni dell'insieme di credenziali, tra cui:
    - Impostazioni di origine e di destinazione. Se non si gestiscono gli host Hyper-V in un cloud VMM, per la destinazione si crea un contenitore del sito Hyper-V a cui si aggiungono gli host Hyper-V. Se gli host Hyper-V vengono gestiti in VMM, l'origine è il cloud VMM. La destinazione è Azure.
    - Installazione del provider di Azure Site Recovery e dell'agente Servizi di ripristino di Microsoft Azure. Il provider verrà installato in VMM, se disponibile, e l'agente in ogni host Hyper-V. Se non si ha VMM, sia il provider che l'agente vengono installati in ogni host.
    - Si crea un criterio di replica per il sito Hyper-V o per il cloud VMM. Il criterio viene applicato a tutte le VM negli host del sito o del cloud.
    - Si abilita la replica per le VM Hyper-V. La replica iniziale viene eseguita in base alle impostazioni del criterio di replica.
4. Le modifiche ai dati vengono rilevate e la replica delle modifiche differenziali in Azure viene avviata al termine della replica iniziale. Le modifiche rilevate per un elemento vengono salvate in un file HRL.
5. Si esegue un failover di test per verificare che tutto funzioni correttamente.

### <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire un [failover](site-recovery-failover.md) pianificato o non pianificato dalle VM Hyper-V locali ad Azure. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per orchestrare il failover di più computer.
4. Dopo avere eseguito il failover, le VM di replica create verranno visualizzate in Azure. È possibile assegnare un indirizzo IP pubblico alla VM, se necessario.
5. Si esegue quindi il commit del failover per avviare l'accesso al carico di lavoro dalla VM di Azure di replica.
6. Quando il sito locale primario è di nuovo disponibile, è possibile [effettuare il failback](site-recovery-failback-from-azure-to-hyper-v.md). Si avvia un failover pianificato da Azure al sito primario. Per un failover pianificato è possibile scegliere di effettuare il failback nella stessa VM o in una posizione alternativa e di sincronizzare le modifiche tra Azure e l'ambiente locale, per evitare perdite di dati. Quando le VMs vengono create in locale, si esegue il commit del failover.

**Figura 4: Replica dal sito Hyper-V ad Azure**

![Componenti](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 5: Replica da Hyper-V nei cloud VMM ad Azure**

![Componenti](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replicate-to-a-secondary-site"></a>Replica a un sito secondario

È possibile replicare gli elementi seguenti in un sito secondario:

- **VMware**: macchine virtuali VMware locali in esecuzione in un [host supportato](site-recovery-support-matrix-to-sec-site.md#on-premises-servers). È possibile replicare macchine virtuali VMware che eseguono i [sistemi operativi supportati](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Macchine fisiche**: server fisici locali che eseguono Windows o Linux nei [sistemi operativi supportati](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: macchine virtuali Hyper-V locali in esecuzione negli [host Hyper-V supportati](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) gestiti in cloud VMM. [host supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). È possibile replicare macchine virtuali Hyper-V che eseguono qualsiasi sistema operativo guest [supportato da Hyper-V e Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="vmwarephysical-to-a-secondary-site"></a>Da server fisici/VMware a un sito secondario

Per la replica di server fisici o macchine virtuali VMware in un sito secondario si usa InMage Scout.

### <a name="components"></a>Componenti

**Area** | **Componente** | **Dettagli**
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

**Figura 6: Replica da VMware a VMware**

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-to-a-secondary-site"></a>Da Hyper-V a un sito secondario

Di seguito sono indicati i componenti necessari per la replica di macchine virtuali Hyper-V in un sito secondario.


**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azzurro** | È necessario un account Microsoft Azure. |
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

**Figura 7: Replica da VMM a VMM**

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>Failover e failback

1. È possibile eseguire un [failover](site-recovery-failover.md) pianificato o non pianificato tra siti locali. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per orchestrare il failover di più computer.
4. Se si esegue un failover non pianificato in un sito secondario, dopo il failover i computer della posizione secondaria non sono abilitati per la protezione o la replica. Se è stato eseguito un failover pianificato, dopo il failover i computer della posizione secondaria sono protetti.
5. Si esegue quindi il commit del failover per avviare l'accesso al carico di lavoro dalla VM di replica.
6. Quando il sito primario è nuovamente disponibile, si avvia la replica inversa dal sito secondario a quello primario. La replica inversa porta le macchine virtuali in uno stato protetto, ma per il datacenter secondario resta la posizione attiva.
7. Per rendere di nuovo il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](site-recovery-hyper-v-azure-architecture.md) sul flusso di lavoro di replica Hyper-V.
- [Controllare i prerequisiti](site-recovery-prereq.md)

