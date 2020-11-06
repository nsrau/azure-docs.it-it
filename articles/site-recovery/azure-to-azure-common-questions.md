---
title: Domande comuni sul ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Questo articolo risponde a domande comuni sul ripristino di emergenza delle macchine virtuali di Azure quando si usa Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397951"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Domande frequenti: Ripristino di emergenza da Azure ad Azure

Questo articolo risponde a domande comuni sul ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md) .

## <a name="general"></a>Generale

### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?

Informazioni sui [costi](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) per il ripristino di emergenza delle macchine virtuali di Azure.

### <a name="how-does-the-free-tier-work"></a>Come funziona il livello gratuito?

Ogni istanza protetta con Site Recovery è gratuita per i primi 31 giorni di protezione. Dopo tale periodo, la protezione per ogni istanza è alle tariffe riepilogate in [Dettagli prezzi](https://azure.microsoft.com/pricing/details/site-recovery/). Puoi stimare i costi usando il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Sono presenti altri addebiti per Azure nei primi 31 giorni?

Sì. Anche se Azure Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, possono venire addebitati i costi per Archiviazione di Azure, transazioni di archiviazione e trasferimento dei dati. Una macchina virtuale ripristinata potrebbe anche richiedere addebiti di calcolo di Azure. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Ricerca per categorie iniziare a usare il ripristino di emergenza delle macchine virtuali di Azure?

1. [Informazioni](azure-to-azure-architecture.md) sull'architettura di ripristino di emergenza per macchine virtuali di Azure.
2. [Esaminare](azure-to-azure-support-matrix.md) i requisiti di supporto.
3. [Configurare il](azure-to-azure-how-to-enable-replication.md) ripristino di emergenza per le macchine virtuali di Azure.
4. [Eseguire un'esercitazione sul ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md) con un failover di test.
5. [Eseguire un failover completo](azure-to-azure-tutorial-failover-failback.md) in un'area di Azure secondaria.
6. [Eseguire il failback](azure-to-azure-tutorial-failback.md) dall'area secondaria all'area primaria.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Come è possibile garantire la capacità nell'area di destinazione?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità di infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery vengano distribuite nell'area di destinazione.

## <a name="replication"></a>Replica

### <a name="can-i-replicate-vms-with-disk-encryption"></a>È possibile replicare le VM con la crittografia del disco?

Sì. Site Recovery supporta il ripristino di emergenza di macchine virtuali in cui è abilitato crittografia dischi di Azure (ADE). Quando si Abilita la replica, Azure copia tutte le chiavi e i segreti di crittografia del disco necessari dall'area di origine all'area di destinazione, nel contesto utente. Se non si dispone delle autorizzazioni necessarie, l'amministratore della sicurezza può utilizzare uno script per copiare le chiavi e i segreti.

- Site Recovery supporta ADE per le macchine virtuali di Azure che eseguono Windows.
- Site Recovery supporta:
    - ADE versione 0,1, che ha uno schema che richiede Azure Active Directory (Azure AD).
    - ADE versione 1,1, che non richiede Azure AD. Per la versione 1,1, le macchine virtuali di Windows Azure devono avere dischi gestiti.
    - [Altre informazioni](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema) informazioni sugli schemi di estensione.

[Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md) per abilitare la replica di macchine virtuali crittografate.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>È possibile selezionare un account di automazione da un gruppo di risorse diverso?

Quando si consente Site Recovery di gestire gli aggiornamenti per l'estensione del servizio Mobility in esecuzione su macchine virtuali di Azure replicate, distribuisce un Runbook globale (usato dai servizi di Azure) tramite un account di automazione di Azure. È possibile usare l'account di automazione creato da Site Recovery oppure selezionare per usare un account di automazione esistente. 

Attualmente, nel portale, è possibile selezionare solo un account di automazione nello stesso gruppo di risorse dell'insieme di credenziali. È possibile selezionare un account di automazione da un gruppo di risorse diverso usando PowerShell. [Altre informazioni](azure-to-azure-autoupdate.md#enable-automatic-updates)

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Se si usa un account di automazione del cliente che non si trova nel gruppo di risorse dell'insieme di credenziali, è possibile eliminare il valore predefinito di Runbook?

Sì, è possibile eliminarlo se non è necessario. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>È possibile replicare le macchine virtuali in un'altra sottoscrizione?

Sì, è possibile replicare le macchine virtuali di Azure in qualsiasi sottoscrizione all'interno dello stesso tenant Azure AD. Quando si Abilita il ripristino di emergenza per le VM, per impostazione predefinita la sottoscrizione di destinazione visualizzata corrisponde a quella della VM di origine. È possibile modificare la sottoscrizione di destinazione e altre impostazioni, ad esempio il gruppo di risorse e la rete virtuale, vengono popolate automaticamente dalla sottoscrizione selezionata.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>È possibile replicare le macchine virtuali in una zona di disponibilità in un'altra area?

Sì, è possibile replicare le macchine virtuali nelle zone di disponibilità in un'altra area di Azure. La macchina virtuale di destinazione può essere distribuita come una singola istanza, in un set di disponibilità o in una zona di disponibilità, se supportata nell'area di destinazione. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>È possibile replicare macchine virtuali non di zona in una zona all'interno della stessa area? 

Questa operazione non è supportata nel portale. Per eseguire questa operazione, è possibile usare l'API REST o PowerShell.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>È possibile replicare le macchine virtuali in zone in un'area diversa nella stessa area?

Il supporto per questa operazione è limitato a un paio di aree. [Altre informazioni](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)

### <a name="can-i-exclude-disks-from-replication"></a>È possibile escludere I dischi dalla replica?

Sì, è possibile escludere i dischi quando si configura la replica usando PowerShell. [Altre informazioni](azure-to-azure-exclude-disks.md)

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>È possibile replicare nuovi dischi aggiunti alle macchine virtuali replicate?

Per le macchine virtuali replicate con Managed disks, è possibile aggiungere nuovi dischi e abilitarne la replica. Quando si aggiunge un nuovo disco, la macchina virtuale replicata Mostra un messaggio di avviso che indica che uno o più dischi nella macchina virtuale sono disponibili per la protezione. 

- Se si Abilita la replica per i dischi aggiunti, l'avviso scompare dopo la replica iniziale.
- Se non si vuole abilitare la replica per il disco, è possibile ignorare l'avviso.
- Se si esegue il failover di una macchina virtuale con dischi aggiunti, i punti di replica mostrano i dischi disponibili per il ripristino. Ad esempio, se si aggiunge un secondo disco a una macchina virtuale con un disco, un punto di replica creato prima dell'aggiunta viene visualizzato come "1 di 2 dischi".

Site Recovery non supporta la rimozione a caldo dei dischi da una macchina virtuale replicata. Se si rimuove un disco della macchina virtuale, è necessario disabilitare e quindi riabilitare la replica per la macchina virtuale.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali di Azure in un'altra area di Azure è continua. [Altre](./azure-to-azure-architecture.md#replication-process) informazioni sul funzionamento della replica.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>È possibile eseguire la replica delle macchine virtuali all'interno di un'area? 

Non è possibile usare Site Recovery per replicare i dischi all'interno di un'area.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>È possibile replicare istanze di macchine virtuali in qualunque area di Azure?

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti con la latenza e la sovranità dei dati. [Altre](./azure-to-azure-support-matrix.md#region-support) informazioni sul supporto per le aree.

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery necessaria la connettività Internet?

No, ma le macchine virtuali devono accedere a Site Recovery URL e intervalli di indirizzi IP. [Altre informazioni](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>È possibile replicare un'applicazione a livelli tra gruppi di risorse?

Sì, è possibile replicare l'app e mantengono la configurazione del ripristino di emergenza in un gruppo di risorse distinto.

Se, ad esempio, le app hanno tre livelli (applicazione/database/Web) in gruppi di risorse diversi, è necessario abilitare la replica tre volte per proteggere tutti i livelli. Site Recovery replica i tre livelli in tre gruppi di risorse diversi.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>È possibile spostare gli account di archiviazione tra gruppi di risorse?

No, questa operazione non è supportata. Se si spostano accidentalmente gli account di archiviazione in un gruppo di risorse diverso ed eliminare il gruppo di risorse originale, è possibile creare un nuovo gruppo di risorse con lo stesso nome del gruppo di risorse precedente, quindi spostare l'account di archiviazione in questo gruppo di risorse.

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?

Un criterio di replica definisce la cronologia di conservazione dei punti di ripristino e la frequenza degli snapshot coerenti con l'app.  Site Recovery crea un criterio di replica predefinito come segue:

- Mantenere i punti di ripristino per 24 ore.
- Eseguire snapshot coerenti con l'app ogni quattro ore.

[Altre](azure-to-azure-how-to-enable-replication.md#customize-target-resources) informazioni sulle impostazioni di replica.

### <a name="whats-a-crash-consistent-recovery-point"></a>Che cos'è un punto di ripristino coerente con l'arresto anomalo del sistema?

Un punto di ripristino coerente con l'arresto anomalo del sistema contiene dati su disco, come se fosse stato eseguito il pull del cavo di alimentazione dal server durante lo snapshot. Non include tutto ciò che si trovava in memoria quando lo snapshot è stato utilizzato.

Attualmente, la maggior parte delle app è in grado di recuperare bene da snapshot coerenti con l'arresto anomalo. Un punto di ripristino coerente con l'arresto anomalo del sistema è in genere sufficiente per i sistemi operativi non di database e per le app quali file server, server DHCP e server di stampa.

Site Recovery crea automaticamente un punto di ripristino coerente con l'arresto anomalo del sistema ogni cinque minuti.

### <a name="whats-an-application-consistent-recovery-point"></a>Che cos'è un punto di ripristino coerente con l'applicazione?

I punti di ripristino coerenti con l'app vengono creati dagli snapshot coerenti con l'app. Acquisiscono gli stessi dati degli snapshot coerenti con l'arresto anomalo del sistema, oltre a acquisire i dati in memoria e tutte le transazioni in corso.

A causa del contenuto aggiuntivo, gli snapshot coerenti con l'app sono i più interessati e hanno più tempo. Si consigliano punti di ripristino coerenti con l'app per i sistemi operativi del database e app come SQL Server. Per Windows, gli snapshot coerenti con l'app usano il Servizio Copia Shadow del volume (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>I punti di ripristino coerenti con l'app hanno un effetto sulle prestazioni?

 Poiché i punti di ripristino coerenti con l'app acquisiscono tutti i dati in memoria ed elaborati, se acquisiscono di frequente, possono influire sulle prestazioni quando il carico di lavoro è già occupato. Non è consigliabile acquisire troppo spesso per i carichi di lavoro non di database. Anche per i carichi di lavoro del database, un'ora dovrebbe essere sufficiente.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Qual è la frequenza minima per la generazione di punti di ripristino coerenti con l'app?

Site Recovery possibile creare punti di ripristino coerenti con l'app con una frequenza minima di un'ora.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>È possibile abilitare la replica coerente con l'app per le VM Linux?

Sì. L'agente di mobilità per Linux supporta gli script personalizzati per la coerenza delle app. L'agente usa uno script personalizzato con pre e post-Options. [Scopri di più](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?

Per comprendere il modo in cui Site Recovery genera i punti di ripristino, si userà un esempio. 

- Un criterio di replica mantiene i punti di ripristino per 24 ore e accetta ogni ora uno snapshot di frequenza coerente con l'app.
- Site Recovery crea un punto di ripristino coerente con l'arresto anomalo del sistema ogni cinque minuti. L'utente non può modificare questa frequenza.
- Site Recovery pota i punti di ripristino dopo un'ora, salvando un punto all'ora.

Nell'ultima ora è possibile scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e un punto coerente con l'app, come illustrato nell'immagine.

   ![Elenco dei punti di recupero generati](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Il punto di recupero meno recente che è possibile usare è di 72 ore.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Cosa accade se Site Recovery non è in grado di generare punti di ripristino per più di 24 ore? 

Se si dispone di un criterio di replica di 24 ore e Site Recovery non è in grado di generare punti di ripristino per più di 24 ore, i punti di ripristino precedenti rimangono. Site Recovery sostituisce solo il punto meno recente se genera nuovi punti. Fino a quando non sono presenti nuovi punti di ripristino, tutti i vecchi punti rimangono dopo aver raggiunto l'intervallo di conservazione.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>È possibile modificare i criteri di replica dopo l'abilitazione della replica?

Sì. Nell'insieme di credenziali > **Site Recovery**  >  **criteri di replica** dell'infrastruttura, selezionare e modificare i criteri. Le modifiche si applicano anche ai criteri esistenti.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Tutti i punti di ripristino sono copiati da una macchina virtuale completa?

Il primo punto di recupero che viene generato include la copia completa. I punti di ripristino successivi includono modifiche delta.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Aumenta la conservazione dei punti di ripristino aumenta i costi di archiviazione?

Sì. Se, ad esempio, si aumenta il periodo di conservazione da 24 ore a 72, Site Recovery Salva i punti di ripristino per altre 48 ore. L'ora aggiuntiva comporta modifiche all'archiviazione. Se, ad esempio, un singolo punto di ripristino presenta variazioni Delta di 10 GB, con un costo per GB di $0,16 al mese, gli addebiti aggiuntivi saranno $1,60 × 48 al mese.

## <a name="multi-vm-consistency"></a>Coerenza tra più macchine virtuali

### <a name="what-is-multi-vm-consistency"></a>Che cos'è la coerenza tra più macchine virtuali?

La coerenza tra più VM garantisce che i punti di ripristino siano coerenti tra le macchine virtuali replicate.

- Quando si Abilita la coerenza tra più macchine virtuali, Site Recovery crea un gruppo di replica di tutti i computer con l'opzione abilitata. 
- Quando si esegue il failover delle macchine virtuali nel gruppo di replica, i punti di ripristino coerenti con l'arresto anomalo e coerenti con l'app sono stati condivisi.

[Informazioni](azure-to-azure-tutorial-enable-replication.md#enable-replication) su come abilitare la coerenza tra più macchine virtuali.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>È possibile eseguire il failover di una singola macchina virtuale in un gruppo di replica?

No. Quando si Abilita la coerenza tra più VM, viene dedotto che un'app presenta una dipendenza da tutte le macchine virtuali nel gruppo di replica e il failover di una singola macchina virtuale non è consentito.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Quante VM è possibile replicare insieme in un gruppo?

È possibile replicare 16 macchine virtuali insieme in un gruppo di replica.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando è necessario abilitare la coerenza tra più macchine virtuali?

La coerenza tra più macchine virtuali è a elevato utilizzo di CPU e abilitarla può influire sulle prestazioni del carico di lavoro Abilitare solo se le macchine virtuali eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più computer. Se, ad esempio, si dispone di due istanze di SQL Server e di due server Web in un'applicazione, abilitare la coerenza tra più macchine virtuali solo per le istanze di SQL Server.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>È possibile aggiungere una macchina virtuale di replica a un gruppo di replica?

Quando si Abilita la replica per una macchina virtuale, è possibile aggiungerla a un nuovo gruppo di replica o a un gruppo esistente. Non è possibile aggiungere una macchina virtuale che sta già eseguendo la replica in un gruppo. 
 
## <a name="failover"></a>Failover

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Come è possibile garantire la capacità nell'area di destinazione?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità di infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery possano essere distribuite nell'area di destinazione.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il failover non è automatico. È possibile avviare un failover con un singolo clic nel portale oppure usare  [PowerShell](azure-to-azure-powershell.md) per attivare un failover.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>È possibile gestire un indirizzo IP pubblico dopo il failover?

Non è possibile gestire l'indirizzo IP pubblico per un'app di produzione dopo un failover.

Quando si apre un carico di lavoro come parte del processo di failover, è necessario assegnare una risorsa indirizzo IP pubblico di Azure. La risorsa deve essere disponibile nell'area di destinazione. La risorsa indirizzo IP pubblico di Azure può essere assegnata manualmente oppure è possibile automatizzarla con un piano di ripristino. [Informazioni](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) su come configurare gli indirizzi IP pubblici dopo il failover.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>È possibile gestire un indirizzo IP privato dopo il failover?

Sì. Per impostazione predefinita, quando si Abilita il ripristino di emergenza per le macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni delle risorse di origine. Per le macchine virtuali di Azure con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per le macchine virtuali di destinazione, se non è in uso.
[Altre informazioni su](site-recovery-retain-ip-azure-vm-failover.md) come mantenere gli indirizzi IP dopo il failover.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Perché una macchina virtuale ha assegnato un nuovo indirizzo IP dopo il failover?

Site Recovery tenta di fornire l'indirizzo IP al momento del failover. Se un'altra macchina virtuale usa tale indirizzo, Site Recovery imposta il successivo indirizzo IP disponibile come destinazione.

[Altre informazioni sulla](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) configurazione del mapping di rete e degli indirizzi IP per le reti virtuali.

### <a name="whats-the-latest-recovery-point"></a>Qual è il punto di ripristino *più recente* ?

L'opzione del punto di ripristino *più recente (RPO più basso)* esegue le operazioni seguenti:

1. Elabora innanzitutto tutti i dati inviati a Site Recovery.
2. Dopo che il servizio ha elaborato i dati, viene creato un punto di ripristino per ogni macchina virtuale, prima del failover alla macchina virtuale. Questa opzione assicura l'obiettivo del punto di ripristino (RPO) più basso.
3. La macchina virtuale creata dopo il failover ha tutti i dati replicati in Site Recovery, da quando è stato attivato il failover.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>I punti di ripristino *più recenti* influiscano sul failover RTO?

Sì. Site Recovery elabora tutti i dati in sospeso prima del failover, pertanto questa opzione ha un obiettivo del tempo di ripristino (RTO) superiore rispetto alle altre opzioni.

### <a name="whats-the-latest-processed-recovery-option"></a>Qual è l'opzione di ripristino *elaborata più recente* ?

L'opzione *elaborata più recente* esegue le operazioni seguenti:

1. Esegue il failover di tutte le macchine virtuali nel punto di ripristino più recente elaborato da Site Recovery. Questa opzione offre un RTO basso poiché non viene impiegato tempo per la gestione di dati non elaborati.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Che cosa accade se si verifica un'interruzione imprevista nell'area primaria?

È possibile avviare il failover. Per eseguire il failover, in Site Recovery non è necessaria la connettività dall'area primaria.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Qual è il RTO di un failover della macchina virtuale?

Site Recovery dispone di un contratto di RTO di [due ore](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Nella maggior parte dei casi, Site Recovery esegue il failover delle macchine virtuali in pochi minuti. Per calcolare il RTO, esaminare il processo di failover, che mostra il tempo impiegato per visualizzare una macchina virtuale. 

## <a name="recovery-plans"></a>Piani di ripristino

### <a name="whats-a-recovery-plan"></a>Che cos'è un piano di ripristino?

Un [piano di ripristino](site-recovery-create-recovery-plans.md) in Site Recovery orchestra il failover e il ripristino delle macchine virtuali. Consente di eseguire il ripristino in modo accurato, ripetibile e automatizzato. Esegue le operazioni seguenti:

- Definisce un gruppo di macchine virtuali di cui viene eseguito il failover
- Definisce le dipendenze tra le macchine virtuali, in modo che l'applicazione venga visualizzata in modo accurato.
- Automatizza il ripristino, con l'opzione di azioni manuali personalizzate per attività diverse dal failover della macchina virtuale. 


### <a name="how-does-sequencing-work"></a>Come funziona la sequenziazione?

In un piano di ripristino è possibile creare più gruppi di macchine virtuali per la sequenziazione. Il failover dei gruppi viene eseguito una sola volta, in modo che le macchine virtuali che fanno parte dello stesso gruppo vengano sottoutilizzate insieme. [Altre informazioni](recovery-plan-overview.md#model-apps)

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Come trovo l'RTO di un piano di ripristino?

Per controllare il RTO di un piano di ripristino, eseguire un failover di test per il piano di ripristino. In **Site Recovery processi** controllare la durata del failover di test. Nello screenshot di esempio, il processo di failover di test di **SAPTestRecoveryPlan** ha richiesto 8 minuti e 59 secondi.

![Elencare i processi che mostrano la durata del failover di test per RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>È possibile aggiungere manuali operativi di automazione ai piani di ripristino?

Sì. [Altre informazioni](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Riprotezione e failback

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Dopo il failover, le macchine virtuali nell'area secondaria vengono protette automaticamente? 

No. Quando si esegue il failover delle macchine virtuali da un'area a un'altra, le macchine virtuali vengono avviate nell'area di ripristino di emergenza di destinazione in uno stato non protetto. Per [riproteggere](./azure-to-azure-how-to-reprotect.md) le macchine virtuali nell'area secondaria, è possibile abilitare la replica nell'area primaria.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Quando si esegue la riprotezione, tutti i dati vengono replicati dall'area secondaria a quella primaria? 

Dipende da. Se la macchina virtuale dell'area di origine esiste, verranno sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. Site Recovery Confronta i dischi con quelli diversi e quindi trasferisce i dati. Questo processo richiede in genere alcune ore. [Altre informazioni](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)

### <a name="how-long-does-it-take-fail-back"></a>Quanto tempo è opportuno eseguire il failback?

Dopo la riprotezione, il failback richiede circa la stessa quantità di tempo impiegato per eseguire il failover dall'area primaria a un'area secondaria.

## <a name="capacity"></a><a name="capacity"></a>Capacità

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Come è possibile garantire la capacità nell'area di destinazione?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery possano essere distribuite nell'area di destinazione.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funzionano con le istanze riservate?

Sì, è possibile acquistare [macchine virtuali di Azure riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) nell'area di ripristino di emergenza e Site Recovery le operazioni di failover le usano. Non è necessaria alcuna configurazione aggiuntiva.

## <a name="security"></a>Sicurezza

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?

No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA. Il servizio è in fase di valutazione per la conformità SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?

Sì, sono supportate sia la crittografia dei dati in transito che la [crittografia dei dati inattivi in Azure](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare i requisiti di supporto da Azure ad Azure](azure-to-azure-support-matrix.md).
- [Configurare la replica da Azure ad Azure](azure-to-azure-tutorial-enable-replication.md).
- Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nella [pagina delle domande di Domande e risposte Microsoft relativa a Servizi di ripristino di Azure](/answers/topics/azure-site-recovery.html).
