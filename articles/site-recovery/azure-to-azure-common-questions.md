---
title: Domande frequenti - Ripristino di emergenza da Azure ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo presenta un riepilogo delle domande frequenti relative alla configurazione del ripristino di emergenza per le macchine virtuali di Azure in un'altra area di Azure tramite Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969948"
---
# <a name="common-questions---azure-to-azure-replication"></a>Domande frequenti - Replica da Azure ad Azure

Questo articolo fornisce le risposte alle domande frequenti relative all'implementazione del ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure. Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Generale
### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?
Vedere [Dettagli relativi ai prezzi di Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Come configurare Site Recovery nelle macchine virtuali di Azure. Quali sono le procedure consigliate?
1. [Informazioni sull'architettura da Azure ad Azure](azure-to-azure-architecture.md)
2. [Esaminare le configurazioni supportate e non supportate](azure-to-azure-support-matrix.md)
3. [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-how-to-enable-replication.md)
4. [Eseguire un failover di test](azure-to-azure-tutorial-dr-drill.md)
5. [Failover e failback all'area primaria](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replica

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>È possibile replicare le macchine virtuali con crittografia dischi di Azure abilitata?
Sì, è possibile replicarle. Fare riferimento all'[articolo](azure-to-azure-how-to-enable-replication-ade-vms.md) per abilitare la replica di macchine virtuali con crittografia dischi di Azure (ADE) abilitata. Azure Site Recovery supporta attualmente solo macchine virtuali di Azure che eseguono il sistema operativo Windows e quelle abilitate per la crittografia con l'app Azure AD.

### <a name="can-i-replicate-vms-to-another-subscription"></a>È possibile replicare le macchine virtuali in un'altra sottoscrizione?
Sì, è possibile replicare macchine virtuali di Azure in una sottoscrizione diversa nello stesso tenant di Azure Active Directory.
La configurazione per il ripristino di emergenza [tra sottoscrizioni](https://azure.microsoft.com/blog/cross-subscription-dr) è semplice. È possibile selezionare un'altra sottoscrizione al momento della replica.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>È possibile replicare macchine virtuali di Azure aggiunte a una certa zona, in un'altra area?
Sì, è possibile [replicare macchine virtuali di Azure aggiunte a una certa zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region), in un'altra area.

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?

Sì, è possibile escludere dischi al momento della protezione dati tramite PowerShell. Fare riferimento alle [linee guida su PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) per escludere il disco

###<a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?
La replica di macchine virtuali di Azure in un'altra area di Azure è continua. Per maggiori dettagli, verificare l'architettura della replica [da Azure ad Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>È possibile eseguire la replica delle macchine virtuali all'interno di una stessa area? Questa opzione è necessaria al fine di eseguire la migrazione di macchine virtuali?
Non è possibile usare una soluzione di ripristino di emergenza da Azure ad Azure per replicare le macchine virtuali all'interno di una stessa area.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>È possibile replicare le macchine virtuali in qualsiasi area di Azure?
Con Site Recovery è possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti tenendo presente la latenza e la sovranità dei dati. Per altre informazioni, vedere la [matrice di supporto dell'area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) di Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery richiede la connettività Internet?

No, Site Recovery non richiede la connettività Internet, bensì l'accesso agli intervalli IP e URL di Site Recovery come descritto in questo [articolo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?.
Site Recovery definisce le impostazioni per la cronologia della conservazione del punto di recupero e per una frequenza snapshot coerente con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con impostazioni predefinite di 24 ore per la conservazione del punto di recupero e di 60 minuti per la frequenza snapshot coerente con l'app.

### <a name="what-is-crash-consistent-recovery-point"></a>Cos'è il punto di ripristino coerente con l'arresto anomalo?
Il punto di ripristino coerente con l'arresto anomalo rappresenta i dati su disco come se la macchina virtuale si fosse arrestata in modo anomalo o se il cavo di alimentazione fosse stato estratto dal server al momento della creazione del time snapshot. Non include tutto ciò che era contenuto in memoria quando è stato creato lo snapshot. Attualmente, la maggior parte delle applicazioni esegue correttamente il ripristino da snapshot coerenti in caso di arresto anomalo. Un punto di ripristino coerente con l'arresto anomalo del sistema è in genere sufficiente per sistemi operativi senza database e per applicazioni quali file server, server DHCP, server di stampa e così via.

### <a name="what-is-application-consistent-recovery-point"></a>Qual'è il punto di ripristino coerente con l'applicazione? 
I punti di ripristino coerenti con l'applicazione vengono creati da snapshot coerenti con l'app che acquisiscono gli stessi dati di snapshot coerenti con l'arresto anomalo del sistema, con l'aggiunta di tutti i dati in memoria e tutte le transazioni in corso. Per via del loro contenuto aggiuntivo, gli snapshot coerenti con l'applicazione impiegano più tempo di esecuzione e sono i più coinvolti. I punti di ripristino coerenti con le applicazioni sono consigliati per sistemi operativi di database e applicazioni quali SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?
Per comprendere il modo in cui il Site Recovery genera i punti di ripristino, si consideri l'esempio di criteri di replica, il quale presenta una finestra di conservazione del punto di recupero di 24 ore e uno snapshot di frequenza coerente con l'applicazione di 1 ora.
Site Recovery crea punti coerenti con l'arresto anomalo del sistema ogni 5 minuti; l'utente non ha alcun controllo sulla modifica di questa frequenza. Pertanto, per l'ultima ora l'utente potrà scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e 1 punto coerente con l'applicazione. Col passare del tempo, Site Recovery taglia tutti i punti di ripristino oltre l'ultima ora e salva solamente un punto di ripristino per ogni ora.
A scopo illustrativo, nella schermata seguente:


1. Per tempistiche inferiori a 1 ora, sono disponibili punti di ripristino con frequenza di 5 minuti.
2. Per tempistiche superiori all'ultima ora, possiamo vedere che viene mantenuto un solo punto di ripristino per ogni ora.

  ![generazione di punti di ripristino](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?
Il punto di ripristino meno recente che si può usare è di 72 ore.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Cosa succede se si dispone di un criterio di replica pari a 24 ore e non avviene alcuna generazione di punti di ripristino a causa di un problema che risale a più di 24 ore precedenti? Questo comporta l'eliminazione dei precedenti punti di ripristino?
No, Site Recovery consente di mantenere i tutti i punti di ripristino precedenti in questo caso. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Dopo aver abilitato la replica in una macchina virtuale, come si modifica il criterio di replica? 
Passare a Insieme di credenziali di Site Recovery > Infrastruttura di Site Recovery > Criteri di replica. Selezionare i criteri da modificare e salvare le modifiche. Qualsiasi modifica verrà applicata anche a tutte le repliche esistenti. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>I punti di ripristino costituiscono tutti una copia completa della macchina virtuale o sono differenti?
In caso di replica iniziale, il primo punto di ripristino che viene generato sarà una copia completa, mentre i punti di ripristino successivi conterranno modifiche differenziali.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Aumentando le finestre di conservazione dei punti di ripristino, il costo di archiviazione aumenta?
Sì, se si aumenta il periodo di conservazione da 24 ore a 72 ore, Site Recovery salverà i punti di ripristino per altre 48 ore, con le conseguenti spese di archiviazione. Ad esempio se un punto di ripristino singolo presenta modifiche differenziali di 10 GB e il costo per GB corrisponde a 0,16 $ al mese, l'addebito aggiuntivo corrisponderebbe a 1,6 $ x 48 al mese.

## <a name="failover"></a>Failover

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure mediante [PowerShell](azure-to-azure-powershell.md) per Site Recovery. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Come mantenere l'indirizzo IP pubblico in seguito al failover?

L'indirizzo IP pubblico dell'applicazione di produzione **non può essere mantenuto in caso di failover**. I carichi di lavoro avviati come parte del processo di failover devono essere assegnati a una risorsa IP pubblica di Azure disponibile nell'area di destinazione. Questo passaggio può essere eseguito manualmente o in modo automatico con i piani di ripristino. Fare riferimento all'[articolo](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) per assegnare un indirizzo IP pubblico usando il piano di ripristino.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>È possibile conservare un indirizzo IP privato durante il failover?
Sì, è possibile mantenere un indirizzo IP privato. Per impostazione predefinita, quando si abilita il ripristino di emergenza per macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni di quelle di origine. Per le macchine virtuali di Azure con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per le macchine virtuali di destinazione, se non è in uso. Fare riferimento all'[articolo](site-recovery-retain-ip-azure-vm-failover.md) per mantenere l'indirizzo IP privato con condizioni differenti.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Dopo il failover, il server non ha lo stesso indirizzo IP della macchina virtuale di origine. Per quale motivo è stato assegnato un nuovo indirizzo IP?

Site Recovery tenta di fornire l'indirizzo IP nel modo più efficiente possibile al momento del failover. Nel caso in cui sia stato adottato da un'altra macchina virtuale,viene quindi impostato come destinazione il successivo indirizzo IP disponibile. Per una spiegazione completa su come Site Recovery gestisce l'indirizzamento [leggere questo articolo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Cosa significa Punti di ripristino più recenti (valore RPO più basso)?
con questa opzione vengono prima elaborati tutti i dati inviati al servizio Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Questa opzione offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale creata dopo il failover conterrà tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>I punti di ripristino più recenti (RPO più basso) hanno un impatto sul Failover RTO (Obiettivo del tempo di ripristino)?
Sì, poiché Site Recovery elaborerà tutti i dati in sospeso prima del failover, questa opzione avrà un RTO superiore rispetto ad altre.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Che cosa significa Opzione più recente elaborata nei punti di ripristino?
Questa opzione consente di eseguire il failover di tutte le macchine virtuali del piano nel punto di ripristino più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare Punti di ripristino più recenti nelle impostazioni della macchina virtuale. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Se si esegue la replica tra due aree di Azure, che cosa accade se nel nell'area primaria si verifica un'interruzione imprevista?
È possibile attivare un failover dopo l'interruzione. Per eseguire il failover, in Site Recovery non è necessaria la connettività dall'area primaria.

## <a name="recovery-plan"></a>Piano di ripristino

### <a name="what-is-a-recovery-plan-"></a>Che cos'è un piano di ripristino?
I piani di ripristino in Site Recovery gestiscono il ripristino del failover delle macchine virtuali. Consentono anche di ottenere un ripristino costantemente accurato, ripetibile e automatizzato. Un piano di ripristino soddisfa le esigenze seguenti per l'utente:

- Definizione di un gruppo di macchine virtuali che eseguono il failover nello stesso momento.
- Definizione di dipendenze tra macchine virtuali in modo che l'applicazione venga visualizzata in modo accurato.
- Automatizzazione del ripristino con azioni manuali personalizzate in modo che si possano effettuare attività diverse dal failover delle macchine virtuali.

[Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Come si esegue la sequenziazione in un piano di ripristino?

Nel piano di ripristino, è possibile creare più gruppi per ottenere la sequenziazione. Ogni gruppo esegue un failover alla volta, ovvero le macchine virtuali che fanno parte dello stesso gruppo eseguiranno il failover insieme seguite da un altro gruppo. Controllare come [adattare le applicazioni usando il piano di ripristino.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Come trovo l'RTO di un piano di ripristino?
Per controllare l'RTO di un piano di ripristino, eseguire il failover di test del piano di ripristino e passare ai processi di Site Recovery.
Come illustrato di seguito ad esempio, un piano di ripristino di test SAP ha richiesto di 8 minuti e 59 secondi per eseguire il failover di tutte le macchine virtuali e per eseguire eventuali azioni specificate.

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>È possibile aggiungere runbook di automazione di Azure al piano di ripristino?
Sì, è possibile integrare i runbook di automazione di Azure nel piano di ripristino. [Altre informazioni](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Riprotezione e failback 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Dopo aver eseguito un failover da un'area primaria a un'area di ripristino di emergenza, le macchine virtuali in un'area di ripristino di emergenza vengono protette automaticamente?
No, quando si [esegue il failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) di macchine virtuali di Azure da un'area a un'altra, le macchine virtuali si avviano nell'area di ripristino di emergenza in uno stato non protetto. Per eseguire il failback delle macchine virtuali nell'area primaria, è necessario [riproteggere](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) le macchine virtuali nell'area secondaria.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>Al momento della riprotezione, Site Recovery esegue una replica completa dei dati dall'area secondaria all'area primaria?
A seconda della situazione, ad esempio se la macchina virtuale dell'area di origine esiste, verranno sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. I backup differenziali vengono calcolati confrontando entrambi i dischi e quindi trasferiti. Il completamento di questa operazione richiederà alcune ore. Fare riferimento all'[articolo]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) per informazioni dettagliate su ciò che accade durante la riprotezione.

### <a name="how-much-time-does-it-take-to-failback"></a>Quanto tempo si impiega per eseguire un failback?
Una volta effettuata la riprotezione, in genere la quantità di tempo è simile anche per l'esecuzione del failover dall'area primaria all'area secondaria. 

## <a name="security"></a>Sicurezza
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  
Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Sì, sono supportate sia la crittografia in transito che la [crittografia in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption).

## <a name="next-steps"></a>Passaggi successivi
* [Esaminare](azure-to-azure-support-matrix.md) i requisiti di supporto.
* [Configurare](azure-to-azure-tutorial-enable-replication.md) la replica da Azure ad Azure.
