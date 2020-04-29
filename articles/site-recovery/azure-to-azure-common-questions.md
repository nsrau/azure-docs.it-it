---
title: Domande comuni sul ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Questo articolo risponde a domande comuni sul ripristino di emergenza delle macchine virtuali di Azure quando si usa Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547879"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Domande comuni: ripristino di emergenza da Azure ad Azure

Questo articolo risponde a domande comuni sul ripristino di emergenza di macchine virtuali di Azure in un'altra area di Azure quando si usa [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Generale

### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?

Esaminare [Azure Site Recovery prezzi per le macchine virtuali](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Come funziona il livello gratuito di Azure Site Recovery?

Ogni istanza protetta con Azure Site Recovery è gratuita per i primi 31 giorni di protezione. Dopo tale periodo, la protezione per ogni istanza è alle tariffe [Azure Site Recovery prezzi per le macchine virtuali di Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, sono previsti altri addebiti per Azure?

Sì. Anche se Azure Site Recovery è gratuita durante i primi 31 giorni di un'istanza protetta, è possibile che vengano addebitati costi per archiviazione di Azure, transazioni di archiviazione e trasferimenti di dati. Una macchina virtuale ripristinata può anche comportare addebiti di calcolo di Azure. Per informazioni dettagliate sui prezzi, fare [Azure Site Recovery prezzi](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quali sono le procedure consigliate per il ripristino di emergenza di macchine virtuali di Azure?

1. [Informazioni sull'architettura da Azure ad Azure](azure-to-azure-architecture.md)
1. [Esaminare le configurazioni supportate e non supportate](azure-to-azure-support-matrix.md)
1. [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-how-to-enable-replication.md)
1. [Eseguire un failover di test](azure-to-azure-tutorial-dr-drill.md)
1. [Eseguire il failover e il failback nell'area primaria](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Come viene garantita la capacità nell'area di destinazione?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità di infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

## <a name="replication"></a>Replica

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>È possibile replicare le macchine virtuali abilitate con Crittografia dischi di Azure?

Sì. Site Recovery supporta il ripristino di emergenza di macchine virtuali in cui è abilitata la crittografia dischi di Azure. Quando si Abilita la replica, Azure copia tutte le chiavi e i segreti di crittografia del disco necessari dall'area di origine all'area di destinazione nel contesto utente. Se non si dispone delle autorizzazioni appropriate, l'amministratore della sicurezza può utilizzare uno script per copiare le chiavi e i segreti.

- Site Recovery supporta crittografia dischi di Azure per macchine virtuali di Azure che eseguono Windows.
- Site Recovery supporta crittografia dischi di Azure versione 0,1, che dispone di uno schema che richiede Azure Active Directory (Azure AD). Site Recovery supporta anche la versione 1,1, che non richiede Azure AD. [Altre informazioni sullo schema di estensione per crittografia dischi di Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Per crittografia dischi di Azure versione 1,1, è necessario usare le macchine virtuali Windows con Managed Disks.
  - [Altre](azure-to-azure-how-to-enable-replication-ade-vms.md) informazioni sull'abilitazione della replica per le macchine virtuali crittografate.

### <a name="can-i-replicate-vms-to-another-subscription"></a>È possibile replicare le macchine virtuali in un'altra sottoscrizione?

Sì, è possibile replicare macchine virtuali di Azure in una sottoscrizione diversa nello stesso tenant di Azure AD.

Configurare il ripristino di emergenza [tra le sottoscrizioni](https://azure.microsoft.com/blog/cross-subscription-dr) selezionando un'altra sottoscrizione al momento della replica.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>È possibile replicare in un'altra area le macchine virtuali di Azure aggiunte a una certa zona?

Sì, è possibile [replicare le macchine virtuali aggiunte a una certa zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) in un'altra area.

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?

Sì, è possibile escludere dischi al momento della protezione tramite PowerShell. Per ulteriori informazioni, vedere [come escludere dischi dalla replica](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>È possibile aggiungere nuovi dischi alle macchine virtuali replicate e abilitare la replica?

Sì, l'aggiunta di nuovi dischi alle VM replicate e l'abilitazione della replica per esse sono supportate per le macchine virtuali di Azure con Managed Disks. Quando si aggiunge un nuovo disco a una macchina virtuale di Azure abilitata per la replica, l'integrità della replica per la VM Visualizza un avviso. Questo avviso indica che uno o più dischi nella macchina virtuale sono disponibili per la protezione. È possibile abilitare la replica per i dischi aggiunti.

- Se si Abilita la protezione per i dischi aggiunti, l'avviso scomparirà dopo la replica iniziale.
- Se non si Abilita la replica per il disco, è possibile ignorare l'avviso.
- Se si esegue il failover di una macchina virtuale con un disco aggiunto e la replica abilitata, sono presenti punti di replica. I punti di replica visualizzeranno i dischi disponibili per il ripristino.

Si immagini, ad esempio, che una macchina virtuale disponga di un solo disco e ne venga aggiunto uno nuovo. È possibile che sia stato creato un punto di replica prima di aggiungere il disco. Questo punto di replica indicherà che è costituito da "1 di 2 dischi".

Site Recovery non supporta la "rimozione a caldo" di un disco da una macchina virtuale replicata. Se si rimuove un disco della macchina virtuale, è necessario disabilitare e quindi riabilitare la replica per la macchina virtuale.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali di Azure in un'altra area di Azure è continua. Per altre informazioni, vedere [Architettura di replica da Azure ad Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>È possibile eseguire la replica delle macchine virtuali all'interno di un'area? Questa funzionalità è necessaria per eseguire la migrazione delle macchine virtuali.

Non è possibile usare una soluzione di ripristino del disco da Azure ad Azure per replicare le macchine virtuali all'interno di un'area.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>È possibile replicare le istanze di VM in qualsiasi area di Azure?

Con Site Recovery è possibile replicare e ripristinare macchine virtuali tra due aree diverse nello stesso cluster geografico. I cluster geografici vengono definiti con la latenza e la sovranità dei dati. Per altre informazioni, vedere la [matrice di supporto dell'area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) di Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery richiede la connettività Internet?

No, Site Recovery non richiede la connettività Internet. Ma richiede l'accesso a Site Recovery gli URL e gli intervalli IP, come indicato in [rete nel ripristino di emergenza della macchina virtuale di Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>È possibile replicare un'applicazione che dispone di un gruppo di risorse separato per i livelli distinti?

Sì, è possibile replicare l'applicazione e mantenete la configurazione del ripristino di emergenza in un gruppo di risorse separato.

Se, ad esempio, l'applicazione include l'applicazione, il database e il Web di ogni livello in un gruppo di risorse distinto, è necessario selezionare la [procedura guidata di replica](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) tre volte per proteggere tutti i livelli. Site Recovery eseguirà la replica di questi tre livelli in tre gruppi di risorse diversi.

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?

I criteri di replica definiscono le impostazioni per la cronologia di conservazione dei punti di ripristino. Il criterio definisce anche la frequenza degli snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con le impostazioni predefinite seguenti:

- 24 ore per la cronologia della conservazione dei punti di recupero.
- 60 minuti per la frequenza degli snapshot coerenti con l'app.

[Altre informazioni sulle impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Che cos'è il punto di recupero coerente con l'arresto anomalo del sistema?

Un punto di ripristino coerente con l'arresto anomalo del sistema ha i dati su disco come se fosse stato eseguito il pull del cavo di alimentazione dal server durante lo snapshot. Il punto di ripristino coerente con l'arresto anomalo del sistema non include alcun elemento in memoria quando è stato effettuato lo snapshot.

La maggior parte delle applicazioni esegue ora correttamente il ripristino da snapshot coerenti con l'arresto anomalo del sistema. Un punto di recupero coerente con l'arresto anomalo del sistema è in genere sufficiente per sistemi operativi senza database e per applicazioni quali file server, server DHCP e server di stampa.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual è la frequenza di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti.

### <a name="what-is-an-application-consistent-recovery-point"></a>Che cos'è un punto di recupero coerente con l'applicazione?

I punti di recupero coerenti con l'applicazione vengono creati dagli snapshot coerenti con l'applicazione. I punti di ripristino coerenti con l'applicazione acquisiscono gli stessi dati degli snapshot coerenti con l'arresto anomalo del sistema, acquisendo inoltre i dati in memoria e tutte le transazioni in corso.

A causa del contenuto aggiuntivo, gli snapshot coerenti con l'applicazione sono i più interessati e hanno più tempo. È consigliabile usare i punti di recupero coerenti con l'applicazione per sistemi operativi e applicazioni di database come SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual è l'impatto dei punti di recupero coerenti con l'applicazione sulle prestazioni dell'applicazione?

I punti di ripristino coerenti con l'applicazione acquisiscono tutti i dati in memoria e in corso. Poiché i punti di ripristino acquisiscono tali dati, richiedono Framework come Servizio Copia Shadow del volume in Windows per mettere in stato l'applicazione. Se il processo di acquisizione è frequente, può influire sulle prestazioni quando il carico di lavoro è già occupato. Non è consigliabile usare bassa frequenza per i punti di ripristino coerenti con l'app per i carichi di lavoro non di database. Anche per il carico di lavoro del database, 1 ora è sufficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual è la frequenza minima di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery possibile creare un punto di ripristino coerente con l'applicazione con una frequenza minima di 1 ora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?

Per comprendere il modo in cui Site Recovery genera i punti di ripristino, è possibile vedere un esempio di criteri di replica. Questo criterio di replica dispone di un punto di ripristino con un intervallo di conservazione di 24 ore e uno snapshot di frequenza coerente con l'app di 1 ora.

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti. Non è possibile modificare questa frequenza. Nell'ultima ora è possibile scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e 1 punto coerente con l'app. Con l'avanzare del tempo, Site Recovery elimina tutti i punti di ripristino oltre l'ultima ora e salva un solo punto di ripristino all'ora.

Lo screenshot seguente illustra l'esempio. Nello screenshot:

- Nell'ultima ora sono presenti punti di ripristino con una frequenza di 5 minuti.
- Oltre l'ora precedente, Site Recovery mantiene solo 1 punto di ripristino.

   ![Elenco dei punti di recupero generati](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Il punto di recupero meno recente che è possibile usare è di 72 ore.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ho un criterio di replica di 24 ore. Cosa accade se un problema impedisce a Site Recovery di generare punti di ripristino per più di 24 ore? I precedenti punti di recupero verranno persi?

No, Site Recovery manterrà tutti i punti di recupero precedenti. A seconda dell'intervallo di conservazione dei punti di ripristino, Site Recovery sostituisce il punto meno recente solo se genera nuovi punti. A causa del problema, Site Recovery non è in grado di generare nuovi punti di ripristino. Fino a quando non saranno presenti nuovi punti di ripristino, tutti i vecchi punti rimarranno dopo aver raggiunto la finestra di conservazione.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Dopo aver abilitato la replica in una macchina virtuale, come si modifica il criterio di replica?

Passare a **Site Recovery** > insieme di credenziali Site Recovery i**criteri di replica**dell'**infrastruttura** > . Selezionare il criterio che si desidera modificare e salvare le modifiche. Qualsiasi modifica verrà applicata anche a tutte le repliche esistenti.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tutti i punti di recupero includono una copia completa della macchina virtuale o solo una copia differenziale?

Il primo punto di recupero che viene generato include la copia completa. I punti di recupero successivi includono le modifiche delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>L'aumento del periodo di conservazione dei punti di recupero comporta l'aumento dei costi di archiviazione?

Sì, se si aumenta il periodo di conservazione da 24 ore a 72 ore, Site Recovery salverà i punti di ripristino per altre 48 ore. Il tempo aggiuntivo comporterà dei costi di archiviazione. Ad esempio, un singolo punto di ripristino potrebbe avere variazioni Delta di 10 GB con un costo per GB di $0,16 al mese. Gli addebiti aggiuntivi saranno $1,60 × 48 al mese.

## <a name="multi-vm-consistency"></a>Coerenza tra più macchine virtuali

### <a name="what-is-multi-vm-consistency"></a>Che cos'è la coerenza tra più macchine virtuali?

La coerenza tra più macchine virtuali garantisce che il punto di ripristino sia coerente in tutte le macchine virtuali replicate.

Site Recovery offre un'opzione di coerenza tra più macchine **virtuali** , che consente di creare un gruppo di replica di tutti i computer.

Quando si esegue il failover delle macchine virtuali, questi avranno i punti di ripristino coerenti con l'arresto anomalo e coerenti con l'app.

Seguire l'esercitazione per abilitare la coerenza tra più [macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>È possibile eseguire il failover di una singola macchina virtuale all'interno di un gruppo di replica di coerenza tra più VM?

Quando si seleziona l'opzione di coerenza tra più **macchine** virtuali, si indica che l'applicazione dipende da tutte le macchine virtuali all'interno di un gruppo. Il failover di una singola macchina virtuale non è consentito.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quante macchine virtuali è possibile replicare come parte di un gruppo di replica di coerenza tra più VM?

È possibile replicare 16 macchine virtuali all'interno di uno stesso gruppo di replica.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando è consigliabile abilitare la coerenza tra più macchine virtuali?

Poiché la coerenza tra più macchine virtuali è a elevato utilizzo di CPU, l'abilitazione può influire sulle prestazioni del carico Usare la coerenza tra più macchine virtuali solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più computer. Se, ad esempio, si dispone di due istanze di SQL Server e di due server Web in un'applicazione, è necessario disporre di coerenza tra più macchine virtuali solo per le istanze di SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>È possibile aggiungere una macchina virtuale già replicata a un gruppo di replica?

È possibile aggiungere una macchina virtuale a un nuovo gruppo di replica abilitando la replica. È anche possibile aggiungere una macchina virtuale a un gruppo di replica esistente abilitando la replica. Tuttavia, non è possibile aggiungere una macchina virtuale già replicata a un nuovo gruppo di replica o a un gruppo di replica esistente.

## <a name="failover"></a>Failover

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le macchine virtuali di Azure?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità di infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il failover non è automatico. È possibile avviare i failover con un singolo clic nel portale oppure è possibile usare [PowerShell](azure-to-azure-powershell.md) per attivare un failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>È possibile gestire un indirizzo IP pubblico dopo un failover?

Non è possibile gestire l'indirizzo IP pubblico dell'applicazione di produzione dopo un failover.

Quando si apre un carico di lavoro come parte del processo di failover, è necessario assegnare una risorsa IP pubblico di Azure al carico di lavoro. La risorsa IP pubblico di Azure deve essere disponibile nell'area di destinazione. È possibile assegnare manualmente la risorsa IP pubblico di Azure oppure è possibile automatizzarla con un piano di ripristino. Informazioni su come [configurare gli indirizzi IP pubblici dopo il failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>È possibile gestire un indirizzo IP privato durante un failover?

Sì, è possibile usare un indirizzo IP privato. Per impostazione predefinita, quando si abilita il ripristino di emergenza per macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni di quelle di origine. Per le macchine virtuali di Azure configurate con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per la VM di destinazione, se non è in uso.
Informazioni su come [mantenere gli indirizzi IP durante il failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dopo un failover, perché il server ha assegnato un nuovo indirizzo IP?

Site Recovery tenta di fornire l'indirizzo IP al momento del failover. Se un'altra macchina virtuale richiede tale indirizzo, Site Recovery imposta il successivo indirizzo IP disponibile come destinazione.

Altre informazioni sulla [configurazione del mapping di rete e degli indirizzi IP per le reti virtuali](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Che cosa sono i punti di recupero **più recenti (valore RPO più basso)**?

L'opzione **più recente (RPO più bassa)** elabora innanzitutto tutti i dati inviati al Site Recovery. Dopo che il servizio ha elaborato i dati, viene creato un punto di ripristino per ogni macchina virtuale prima del failover alla macchina virtuale. Questa opzione fornisce l'obiettivo del punto di ripristino (RPO) più basso. Per la macchina virtuale creata dopo il failover sono stati replicati tutti i dati Site Recovery da quando è stato attivato il failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>I punti di recupero **più recenti (RPO più basso)** hanno un impatto sul failover RTO (Recovery Time Objective)?

Sì. Site Recovery elabora tutti i dati in sospeso prima del failover, pertanto questa opzione ha un obiettivo del tempo di ripristino (RTO) superiore rispetto ad altre opzioni.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Che cosa significa l'opzione **Elaborato più recente** nei punti di recupero?

L'opzione **elaborata più recente** esegue il failover di tutte le macchine virtuali nel piano al punto di ripristino più recente che Site Recovery elaborato. Per visualizzare il punto di ripristino più recente per una macchina virtuale specifica, controllare i **punti di ripristino più recenti** nelle impostazioni della macchina virtuale. Questa opzione offre un RTO basso poiché non viene impiegato tempo per la gestione di dati non elaborati.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Cosa accade se si verifica un'interruzione imprevista dell'area primaria?

Dopo l'interruzione è possibile attivare un failover. Per eseguire il failover Site Recovery non è necessaria la connettività dall'area primaria.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Che cos'è un RTO di failover di una macchina virtuale?

Site Recovery dispone [di un contratto di RTO di 2 ore](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Tuttavia, nella maggior parte dei casi, Site Recovery esegue il failover delle macchine virtuali in pochi minuti. È possibile calcolare il RTO passando ai processi di failover, che indicano il tempo richiesto per visualizzare la VM. Per RTO del piano di ripristino, fare riferimento alla sezione successiva.

## <a name="recovery-plans"></a>Piani di ripristino

### <a name="what-is-a-recovery-plan"></a>Che cos'è un piano di ripristino?

I piani di ripristino in Site Recovery gestiscono il ripristino del failover delle macchine virtuali. Consentono anche di ottenere un ripristino costantemente accurato, ripetibile e automatizzato. Un piano di ripristino soddisfa le seguenti esigenze:

- Definizione di un gruppo di macchine virtuali che eseguono il failover contemporaneamente.
- Definizione di dipendenze tra macchine virtuali, affinché l'applicazione venga visualizzata in modo accurato.
- Automatizzazione del ripristino con azioni manuali personalizzate, in modo che si possano effettuare attività diverse dal failover delle macchine virtuali.

Altre informazioni [sulla creazione di piani di ripristino](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Come si esegue la sequenziazione in un piano di ripristino?

In un piano di ripristino è possibile creare più gruppi per ottenere la sequenziazione. Ogni gruppo esegue il failover contemporaneamente. Le macchine virtuali che fanno parte dello stesso gruppo eseguono il failover insieme, seguite da un altro gruppo. Per informazioni su come modellare un'applicazione usando un piano di ripristino, vedere [Informazioni sui piani di ripristino](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Come trovo l'RTO di un piano di ripristino?

Per controllare l'RTO di un piano di ripristino, eseguire un failover di test del piano di ripristino e passare a **Processi di Site Recovery**.
Nell'esempio seguente, vedere il processo **SAPTestRecoveryPlan**. Il processo ha impiegato 8 minuti e 59 secondi per eseguire il failover di tutte le macchine virtuali ed eseguire le azioni specificate.

![Elenco dei processi di Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>È possibile aggiungere runbook di Automazione al piano di ripristino?

Sì, è possibile integrare i runbook di Automazione di Azure nel piano di ripristino. Altre informazioni sull' [aggiunta di manuali operativi di automazione di Azure](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Riprotezione e failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>È stato eseguito il failover dall'area primaria a un'area di ripristino di emergenza. Le macchine virtuali in un'area di ripristino di emergenza sono protette automaticamente?

No. Quando si [esegue il failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) di macchine virtuali di Azure da un'area a un'altra, le macchine virtuali si avviano nell'area di ripristino di emergenza in uno stato non protetto. Per eseguire il failback delle macchine virtuali nell'area primaria, è necessario [riproteggere](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) le macchine virtuali nell'area secondaria.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Al momento della riprotezione, Site Recovery esegue una replica completa dei dati dall'area secondaria all'area primaria?

Dipende dalla situazione. Se la macchina virtuale dell'area di origine esiste, verranno sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. Site Recovery calcola i backup differenziali, confrontando i dischi e quindi trasferisce i dati. Questo processo richiede in genere alcune ore. Per altre informazioni su ciò che accade durante la riprotezione, vedere [riproteggi le istanze di VM di Azure di cui è stato eseguito il failover nell'area primaria](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo richiede l'esecuzione di un failback?

Dopo la riprotezione, il failback richiede circa la stessa quantità di tempo necessaria per eseguire il failover dall'area primaria a un'area secondaria.

## <a name="capacity"></a><a name="capacity"></a>Capacity

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Come viene garantita la capacità nell'area di destinazione per le macchine virtuali di Azure?

Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità di infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funzionano con le istanze riservate?

Sì, è possibile acquistare [macchine virtuali di Azure riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) nell'area di ripristino di emergenza e Site Recovery le operazioni di failover le useranno. Non è necessaria alcuna configurazione aggiuntiva.

## <a name="security"></a>Sicurezza

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?

No, Site Recovery non intercetta i dati replicati e non contiene informazioni su ciò che è in esecuzione nelle macchine virtuali. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA. Il servizio è in fase di valutazione di SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?

Sì, sono supportate sia la crittografia in transito che [la crittografia inattiva in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare i requisiti di supporto da Azure ad Azure](azure-to-azure-support-matrix.md).
- [Configurare la replica da Azure ad Azure](azure-to-azure-tutorial-enable-replication.md).
- Per eventuali domande dopo aver letto questo articolo, pubblicarle nel [Forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
