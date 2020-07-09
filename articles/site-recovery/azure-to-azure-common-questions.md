---
title: Domande comuni sul ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Questo articolo risponde a domande comuni sul ripristino di emergenza delle macchine virtuali di Azure quando si usa Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 22848d84896989b1872c55e687c4a5e73da31de8
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134046"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Domande frequenti: Ripristino di emergenza da Azure ad Azure

Questo articolo risponde a domande comuni sul ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure per l'uso di [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Generale

### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?

Vedere [Prezzi di Azure Site Recovery per macchine virtuali](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Come funziona il livello gratuito di Azure Site Recovery?

Ogni istanza protetta con Azure Site Recovery è gratuita per i primi 31 giorni di protezione. Dopo tale periodo, la protezione per ogni istanza è addebitata ai costi indicati in [Prezzi di Azure Site Recovery per le macchine virtuali di Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, sono previsti altri addebiti per Azure?

Sì. Anche se Azure Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, possono venire addebitati i costi per Archiviazione di Azure, transazioni di archiviazione e trasferimento dei dati. Per una macchina virtuale ripristinata possono venire addebitati anche i costi di calcolo di Azure. Per informazioni dettagliate sui prezzi, vedere [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quali sono le procedure consigliate per il ripristino di emergenza di macchine virtuali di Azure?

1. [Informazioni sull'architettura da Azure ad Azure](azure-to-azure-architecture.md)
1. [Esaminare le configurazioni supportate e non supportate](azure-to-azure-support-matrix.md)
1. [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-how-to-enable-replication.md)
1. [Eseguire un failover di test](azure-to-azure-tutorial-dr-drill.md)
1. [Eseguire il failover e il failback nell'area primaria](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Come viene garantita la capacità nell'area di destinazione?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

## <a name="replication"></a>Replica

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>È possibile replicare le macchine virtuali abilitate con Crittografia dischi di Azure?

Sì. Site Recovery supporta il ripristino di emergenza di macchine virtuali in cui è abilitata Crittografia dischi di Azure. Quando si abilita la replica, Azure copia tutte le chiavi e i segreti di crittografia del disco necessari dall'area di origine all'area di destinazione nel contesto utente. Se non si dispone delle autorizzazioni appropriate, l'amministratore della sicurezza può usare uno script per copiare le chiavi e i segreti.

- Site Recovery supporta Crittografia dischi di Azure per macchine virtuali di Azure che eseguono Windows.
- Site Recovery supporta Crittografia dischi di Azure versione 0.1, che dispone di uno schema che richiede Azure Active Directory (Azure AD). Site Recovery supporta anche la versione 1.1, che non richiede Azure AD. [Altre informazioni sullo schema di estensione per Crittografia dischi di Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Per Crittografia dischi di Azure versione 1.1, è necessario usare le macchine virtuali Windows con dischi gestiti.
  - [Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md) per abilitare la replica di macchine virtuali crittografate.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>È possibile selezionare un account di automazione da un gruppo di risorse diverso?

Questa operazione non è attualmente supportata tramite il portale, ma è possibile scegliere un account di automazione da un gruppo di risorse diverso tramite PowerShell.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>Dopo aver specificato un account di automazione che si trova in un gruppo di risorse diverso da quello dell'insieme di credenziali, è possibile eliminare il Runbook se non sono presenti altri insiemi di credenziali da specificare?

Il Runbook personalizzato creato è uno strumento ed è sicuro da eliminare se lo stesso non è più necessario.

### <a name="can-i-replicate-vms-to-another-subscription"></a>È possibile replicare le macchine virtuali in un'altra sottoscrizione?

Sì, è possibile replicare macchine virtuali di Azure in una sottoscrizione diversa nello stesso tenant di Azure AD.

Configurare il ripristino di emergenza [tra sottoscrizioni](https://azure.microsoft.com/blog/cross-subscription-dr) selezionando un'altra sottoscrizione al momento della replica.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>È possibile replicare in un'altra area le macchine virtuali di Azure aggiunte a una certa zona?

Sì, è possibile [replicare le macchine virtuali aggiunte a una certa zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) in un'altra area.

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?

Sì, è possibile escludere dischi al momento della protezione tramite PowerShell. Per altre informazioni, vedere [come escludere dischi dalla replica](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>È possibile aggiungere nuovi dischi alle macchine virtuali replicate e abilitarne la replica?

Sì, l'aggiunta di nuovi dischi alle macchine virtuali replicate e l'abilitazione della loro replica è supportata per le macchine virtuali di Azure con dischi gestiti. Quando si aggiunge un nuovo disco a una macchina virtuale di Azure abilitata per la replica, l'integrità della replica per la VM visualizza un avviso. Questo avviso indica che uno o più dischi nella macchina virtuale sono disponibili per la protezione. È possibile abilitare la replica per i dischi aggiunti.

- Se si abilita la protezione per i dischi aggiunti, l'avviso scomparirà dopo la replica iniziale.
- Se non si abilita la replica per il disco, è possibile ignorare l'avviso.
- Se si esegue il failover di una macchina virtuale con un disco aggiunto e la replica abilitata, sono presenti punti di replica. I punti di replica visualizzeranno i dischi disponibili per il ripristino.

Si immagini, ad esempio, che una macchina virtuale disponga di un solo disco e che ne venga aggiunto uno nuovo. È possibile che sia stato creato un punto di replica prima di aggiungere il disco. Questo punto di replica indicherà che è costituito da "1 di 2 dischi".

Site Recovery non supporta la "rimozione a caldo" di un disco da una macchina virtuale replicata. Se si rimuove un disco della macchina virtuale, è necessario disabilitare e quindi riabilitare la replica per la macchina virtuale.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali di Azure in un'altra area di Azure è continua. Per altre informazioni, vedere [Architettura di replica da Azure ad Azure](./azure-to-azure-architecture.md#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>È possibile eseguire la replica delle macchine virtuali all'interno di un'area? Questa funzionalità è necessaria per eseguire la migrazione delle macchine virtuali.

Non è possibile usare una soluzione di ripristino di emergenza da Azure ad Azure per replicare le macchine virtuali all'interno di un'area.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>È possibile replicare istanze di macchine virtuali in qualunque area di Azure?

Con Site Recovery è possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti con la latenza e la sovranità dei dati. Per altre informazioni, vedere la [matrice di supporto dell'area](./azure-to-azure-support-matrix.md#region-support) di Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery richiede la connettività Internet?

No, Site Recovery non richiede la connettività Internet. È tuttavia necessario l'accesso agli intervalli IP e agli URL di Site Recovery, come indicato in [Gestione delle reti nel ripristino di emergenza delle VM di Azure](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>È possibile eseguire la replica di un'applicazione con un gruppo di risorse separato per livelli diversi?

Sì, è possibile eseguire la replica dell'applicazione e mantenere la configurazione di ripristino di emergenza in un gruppo di risorse separato.

Se ad esempio si ha un'applicazione con app, database e Web di ogni livello in un gruppo di risorse separato, è necessario fare clic tre volte sulla [procedura guidata di replica](./azure-to-azure-how-to-enable-replication.md#enable-replication) per proteggere tutti i livelli. Site Recovery eseguirà la replica dei tre livelli in tre gruppi di risorse distinti.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>È possibile spostare gli account di archiviazione tra gruppi di risorse?

No, questo scenario non è supportato. Tuttavia, se si spostano accidentalmente gli account di archiviazione in un gruppo di risorse diverso e si elimina il gruppo di risorse originale, è possibile creare un nuovo gruppo di risorse con lo stesso nome del gruppo di risorse precedente, quindi spostare l'account di archiviazione in questo gruppo di risorse.

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?

I criteri di replica definiscono le impostazioni per la cronologia di conservazione dei punti di recupero. Definiscono inoltre la frequenza degli snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con le impostazioni predefinite seguenti:

- 24 ore per la cronologia della conservazione dei punti di recupero.
- 60 minuti per la frequenza degli snapshot coerenti con l'app.

[Altre informazioni sulle impostazioni di replica](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Che cos'è il punto di recupero coerente con l'arresto anomalo del sistema?

Un punto di recupero coerente con l'arresto anomalo del sistema dispone dei dati su disco come se fosse stato rimosso il cavo di alimentazione dal server durante lo snapshot. Non include tutto ciò che era contenuto in memoria quando è stato creato lo snapshot.

La maggior parte delle applicazioni esegue ora correttamente il ripristino da snapshot coerenti con l'arresto anomalo del sistema. Un punto di recupero coerente con l'arresto anomalo del sistema è in genere sufficiente per sistemi operativi senza database e per applicazioni quali file server, server DHCP e server di stampa.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual è la frequenza di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti.

### <a name="what-is-an-application-consistent-recovery-point"></a>Che cos'è un punto di recupero coerente con l'applicazione?

I punti di recupero coerenti con l'applicazione vengono creati dagli snapshot coerenti con l'applicazione. I punti di recupero coerenti con l'applicazione acquisiscono gli stessi dati di snapshot coerenti con l'arresto anomalo del sistema, oltre a tutti i dati in memoria e a tutte le transazioni in corso.

Per via del loro contenuto aggiuntivo, gli snapshot coerenti con l'applicazione impiegano più tempo di esecuzione e sono i più usati. È consigliabile usare i punti di recupero coerenti con l'applicazione per sistemi operativi e applicazioni di database come SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual è l'impatto dei punti di recupero coerenti con l'applicazione sulle prestazioni dell'applicazione?

I punti di recupero coerenti con l'applicazione acquisiscono tutti i dati in memoria e in corso. Poiché i punti di recupero acquisiscono tali dati, richiedono framework come Servizio Copia Shadow del volume in Windows per disattivare l'applicazione. Se il processo di acquisizione è frequente, può influire sulle prestazioni quando il carico di lavoro è già elevato. Non è consigliabile usare una frequenza bassa per i punti di recupero coerenti con l'applicazione per carichi di lavoro non di database. Anche per il carico di lavoro del database, 1 ora è sufficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual è la frequenza minima di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery può creare un punto di recupero coerente con l'applicazione con una frequenza minima di 1 ora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?

Per comprendere il modo in cui Site Recovery genera i punti di recupero, è possibile esaminare un esempio di criteri di replica. Questo criterio di replica dispone di un punto di recupero con un intervallo di conservazione di 24 ore e uno snapshot di frequenza coerente con l'applicazione di 1 ora.

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti. L'utente non può modificare questa frequenza. Nell'ultima ora è possibile scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e 1 punto coerente con l'applicazione. Col passare del tempo, Site Recovery elimina tutti i punti di recupero oltre l'ultima ora e salva solo 1 punto di recupero per ogni ora.

Lo screenshot seguente illustra l'esempio. Nello screenshot:

- Nell'ultima ora, sono disponibili punti di recupero con una frequenza di 5 minuti.
- Oltre l'ultima ora, Site Recovery conserva un solo punto di recupero.

   ![Elenco dei punti di recupero generati](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Il punto di recupero meno recente che è possibile usare è di 72 ore.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ho un criterio di replica di 24 ore. Cosa accade se un problema impedisce a Site Recovery di generare punti di recupero per più di 24 ore? I precedenti punti di recupero verranno persi?

No, Site Recovery manterrà tutti i punti di recupero precedenti. A seconda dell'intervallo di conservazione dei punti di recupero, Site Recovery sostituisce il punto meno recente solo se genera nuovi punti. A causa del problema, Site Recovery non è in grado di generare nuovi punti di recupero. Fino a quando non saranno presenti nuovi punti di recupero, tutti i punti precedenti verranno conservati dopo aver raggiunto l'intervallo di conservazione.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Dopo aver abilitato la replica in una macchina virtuale, come si modifica il criterio di replica?

Passare a **Insieme di credenziali di Site Recovery** > **Infrastruttura di Site Recovery** > **Criteri di replica**. Selezionare i criteri da modificare e salvare le modifiche. Qualsiasi modifica verrà applicata anche a tutte le repliche esistenti.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tutti i punti di recupero includono una copia completa della macchina virtuale o solo una copia differenziale?

Il primo punto di recupero che viene generato include la copia completa. I punti di recupero successivi includono le modifiche delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>L'aumento del periodo di conservazione dei punti di recupero comporta l'aumento dei costi di archiviazione?

Sì, se si aumenta il periodo di conservazione da 24 a 72 ore, Site Recovery salverà i punti di recupero per altre 48 ore. Il tempo aggiuntivo comporterà dei costi di archiviazione. Ad esempio, un singolo punto di recupero potrebbe avere variazioni delta di 10 GB con un costo per GB di $0,16 al mese. Gli addebiti aggiuntivi saranno $1,60 × 48 al mese.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>È possibile abilitare la replica con la coerenza delle app nei server Linux?

Sì. Azure Site Recovery per il sistema operativo Linux supporta gli script personalizzati dell'applicazione per la coerenza delle app. Lo script personalizzato con pre e post-Options verrà usato dall'agente di Azure Site Recovery Mobility durante la coerenza dell'app. [Scopri di più](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>Coerenza tra più macchine virtuali

### <a name="what-is-multi-vm-consistency"></a>Che cos'è la coerenza tra più macchine virtuali?

La coerenza tra più macchine virtuali garantisce che il punto di recupero sia coerente in tutte le macchine virtuali replicate.

Site Recovery offre un'opzione di **coerenza tra più macchine virtuali**, che consente di creare un gruppo di replica di tutti i computer.

Quando saranno sottoposte a failover, le macchine virtuali condivideranno punti di recupero coerenti con l'arresto anomalo del sistema e con l'applicazione.

Per [abilitare la coerenza tra più macchine virtuali](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm), seguire i passaggi nell'esercitazione.

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>È possibile eseguire il failover di una singola macchina virtuale all'interno di un gruppo di replica per la coerenza tra più macchine virtuali?

Quando si seleziona l'opzione **Coerenza tra più macchine virtuali**, si dichiara che l'applicazione ha una dipendenza su tutte le macchine virtuali all'interno di un gruppo. Il failover di una singola macchina virtuale non è consentito.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quante macchine virtuali è possibile replicare come parte di un gruppo di replica per la coerenza tra più macchine virtuali?

È possibile replicare 16 macchine virtuali all'interno di uno stesso gruppo di replica.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando è necessario abilitare la coerenza tra più macchine virtuali?

Poiché comporta un uso intensivo della CPU, l'abilitazione della coerenza tra più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Usare la coerenza tra più macchine virtuali solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più macchine virtuali. Se ad esempio sono presenti due istanze di SQL Server e due server Web in un'applicazione, è necessaria la coerenza tra più macchine virtuali solo per le istanze di SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>È possibile aggiungere una macchina virtuale già replicata a un gruppo di replica?
È possibile aggiungere una macchina virtuale a un nuovo gruppo di replica abilitando la replica. È anche possibile aggiungere una macchina virtuale a un gruppo di replica esistente abilitando la replica. Tuttavia, non è possibile aggiungere una macchina virtuale già replicata a un nuovo gruppo di replica o a un gruppo di replica esistente.
 
## <a name="failover"></a>Failover


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le VM di Azure?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il failover non è automatico. Può essere avviato con un singolo clic nel portale oppure è possibile usare [PowerShell](azure-to-azure-powershell.md) per attivare un failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>È possibile mantenere l'indirizzo IP pubblico in seguito a un failover?

Non è possibile mantenere l'indirizzo IP pubblico dell'applicazione di produzione dopo un failover.

Quando si apre un carico di lavoro come parte del processo di failover, è necessario assegnare una risorsa IP pubblico di Azure al carico di lavoro. La risorsa IP pubblico di Azure deve essere disponibile nell'area di destinazione. È possibile assegnare la risorsa IP pubblico di Azure manualmente oppure è possibile automatizzarla con un piano di ripristino. Informazioni su come [configurare indirizzi IP pubblici dopo il failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>È possibile mantenere un indirizzo IP privato durante un failover?

Sì, è possibile mantenere un indirizzo IP privato. Per impostazione predefinita, quando si abilita il ripristino di emergenza per macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni di quelle di origine. Per le macchine virtuali di Azure configurate con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per le macchine virtuali di destinazione, se non è in uso.
Informazioni su come [mantenere indirizzi IP durante il failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dopo un failover, per quale motivo viene assegnato un nuovo indirizzo IP al server?

Site Recovery tenta di fornire l'indirizzo IP al momento del failover. Se un'altra macchina virtuale richiede tale indirizzo, Site Recovery imposta il successivo indirizzo IP disponibile come destinazione.

Altre informazioni su come [configurare il mapping di rete e impostare gli indirizzi IP per le reti virtuali](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Che cosa sono i punti di recupero **più recenti (valore RPO più basso)** ?

L'opzione **Più recente (RPO più basso)** elabora prima tutti i dati inviati a Site Recovery. Dopo che il servizio ha elaborato i dati, viene creato un punto di recupero per ogni macchina virtuale prima del failover sulla macchina virtuale. Questa opzione assicura l'obiettivo del punto di ripristino (RPO) più basso. Per la macchina virtuale creata dopo il failover, tutti i dati vengono replicati in Site Recovery da quando è stato attivato il failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>I punti di recupero **più recenti (RPO più basso)** hanno un impatto sul failover RTO (Recovery Time Objective)?

Sì. Site Recovery elabora tutti i dati in sospeso prima dell'esecuzione del failover, pertanto questa opzione ha un obiettivo del tempo di ripristino (RTO) maggiore rispetto ad altre opzioni.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Che cosa significa l'opzione **Elaborato più recente** nei punti di recupero?

L'opzione **Elaborato più recente** consente di eseguire il failover di tutte le macchine virtuali del piano nel punto di recupero più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare **Punti di ripristino più recenti** nelle impostazioni della macchina virtuale. Questa opzione offre un RTO basso poiché non viene impiegato tempo per la gestione di dati non elaborati.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Cosa accade se si verifica un'interruzione imprevista dell'area primaria?

Dopo l'interruzione è possibile attivare un failover. Per eseguire il failover, in Site Recovery non è necessaria la connettività dall'area primaria.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Qual è l'RTO del failover di una macchina virtuale?

Site Recovery prevede un [RTO del contratto di servizio di 2 ore](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Tuttavia, nella maggior parte dei casi, Site Recovery esegue il failover delle macchine virtuali in pochi minuti. È possibile calcolare l'RTO esaminando i processi di failover, che mostrano il tempo impiegato per aprire la macchina virtuale. Per l'RTO del piano di ripristino, fare riferimento alla sezione successiva.

## <a name="recovery-plans"></a>Piani di ripristino

### <a name="what-is-a-recovery-plan"></a>Che cos'è un piano di ripristino?

I piani di ripristino in Site Recovery gestiscono il ripristino del failover delle macchine virtuali. Consentono anche di ottenere un ripristino costantemente accurato, ripetibile e automatizzato. Un piano di ripristino soddisfa le esigenze seguenti:

- Definizione di un gruppo di macchine virtuali che eseguono il failover contemporaneamente.
- Definizione di dipendenze tra macchine virtuali, affinché l'applicazione venga visualizzata in modo accurato.
- Automatizzazione del ripristino con azioni manuali personalizzate, in modo che si possano effettuare attività diverse dal failover delle macchine virtuali.

Altre informazioni sulla [creazione di un piano di ripristino](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Come si esegue la sequenziazione in un piano di ripristino?

In un piano di ripristino è possibile creare più gruppi per ottenere la sequenziazione. Ogni gruppo esegue il failover contemporaneamente. Le macchine virtuali che fanno parte dello stesso gruppo eseguono il failover nello stesso momento, seguite da un altro gruppo. Per informazioni su come modellare un'applicazione usando un piano di ripristino, vedere [Informazioni sui piani di ripristino](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Come trovo l'RTO di un piano di ripristino?

Per controllare l'RTO di un piano di ripristino, eseguire un failover di test del piano di ripristino e passare a **Processi di Site Recovery**.
Nell'esempio seguente, esaminare il processo **SAPTestRecoveryPlan**. Il processo ha impiegato 8 minuti e 59 secondi per eseguire il failover di tutte le macchine virtuali ed eseguire le azioni specificate.

![Elenco dei processi di Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>È possibile aggiungere runbook di Automazione al piano di ripristino?

Sì, è possibile integrare i runbook di Automazione di Azure nel piano di ripristino. Altre informazioni sull'[aggiunta di runbook di Automazione di Azure](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Riprotezione e failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>È stato eseguito il failover dall'area primaria a un'area di ripristino di emergenza. Le macchine virtuali in un'area di ripristino di emergenza sono protette automaticamente?

No. Quando si [esegue il failover](./azure-to-azure-tutorial-failover-failback.md) di macchine virtuali di Azure da un'area a un'altra, le macchine virtuali si avviano nell'area di ripristino di emergenza in uno stato non protetto. Per eseguire il failback delle macchine virtuali nell'area primaria, è necessario [riproteggere](./azure-to-azure-how-to-reprotect.md) le macchine virtuali nell'area secondaria.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Al momento della riprotezione, Site Recovery esegue una replica completa dei dati dall'area secondaria all'area primaria?

Dipende dalla situazione. Se la macchina virtuale dell'area di origine esiste, verranno sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. Site Recovery calcola i backup differenziali, confrontando i dischi e quindi trasferisce i dati. Questo processo richiede in genere alcune ore. Per altre informazioni su cosa accade durante la riprotezione, vedere [Riproteggere istanze di macchine virtuali di Azure sottoposte a failover nell'area primaria](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo richiede l'esecuzione di un failback?

Dopo la riprotezione, il failback richiede circa la stessa quantità di tempo necessaria per eseguire il failover dall'area primaria a un'area secondaria.

## <a name="capacity"></a><a name="capacity"></a>Capacità

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le VM di Azure?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funzionano con le istanze riservate?

Sì, è possibile acquistare [macchine virtuali di Azure riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) nell'area di ripristino di emergenza e le operazioni di failover di Site Recovery le useranno. Non è necessaria alcuna configurazione aggiuntiva.

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
