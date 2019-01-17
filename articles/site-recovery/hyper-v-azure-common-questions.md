---
title: Domande frequenti - Ripristino di emergenza da Hyper-V ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo presenta un riepilogo delle domande frequenti relative alla configurazione del ripristino di emergenza per le macchine virtuali Hyper-V locali in Azure tramite Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 12/27/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 11e29aa8d85ed7e3cf5ce7b4a8360e4b5eb628f9
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319218"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Domande frequenti - Ripristino di emergenza da Hyper-V ad Azure

Questo articolo fornisce le risposte alle domande frequenti relative alla replica di macchine virtuali Hyper-V locali in Azure. 


## <a name="general"></a>Generale

### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?
Vedere [Dettagli relativi ai prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Come vengono addebitati i costi per le macchine virtuali di Azure?
Durante la replica, i dati vengono replicati in Archiviazione di Azure e non viene addebitato alcun costo per le modifiche alle macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure. In seguito vengono fatturate le risorse di calcolo utilizzate in Azure.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-azure"></a>Cosa è necessario avere in Azure?
Sono necessari una sottoscrizione di Azure, un insieme di credenziali di Servizi di ripristino, un account di archiviazione e una rete virtuale. L'insieme di credenziali, l'account di archiviazione e la rete devono trovarsi nella stessa area.

### <a name="what-azure-storage-account-do-i-need"></a>Quale account di archiviazione di Azure serve?
È necessario un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account di archiviazione Premium è supportato.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?
L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non si ha questo ruolo, sono necessarie le autorizzazioni per creare una macchina virtuale di Azure nel gruppo di risorse e nella rete virtuale specificata durante la configurazione di Site Recovery e le autorizzazioni di scrittura per l'account di archiviazione selezionato. [Altre informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)

### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali. I dati di replica vengono scambiati tra gli host Hyper-V e Archiviazione di Azure. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>È possibile mantenere metadati locali in un'area geografica?
Sì. Quando si crea un insieme di credenziali in un'area, tutti i metadati usati da Site Recovery restano all'interno dei confini di tale area geografica.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Sì, sono supportate sia la crittografia in transito che la [crittografia in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption).


## <a name="deployment"></a>Distribuzione

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Cosa si può fare con la replica da Hyper-V ad Azure?

- **Ripristino di emergenza**: È possibile configurare il ripristino di emergenza completo. In questo scenario viene eseguita la replica di macchine virtuali Hyper-V locali in Archiviazione di Azure:
    - È possibile replicare macchine virtuali in Azure. Se l'infrastruttura locale non è disponibile, si effettua il failover in Azure.
    - Quando si esegue un failover, vengono create macchine virtuali di Azure con i dati replicati. È possibile accedere alle app e ai carichi di lavoro nelle macchine virtuali di Azure.
    - Quando il data center locale è di nuovo disponibile, è possibile eseguire il failback da Azure al sito locale.
- **Migrazione**: È possibile usare Site Recovery per eseguire la migrazione delle macchine virtuali Hyper-V locali in Archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure.


### <a name="what-do-i-need-on-premises"></a>Cosa è necessario avere in locale?

Sono necessarie una o più macchine virtuali in esecuzione in uno o più host Hyper-V autonomi o in cluster. È anche possibile replicare macchine virtuali in esecuzione su host gestiti da System Center Virtual Machine Manager (VMM).
    - Se non si esegue VMM, durante la distribuzione di Site Recovery, è necessario raccogliere gli host e i cluster Hyper-V in siti Hyper-V. È necessario installare gli agenti di Site Recovery (il provider di Azure Site Recovery e l'agente di Servizi di ripristino) in ogni host Hyper-V.
    - Se gli host Hyper-V si trovano in un cloud VMM, si orchestra la replica in VMM. Installare il provider di Site Recovery nel server VMM e l'agente di Servizi di ripristino in ogni host Hyper-V. Eseguire il mapping tra le reti logiche VMM/reti VM e le reti virtuali di Azure.
    - 
[Altre informazioni](hyper-v-azure-architecture.md) sull'architettura della replica da Hyper-V ad Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>È possibile replicare le macchine virtuali situate in un cluster Hyper-V?

Sì, Site Recovery supporta gli host Hyper-V in cluster. Si noti che:

- Tutti i nodi del cluster devono essere registrati nello stesso insieme di credenziali.
- Se non si usa VMM, tutti gli host Hyper-V nel cluster devono essere aggiunto allo stesso sito Hyper-V.
- Installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino in ogni host Hyper-V del cluster e aggiungere ogni host a un sito Hyper-V.
- Non è necessario eseguire procedure specifiche nel cluster.
- Se si esegue lo strumento Deployment Planner per Hyper-V, questo raccoglie i dati del profilo dal nodo in esecuzione e in cui è in esecuzione la macchina virtuale. Lo strumento non può raccogliere dati da un nodo disattivato, ma ne terrà traccia. Quando il nodo è in esecuzione, lo strumento avvia la raccolta di dati del profilo della macchina virtuale dal nodo (se la VM fa parte dell'elenco di profili di macchine virtuali ed è in esecuzione nel nodo).
- Se viene eseguita la migrazione di una macchina virtuale su un host Hyper-V in Site Recovery a un altro host Hyper-V nello stesso cluster o a un host autonomo, la replica per la macchina virtuale non sarà interessata. L'host Hyper-V deve soddisfare i [prerequisiti](hyper-v-azure-support-matrix.md#on-premises-servers) ed essere configurata in un insieme di credenziali di Site Recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>È possibile proteggere le macchine virtuali quando Hyper-V è in esecuzione in un sistema operativo client?
No, le VM devono trovarsi in un server host Hyper-V in esecuzione in un computer server Windows supportato. Se è necessario proteggere un computer client, è possibile [replicarlo come computer fisico](physical-azure-disaster-recovery.md) in Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>È possibile replicare le macchine virtuali Hyper-V di seconda generazione in Azure?
Sì. Site Recovery esegue la conversione dalla seconda generazione alla prima durante il failover. In caso di failback, la macchina virtuale viene riconvertita nella seconda generazione.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>È possibile automatizzare gli scenari di Site Recovery con un SDK?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la replica da Hyper-V ad Azure con PowerShell:

- [Replica di Hyper-V senza VMM tramite PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replica di Hyper-V con VMM tramite PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replica

### <a name="where-do-on-premises-vms-replicate-to"></a>Dove viene eseguita la replica delle macchine virtuali locali?
I dati vengono replicati in Archiviazione di Azure. Quando si esegue un failover, Site Recovery crea automaticamente le macchine virtuali di Azure dall'account di archiviazione.

### <a name="what-apps-can-i-replicate"></a>Quali app è possibile replicare?
È possibile replicare qualsiasi app o carico di lavoro in esecuzione su una macchina virtuale Hyper-V conforme ai [requisiti di replica](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il failover e il failback delle app a uno stato intelligente. Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="whats-the-replication-process"></a>Qual è il processo di replica?

1. Quando viene attivata la replica iniziale, viene acquisito uno snapshot della macchina virtuale Hyper-V.
2. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno per volta fino a quando non vengono copiati tutti in Azure. Questa operazione può richiedere tempo, a seconda delle dimensioni della macchina virtuale e della larghezza di banda di rete. Informazioni su come aumentare la larghezza di banda.
3. Se vengono apportate modifiche ai dischi mentre è in corso la replica iniziale, Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (HRL). Questi file di log si trovano nella stessa cartella dei dischi. A ogni disco è associato un file HRL, che viene inviato alla risorsa di archiviazione secondaria. Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
4. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato.
5. Nel log le modifiche al disco vengono sincronizzate e unite al disco padre.
6. Al termine della replica iniziale, viene eseguito il processo Finalizza la protezione nella macchina virtuale. Questo processo configura impostazioni di rete e altre impostazioni successive alla replica in modo da proteggere la macchina virtuale.
7. In questa fase è possibile controllare le impostazioni della macchina virtuale per assicurarsi che sia pronta per il failover. È possibile eseguire un'analisi di ripristino di emergenza (failover di test) per la macchina virtuale, per verificare che il failover avvenga nel modo previsto.
8. Dopo la replica iniziale, viene avviata la replica differenziale in base ai criteri di replica.
9. Le modifiche vengono registrate in file con estensione HRL. A ogni disco configurato per la replica è associato un file con estensione hrl.
10. Il log viene inviato all'account di archiviazione del cliente. Quando un log è in transito verso Azure, le modifiche del disco primario vengono registrate in un altro file di log nella stessa cartella.
11. Durante la replica iniziale e quella differenziale, è possibile monitorare la macchina virtuale nel portale di Azure.

[Altre informazioni](hyper-v-azure-architecture.md#replication-process) sul processo di replica.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>È possibile eseguire la replica in Azure tramite una VPN da sito a sito?

Site Recovery replica i dati dal sito locale ad Archiviazione di Azure su un endpoint pubblico o mediante peering pubblico ExpressRoute. La replica su una rete VPN da sito a sito non è supportata.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>È possibile eseguire la replica in Azure con ExpressRoute?

Sì, è possibile usare ExpressRoute per replicare macchine virtuali in Azure. Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico ed è necessario configurare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#publicpeering) per la replica di Site Recovery. Dopo il failover delle macchine virtuali in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Perché non è possibile eseguire la replica su VPN?

Quando si esegue la replica in Azure, il traffico di replica raggiunge gli endpoint pubblici di un account di archiviazione di Azure, quindi è possibile eseguire la replica solo su Internet pubblico con ExpressRoute (peering pubblico), mentre la VPN non è supportata. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

Per la replica è necessario che la macchina virtuale Hyper-V esegua un sistema operativo supportato. Inoltre, la macchina virtuale deve soddisfare i requisiti relativi alle macchine virtuali di Azure. Vedere [altre informazioni](hyper-v-azure-support-matrix.md#replicated-vms) nella matrice di supporto.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

Le macchine virtuali Hyper-V possono essere replicate ogni 30 secondi (eccetto per Archiviazione Premium), 5 minuti o 15 minuti.

###<a name="can-i-extend-replication"></a>È possibile estendere la replica?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?
Sì, è possibile escludere dischi dalla replica. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.



## <a name="security"></a>Sicurezza

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>A quale scopo Site Recovery deve accedere agli host Hyper-V?

Site Recovery deve accedere agli host Hyper-V per replicare le macchine virtuali selezionate. Site Recovery installa negli host Hyper-V quanto segue:

- Se non si esegue VMM, il provider di Site Recovery e l'agente di Servizi di ripristino vengono installati in ogni host.
- Se si esegue VMM, l'agente di Servizi di ripristino viene installato in ogni host. Il provider viene eseguito nel server VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Cosa installa Site Recovery nelle macchine virtuali Hyper-V?

Site Recovery non installa esplicitamente alcun componente nelle macchine virtuali Hyper-V abilitate per la replica.




## <a name="failover-and-failback"></a>Failover e failback


### <a name="how-do-i-fail-over-to-azure"></a>Come si effettua il failover in Azure?

È possibile eseguire un failover pianificato o non pianificato dalle macchine virtuali Hyper-V locali ad Azure.
    - Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
    - È possibile eseguire un failover non pianificato se il sito primario non è accessibile.
    - È possibile eseguire il failover di un solo computer o creare piani di ripristino per orchestrare il failover di più computer.
    - Eseguire un failover. Al termine della prima fase, le macchine virtuali di replica create dovrebbero essere visualizzate in Azure. È possibile assegnare un indirizzo IP pubblico alla VM, se necessario. È quindi necessario eseguire il commit del failover per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure di replica.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?
Dopo il failover, è possibile accedere alle macchine virtuali di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>I dati di cui è stato effettuato il failover sono resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è progettato per il failover a un data center secondario di Azure in conformità con il contratto di servizio di Azure. Al momento del failover, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il [failover](site-recovery-failover.md) non è automatico. Può essere avviato con un singolo clic nel portale oppure mediante [PowerShell](/powershell/module/azurerm.siterecovery).

### <a name="how-do-i-fail-back"></a>Come si esegue il failback?

Quando l'infrastruttura locale è di nuovo operativa, è possibile eseguire il failback. Il failback avviene in tre fasi:

1. Si può avviare un failover pianificato da Azure al sito primario scegliendo tra due diverse opzioni:

    - Ridurre al minimo il tempo di inattività: Se si usa questa opzione, Site Recovery sincronizza i dati prima del failover. Il servizio verifica la presenza di blocchi di dati modificati e li scarica nel sito locale, mentre la macchina virtuale di Azure resta in esecuzione, riducendo al minimo il tempo di inattività. Quando si specifica manualmente che è necessario completare il failover, la macchina virtuale di Azure viene arrestata, vengono copiate tutte le modifiche differenziali finali e viene avviato il failover.
    - Download completo: Con questa opzione i dati vengono sincronizzati durante il failover. Questa opzione scarica l'intero disco. Questa opzione è più veloce, perché non viene calcolato alcun checksum, ma il tempo di inattività è maggiore. Usare questa opzione se le macchine virtuali di Azure di replica sono state eseguite per un certo tempo o se la macchina virtuale locale è stata eliminata.

2. È possibile scegliere di eseguire il failback nella stessa macchina virtuale o in una alternativa. È possibile specificare che Site Recovery deve creare la macchina virtuale, se non esiste già.
3. Al termine della sincronizzazione iniziale, scegliere di completare il failover. Al termine, è possibile accedere alla macchina virtuale locale per verificare che funzioni tutto come previsto. Nel portale di Azure si noterà che le macchine virtuali di Azure sono state arrestate.
4. Eseguire il commit del failover per completare il processo e iniziare ad accedere di nuovo al carico di lavoro dalla macchina virtuale locale.
5. Al termine del failback dei carichi di lavoro, abilitare la replica inversa, in modo che le macchine virtuali locali vengano replicate di nuovo in Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?
Sì, se è stato effettuato il failover ad Azure, è possibile eseguire il failback in una posizione diversa se quella originale non è disponibile. [Altre informazioni](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment)
