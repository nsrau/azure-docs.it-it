---
title: Domande comuni sul ripristino di emergenza da Azure ad Azure con Azure Site Recovery
description: Questo articolo risponde a domande comuni sul ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure usando Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: cd1c6cf0ff5a963720df7420a5d983d24e7b4d3e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861383"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Domande frequenti: Ripristino di emergenza da Azure ad Azure

Questo articolo fornisce le risposte alle domande comuni sul ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure usando [Site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Generale

### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?
Vedere [Dettagli relativi ai prezzi di Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Come funziona il livello gratuito di Azure Site Recovery?
Ogni istanza protetta con Azure Site Recovery è gratuita per i primi 31 giorni di protezione. Dal trentaduesimo giorno in avanti, per la protezione dell'istanza vengono addebitati i costi sopra indicati.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, sono previsti altri addebiti per Azure?
Sì, anche se Azure Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, è possibile che vengano addebitati i costi per Archiviazione di Azure, transazioni di archiviazione e trasferimento dati. Per una macchina virtuale ripristinata possono venire addebitati anche i costi di calcolo di Azure. Informazioni dettagliate sui prezzi sono disponibili [qui](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Dove è possibile trovare le procedure consigliate per il ripristino di emergenza delle macchine virtuali di Azure? 
1. [Informazioni sull'architettura da Azure ad Azure](azure-to-azure-architecture.md)
2. [Esaminare le configurazioni supportate e non supportate](azure-to-azure-support-matrix.md)
3. [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-how-to-enable-replication.md)
4. [Eseguire un failover di test](azure-to-azure-tutorial-dr-drill.md)
5. [Eseguire il failover e il failback nell'area primaria](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Come viene garantita la capacità nell'area di destinazione?
Il team di Site Recovery collabora con il team di gestione della capacità di Azure per pianificare una capacità di infrastruttura sufficiente e garantire che le macchine virtuali protette da Site Recovery per vengano distribuite correttamente nell'area di destinazione quando viene avviato il failover.

## <a name="replication"></a>Replica

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>È possibile replicare le macchine virtuali abilitate con Crittografia dischi di Azure?

Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali con crittografia dischi di Azure (ADE) abilitata. Quando si Abilita la replica, tutte le chiavi e i segreti di crittografia del disco necessari vengono copiati dall'area di origine all'area di destinazione nel contesto utente. Se non si dispone delle autorizzazioni appropriate, è possibile passare uno script pronto all'uso all'amministratore della sicurezza per copiare le chiavi e i segreti.

- Site Recovery supporta ADE per le macchine virtuali di Azure che eseguono Windows.
- Site Recovery supporta ADE versione 0,1, con uno schema che usa Azure Active Directory (AAD) e la versione 1,1, senza AAD. [Altre informazioni](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata)
- ADE versione 1,1, le macchine virtuali Windows devono essere usate dischi gestiti.
- [Altre](azure-to-azure-how-to-enable-replication-ade-vms.md) informazioni sull'abilitazione della replica per le macchine virtuali crittografate.



### <a name="can-i-replicate-vms-to-another-subscription"></a>È possibile replicare le macchine virtuali in un'altra sottoscrizione?
Sì, è possibile replicare macchine virtuali di Azure in una sottoscrizione diversa nello stesso tenant di Azure AD.
La configurazione per il ripristino di emergenza [tra sottoscrizioni](https://azure.microsoft.com/blog/cross-subscription-dr) è semplice. È possibile selezionare un'altra sottoscrizione al momento della replica.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>È possibile replicare in un'altra area le macchine virtuali di Azure aggiunte a una certa zona?
Sì, è possibile [replicare le macchine virtuali aggiunte a una certa zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) in un'altra area.

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?

Sì, è possibile escludere dischi al momento della protezione tramite PowerShell. Per ulteriori informazioni, vedere l' [articolo](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>È possibile aggiungere nuovi dischi alle macchine virtuali replicate e abilitare la replica?

Sì, è supportata per le macchine virtuali di Azure con Managed Disks. Quando si aggiunge un nuovo disco a una macchina virtuale di Azure abilitata per la replica, l'integrità della replica per la VM Visualizza un avviso, con una nota che specifica che uno o più dischi nella macchina virtuale sono disponibili per la protezione. È possibile abilitare la replica per i dischi aggiunti.
- Se si Abilita la protezione per i dischi aggiunti, l'avviso scomparirà dopo la replica iniziale.
- Se si sceglie di non abilitare la replica per il disco, è possibile scegliere di ignorare l'avviso.
- Quando si esegue il failover di una macchina virtuale a cui si aggiunge un disco e si Abilita la replica, i punti di replica visualizzeranno i dischi disponibili per il ripristino. Ad esempio, se una macchina virtuale ha un singolo disco e ne viene aggiunto uno nuovo, i punti di replica creati prima di aggiungerlo mostreranno che il punto di replica è costituito da "1 di 2 dischi".

Site Recovery non supporta la "rimozione a caldo" di un disco da una macchina virtuale replicata. Se si rimuove un disco della macchina virtuale, è necessario disabilitare e quindi riabilitare la replica per la macchina virtuale.


### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?
La replica di macchine virtuali di Azure in un'altra area di Azure è continua. Per altre informazioni, vedere [Architettura di replica da Azure ad Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>È possibile eseguire la replica delle macchine virtuali all'interno di un'area? Questa opzione è necessaria per poter eseguire la migrazione di macchine virtuali.
Non è possibile usare una soluzione di ripristino di emergenza da Azure ad Azure per replicare le macchine virtuali all'interno di un'area.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>È possibile replicare le macchine virtuali in qualsiasi area di Azure?
Con Site Recovery è possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti con la latenza e la sovranità dei dati. Per altre informazioni, vedere la [matrice di supporto dell'area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) di Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery richiede la connettività Internet?

No, Site Recovery non richiede la connettività Internet. È tuttavia necessario l'accesso agli intervalli IP e agli URL di Site Recovery, come indicato in [questo articolo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>È possibile eseguire la replica dell'applicazione con un gruppo di risorse separato per livelli diversi?
Sì, è possibile eseguire la replica dell'applicazione e mantenere la configurazione di ripristino di emergenza in un gruppo di risorse separato.
Se ad esempio si ha un'applicazione con ogni livello (app, database e Web) in un gruppo di risorse separato, è necessario fare clic tre volte sulla [procedura guidata di replica](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) per proteggere tutti i livelli. Site Recovery eseguirà la replica di questi tre livelli in tre gruppi di risorse diversi.

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?
Site Recovery definisce le impostazioni per la cronologia della conservazione dei punti di recupero e per la frequenza degli snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con le impostazioni predefinite seguenti:

* 24 ore per la cronologia della conservazione dei punti di recupero.
* 60 minuti per la frequenza degli snapshot coerenti con l'app.

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)

### <a name="what-is-a-crash-consistent-recovery-point"></a>Che cos'è il punto di recupero coerente con l'arresto anomalo del sistema?
Il punto di recupero coerente con l'arresto anomalo del sistema rappresenta i dati su disco come quando la macchina virtuale si arresta in modo anomalo o il cavo di alimentazione viene estratto dal server al momento della creazione dello snapshot temporizzato. Non include tutto ciò che era contenuto in memoria quando è stato creato lo snapshot.

La maggior parte delle applicazioni esegue ora correttamente il ripristino da snapshot coerenti con l'arresto anomalo del sistema. Un punto di recupero coerente con l'arresto anomalo del sistema è in genere sufficiente per sistemi operativi senza database e per applicazioni quali file server, server DHCP e server di stampa.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual è la frequenza di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?
Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti.

### <a name="what-is-an-application-consistent-recovery-point"></a>Che cos'è un punto di recupero coerente con l'applicazione?
I punti di recupero coerenti con l'applicazione vengono creati dagli snapshot coerenti con l'applicazione. I punti di recupero coerenti con l'applicazione acquisiscono gli stessi dati di snapshot coerenti con l'arresto anomalo del sistema, oltre a tutti i dati in memoria e a tutte le transazioni in corso.
Per via del loro contenuto aggiuntivo, gli snapshot coerenti con l'applicazione impiegano più tempo di esecuzione e sono i più coinvolti. È consigliabile usare i punti di recupero coerenti con l'applicazione per sistemi operativi e applicazioni di database come SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual è l'impatto dei punti di recupero coerenti con l'applicazione sulle prestazioni dell'applicazione?
Considerando che i punti di recupero coerenti con l'applicazione acquisiscono tutti i dati in memoria e tutte le transazioni in corso, per disattivare l'applicazione è necessario un framework come VSS in Windows. Questa operazione, se eseguita con frequenza elevata, può incidere in modo significativo sulle prestazioni, soprattutto se il carico di lavoro è già molto pesante. In genere è quindi consigliabile non usare una frequenza ridotta per i punti di recupero coerenti con l'app, sia per carichi di lavoro non di database sia per quelli di database: un'ora è sufficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual è la frequenza minima di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?
Site Recovery possibile creare un punto di ripristino coerente con l'applicazione con una frequenza minima di in 1 ora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?
Per comprendere il modo in cui Site Recovery genera i punti di recupero, si consideri l'esempio dei criteri di replica con una finestra di conservazione del punto di recupero di 24 ore e uno snapshot di frequenza coerente con l'app di 1 ora.

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti. L'utente non può modificare questa frequenza. Pertanto, per l'ultima ora l'utente potrà scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e 1 punto coerente con l'app. Col passare del tempo, Site Recovery elimina tutti i punti di recupero oltre l'ultima ora e salva solo 1 punto di recupero per ogni ora.

Lo screenshot seguente illustra l'esempio. Nello screenshot:

1. Per tempistiche inferiori all'ultima ora, sono disponibili punti di recupero con frequenza di 5 minuti.
2. Per tempistiche che superano l'ultima ora, Site Recovery conserva un solo punto di recupero.

   ![Elenco dei punti di recupero generati](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?
Il punto di recupero meno recente che è possibile usare è di 72 ore.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Cosa succede se sono configurati criteri di replica di 24 ore e un problema impedisce a Site Recovery di generare punti di recupero per più di 24 ore? I precedenti punti di recupero verranno persi?
No, Site Recovery manterrà tutti i punti di recupero precedenti. A seconda dell'intervallo di conservazione dei punti di recupero, 24 ore in questo caso, Site Recovery sostituisce il punto meno recente solo se vengono generati nuovi punti. In questo caso, poiché non verranno generati nuovi punti di recupero a causa di un problema, dopo la fine l'intervallo di conservazione tutti i punti precedenti rimarranno invariati.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Dopo aver abilitato la replica in una macchina virtuale, come si modifica il criterio di replica?
Passare a **Insieme di credenziali di Site Recovery** > **Infrastruttura di Site Recovery** > **Criteri di replica**. Selezionare i criteri da modificare e salvare le modifiche. Qualsiasi modifica verrà applicata anche a tutte le repliche esistenti.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tutti i punti di recupero includono una copia completa della macchina virtuale o solo una copia differenziale?
Il primo punto di recupero che viene generato include la copia completa. I punti di recupero successivi includono le modifiche delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>L'aumento del periodo di conservazione dei punti di recupero comporta l'aumento dei costi di archiviazione?
Sì. Se si aumenta il periodo di conservazione da 24 a 72 ore, Site Recovery salverà i punti di recupero per altre 48 ore. Il tempo aggiuntivo comporterà dei costi di archiviazione. Se ad esempio un singolo punto di recupero presenta modifiche delta di 10 GB e il costo per GB è pari a 0,16 USD al mese, l'addebito aggiuntivo sarà pari a 1,6 USD x 48 al mese.

## <a name="multi-vm-consistency"></a>Coerenza tra più macchine virtuali

### <a name="what-is-multi-vm-consistency"></a>Che cos'è la coerenza tra più macchine virtuali?
Questo tipo di coerenza implica che il punto di recupero sia coerente in tutte le macchine virtuali replicate.
Site Recovery offre l'opzione Coerenza tra più macchine virtuali che consente di eseguire la replica di tutte le macchine virtuali presenti in un gruppo di replica appositamente creato.
Tutte le macchine virtuali condivideranno punti di recupero coerenti con l'arresto anomalo del sistema e con l'app quando saranno sottoposte a failover.
Per [abilitare la coerenza tra più macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm), seguire i passaggi nell'esercitazione.

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>È possibile eseguire il failover di una macchina virtuale all'interno di un gruppo di replica per la coerenza tra più macchine virtuali?
Quando si seleziona l'opzione Coerenza tra più macchine virtuali, si dichiara che l'applicazione ha una dipendenza su tutte le macchine virtuali all'interno di un gruppo. Di conseguenza, il failover di una singola macchina virtuale non è consentito.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quante macchine virtuali è possibile replicare come parte di un gruppo di replica per la coerenza tra più macchine virtuali?
È possibile replicare 16 macchine virtuali all'interno di uno stesso gruppo di replica.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Quando è necessario abilitare la coerenza tra più macchine virtuali?
Poiché comporta un uso intensivo della CPU, l'abilitazione della coerenza tra più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più macchine virtuali. Se ad esempio sono presenti due istanze di SQL Server e due server Web in un'applicazione, è necessaria la coerenza tra più macchine virtuali solo per le istanze di SQL Server.


## <a name="failover"></a>Failover

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le macchine virtuali di Azure?
Il team di Site Recovery collabora con il team di gestione della capacità di Azure per pianificare una capacità di infrastruttura sufficiente per garantire che le macchine virtuali abilitate per il ripristino di emergenza vengano distribuite correttamente nell'area di destinazione quando viene avviato il failover.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il failover non è automatico. Può essere avviato con un singolo clic nel portale oppure tramite [PowerShell](azure-to-azure-powershell.md).

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>È possibile mantenere l'indirizzo IP pubblico in seguito al failover?

Non è possibile mantenere l'indirizzo IP pubblico dell'applicazione di produzione dopo il failover.
- I carichi di lavoro avviati come parte del processo di failover devono essere assegnati a una risorsa IP pubblica di Azure disponibile nell'area di destinazione.
- Questa operazione può essere eseguita manualmente o automatizzata con un piano di ripristino.
- Informazioni su come [configurare gli indirizzi IP pubblici dopo il failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>È possibile mantenere un indirizzo IP privato durante il failover?
Sì, è possibile usare un indirizzo IP privato. Per impostazione predefinita, quando si abilita il ripristino di emergenza per macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni di quelle di origine. -Per le macchine virtuali di Azure configurate con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per la macchina virtuale di destinazione, se non è in uso.
Informazioni su come [mantenere gli indirizzi IP durante il failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dopo il failover, perché il server ha assegnato un nuovo indirizzo IP?

Site Recovery tenta di fornire l'indirizzo IP al momento del failover. Se un'altra macchina virtuale richiede tale indirizzo, Site Recovery imposta il successivo indirizzo IP disponibile come destinazione.
Altre informazioni sulla [configurazione del mapping di rete e degli indirizzi IP per reti virtuali](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Che cosa sono i punti di recupero **più recenti (valore RPO più basso)** ?
L'opzione **Più recente (RPO più basso)** elabora tutti i dati inviati al servizio Site Recovery per creare un punto di recupero per ogni macchina virtuale e quindi esegue il failover in tale punto. Questa opzione offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>I punti di recupero **più recenti (RPO più basso)** hanno un impatto sul failover RTO (Recovery Time Objective)?
Sì. Site Recovery elabora tutti i dati in sospeso prima dell'esecuzione del failover, pertanto questa opzione ha un valore di RTO maggiore rispetto ad altre opzioni.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Che cosa significa l'opzione **Elaborato più recente** nei punti di recupero?
L'opzione **Elaborato più recente** consente di eseguire il failover di tutte le macchine virtuali del piano nel punto di recupero più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare **Punti di ripristino più recenti** nelle impostazioni della macchina virtuale. Questa opzione offre un RTO basso poiché non viene impiegato tempo per la gestione di dati non elaborati.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Cosa accade se si verifica un'interruzione imprevista dell'area primaria?
Dopo l'interruzione è possibile attivare un failover. Per eseguire il failover, in Site Recovery non è necessaria la connettività dall'area primaria.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Che cos'è un RTO di failover di una macchina virtuale?
Site Recovery ha un [RTO SLA di 2 ore](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Tuttavia, nella maggior parte dei casi, Site Recovery esegue il failover delle macchine virtuali in pochi minuti. È possibile calcolare l'RTO visitando il failover dei processi che mostra il tempo impiegato per far apparire la macchina virtuale. Per pianificare l'RTO del piano di ripristino, fare riferimento alla sezione seguente.

## <a name="recovery-plans"></a>Piani di ripristino

### <a name="what-is-a-recovery-plan"></a>Che cos'è un piano di ripristino?
I piani di ripristino in Site Recovery gestiscono il ripristino del failover delle macchine virtuali. Consentono anche di ottenere un ripristino costantemente accurato, ripetibile e automatizzato. Un piano di ripristino soddisfa le esigenze seguenti per l'utente:

- Definizione di un gruppo di macchine virtuali che eseguono il failover contemporaneamente.
- Definizione di dipendenze tra macchine virtuali, affinché l'applicazione venga visualizzata in modo accurato.
- Automatizzazione del ripristino con azioni manuali personalizzate, in modo che si possano effettuare attività diverse dal failover delle macchine virtuali.

[Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Come si esegue la sequenziazione in un piano di ripristino?

In un piano di ripristino è possibile creare più gruppi per ottenere la sequenziazione. Ogni gruppo esegue il failover contemporaneamente. Le macchine virtuali che fanno parte dello stesso gruppo eseguono il failover nello stesso momento, seguite da un altro gruppo. Per informazioni su come modellare un'applicazione usando un piano di ripristino, vedere [Informazioni sui piani di ripristino](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Come trovo l'RTO di un piano di ripristino?
Per controllare l'RTO di un piano di ripristino, eseguire un failover di test del piano di ripristino e passare a **Processi di Site Recovery**.
Nell'esempio seguente, il processo denominato SAPTestRecoveryPlan ha richiesto 8 minuti e 59 secondi per eseguire il failover di tutte le macchine virtuali e le azioni specificate.

![Elenco dei processi di Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>È possibile aggiungere runbook di Automazione al piano di ripristino?
Sì, è possibile integrare i runbook di Automazione di Azure nel piano di ripristino. [Altre informazioni](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Riprotezione e failback

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Dopo aver eseguito un failover dall'area primaria a un'area di ripristino di emergenza, le macchine virtuali in un'area di ripristino di emergenza vengono protette automaticamente?
No. Quando si [esegue il failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) di macchine virtuali di Azure da un'area a un'altra, le macchine virtuali si avviano nell'area di ripristino di emergenza in uno stato non protetto. Per eseguire il failback delle macchine virtuali nell'area primaria, è necessario [riproteggere](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) le macchine virtuali nell'area secondaria.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Al momento della riprotezione, Site Recovery esegue una replica completa dei dati dall'area secondaria all'area primaria?
Dipende dalla situazione. Se ad esempio la macchina virtuale dell'area di origine esiste, verranno sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. Site Recovery calcola i backup differenziali, confrontando i dischi e quindi trasferisce i dati. Questo processo richiede in genere alcune ore. Per altre informazioni su cosa accade durante la riprotezione, vedere [Riproteggere macchine virtuali di Azure sottoposte a failover nell'area primaria]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo richiede l'esecuzione di un failback?
Dopo la riprotezione, la quantità di tempo per il failback è in genere simile al tempo necessario per il failover dall'area primaria a un'area secondaria.

## <a name="capacity"></a>Capacità

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le macchine virtuali di Azure?
Il team di Site Recovery collabora con il team di gestione della capacità di Azure per pianificare una capacità di infrastruttura sufficiente per garantire che le macchine virtuali abilitate per il ripristino di emergenza vengano distribuite correttamente nell'area di destinazione quando viene avviato il failover.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funzionano con le istanze riservate?
Sì, è possibile acquistare [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) nell'area di ripristino di emergenza e Site Recovery le operazioni di failover le useranno. </br> Non è necessaria alcuna configurazione aggiuntiva.


## <a name="security"></a>Security

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?
No, Site Recovery non intercetta i dati replicati e non contiene informazioni su ciò che è in esecuzione nelle macchine virtuali. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  
Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Sì, sono supportate sia la crittografia in transito che [la crittografia inattiva in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

## <a name="next-steps"></a>Passaggi successivi
* [Esaminare](azure-to-azure-support-matrix.md) i requisiti di supporto.
* [Configurare](azure-to-azure-tutorial-enable-replication.md) la replica da Azure ad Azure.
- Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
