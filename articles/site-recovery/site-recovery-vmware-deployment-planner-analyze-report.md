---
title: Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure | Microsoft Docs
description: Questo articolo illustra l'analisi del report generato da Azure Site Recovery Deployment Planner per uno scenario da VMware ad Azure.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/04/2017
ms.author: nisoneji
ms.openlocfilehash: d8c4f5431d8e2d406cd5b203b468c447d4dd6e17
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="azure-site-recovery-deployment-planner-report"></a>Report di Azure Site Recovery Deployment Planner
Il report di Microsoft Excel generato contiene i fogli riportati di seguito.
## <a name="on-premises-summary"></a>Riepilogo ambiente locale
Il foglio di lavoro On-premises summary (Riepilogo ambiente locale) offre una panoramica dell'ambiente VMware profilato.

![Riepilogo dell'ambiente VMware locale](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Start Date** (Data di inizio) ed **End Date** (Data di fine): date di inizio e fine dei dati di profilatura considerati per la generazione di report. Per impostazione predefinita, la data di inizio è la data in cui inizia la profilatura e la data di fine è la data in cui termina. Può trattarsi dei valori di 'StartDate' ed 'EndDate' se il report viene generato con questi parametri.

**Total number of profiling days** (Numero totale di giorni di profilatura): numero totale di giorni di profilatura tra le date di inizio e fine per i quali viene generato il report.

**Number of compatible virtual machines** (Numero di macchine virtuali compatibili): numero totale di VM compatibili per le quali vengono calcolati la larghezza di banda di rete e il numero necessario di account di archiviazione, core di Microsoft Azure, server di configurazione e server di elaborazione aggiuntivi.

**Total number of disks across all compatible virtual machines** (Numero totale di dischi tra tutte le macchine virtuali compatibili): numero usato come uno degli input per stabilire il numero di server di configurazione e di server di elaborazione aggiuntivi da usare nella distribuzione.

**Average number of disks per compatible virtual machine** (Numero medio di dischi per macchina virtuale compatibile): numero medio di dischi calcolato tra tutte le VM compatibili.

**Average disk size (GB)** (Dimensioni medie disco - GB): dimensioni medie dei dischi calcolate tra tutte le VM compatibili.

**Desired RPO (minutes)** (RPO desiderato - minuti): obiettivo del punto di ripristino predefinito o valore passato per il parametro "DesiredRPO" al momento della generazione di report per stimare la larghezza di banda necessaria.

**Desired bandwidth (Mbps)** (Larghezza di banda desiderata - Mbps): valore passato per il parametro "Bandwidth" al momento della generazione di report per stimare il valore RPO ottenibile.

**Observed typical data churn per day (GB)** (Varianza dei dati giornaliera tipica osservata - GB): varianza media dei dati osservata in tutti i giorni di profilatura. Questo numero viene usato come uno degli input per stabilire il numero di server di configurazione e di server di elaborazione aggiuntivi da usare nella distribuzione.

## <a name="recommendations"></a>Raccomandazioni

Il foglio Recommendations (Raccomandazioni) del report per lo scenario da VMware ad Azure contiene i dettagli seguenti, in base al valore RPO desiderato che è stato selezionato:

![Raccomandazioni del report per lo scenario da VMware ad Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dati profilati
![Visualizzazione dei dati profilati in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Profiled data period**(Periodo profilatura dati): periodo durante il quale la profilatura è stata in esecuzione. Per impostazione predefinita, lo strumento include tutti i dati profilati nel calcolo, a meno che non generi il report per un periodo specifico usando le opzioni StartDate ed EndDate durante la generazione del report.

**Server Name** (Nome server): nome o indirizzo IP del server VMware vCenter o dell'host ESXi contenente le VM per le quali viene generato il report.

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino per la distribuzione. Per impostazione predefinita, la larghezza di banda di rete viene calcolata per i valori RPO di 15, 30 e 60 minuti. A seconda della selezione, i valori interessati verranno aggiornati nel foglio. Se è stato usato il parametro *DesiredRPOinMin* durante la generazione del report, tale valore verrà visualizzato nel risultato di Desired RPO (RPO desiderato).

### <a name="profiling-overview"></a>Panoramica della profilatura

![Risultati della profilatura in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total Profiled Virtual Machines** (Totale macchine virtuali profilate): numero totale di VM per le quali sono disponibili dati profilati. Se il parametro VMListFile presenta nomi di VM che non sono state profilate, tali VM non verranno considerate nella generazione di report e verranno escluse dal conteggio totale delle VM profilate.

**Compatible Virtual Machines** (Macchine virtuali compatibili): VM che possono essere protette in Azure con Site Recovery. Si tratta del numero totale di VM compatibili per le quali vengono calcolati larghezza di banda di rete, numero di account di archiviazione, numero di core di Azure, numero di server di configurazione e di server di elaborazione aggiuntivi. I dettagli di ogni VM compatibile sono disponibili nella sezione "VM compatibili".

**Incompatible Virtual Machines** (Macchine virtuali incompatibili): VM profilate incompatibili con la protezione con Site Recovery. I motivi dell'incompatibilità sono indicati nella sezione "Macchine virtuali incompatibili". Se VMListFile contiene nomi di VM non profilate, tali VM vengono escluse dal conteggio delle VM incompatibili. Queste VM sono elencate con la dicitura "Data not found" (Dati non trovati) alla fine della sezione Incompatible VMs (VM incompatibili).

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino desiderato, in minuti. Il report viene generato per tre valori RPO: 15 (impostazione predefinita), 30 e 60 minuti. La raccomandazione relativa alla larghezza di banda nel report varia in funzione della selezione nell'elenco a discesa Desired RPO (RPO desiderato) nella parte superiore destra del foglio. Se il report è stato generato usando il parametro *-DesiredRPO* con un valore personalizzato, questo valore personalizzato verrà visualizzato come predefinito nell'elenco a discesa Desired RPO (RPO desiderato).

### <a name="required-network-bandwidth-mbps"></a>Larghezza di banda di rete necessaria (Mbps)

![Larghezza di banda di rete necessaria in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**To meet RPO 100% of the time** (Per soddisfare il valore RPO il 100% del tempo): larghezza di banda consigliata in Mbps per soddisfare il valore RPO desiderato il 100% del tempo. Questa larghezza di banda deve essere dedicata alla replica differenziale stazionaria di tutte le VM compatibili per evitare violazioni di RPO.

**To meet RPO 90% of the time** (Per soddisfare il valore RPO il 90% del tempo): se a causa dei prezzi della banda larga o per altri motivi non è possibile impostare la larghezza di banda necessaria per soddisfare il valore RPO desiderato il 100% del tempo, è possibile scegliere di passare a una larghezza di banda inferiore che possa soddisfare il valore RPO desiderato il 90% del tempo. Per comprendere le implicazioni dell'impostazione di questa larghezza di banda inferiore, il report offre un'analisi ipotetica del numero e della durata delle violazioni del valore RPO prevedibili.

**Achieved Throughput** (Velocità effettiva ottenuta): velocità effettiva dal server in cui è stato eseguito il comando GetThroughput all'area di Microsoft Azure in cui si trova l'account di archiviazione. Questo numero della velocità effettiva indica il livello stimato ottenibile quando si proteggono le VM compatibili con Site Recovery, se le caratteristiche di archiviazione e di rete del server di configurazione o del server di elaborazione rimangono uguali a quelle del server dal quale è stato eseguito lo strumento.

Per la replica, è opportuno impostare la larghezza di banda consigliata per soddisfare il valore RPO il 100% del tempo. Dopo avere impostato la larghezza di banda, se non è visibile un aumento della velocità effettiva ottenuta, come indicato dallo strumento, seguire questa procedura:

1. Verificare se sono presenti requisiti di qualità del servizio (QoS) che limitano la velocità effettiva di Site Recovery.

2. Verificare se l'insieme di credenziali di Site Recovery si trova nell'area fisica di Microsoft Azure più vicina supportata per ridurre al minimo la latenza di rete.

3. Controllare le caratteristiche di archiviazione locale per determinare se sia possibile migliorare l'hardware (ad esempio, passando da unità HDD a unità SSD).

4. Modificare le impostazioni di Site Recovery nel server di elaborazione per [aumentare la larghezza di banda di rete usata per la replica](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se si esegue lo strumento in un server di configurazione o in un server di elaborazione che ha già VM protette, eseguire lo strumento più volte. Il numero della velocità effettiva ottenuta cambia in base alla varianza elaborata in quel particolare momento.

Per tutte le distribuzioni aziendali di Site Recovery è consigliabile usare [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Account di archiviazione necessari
Il grafico seguente indica il numero totale di account di archiviazione (Standard e Premium) necessari per proteggere tutte le VM compatibili. Per informazioni sull'account di archiviazione da usare per ogni VM, vedere la sezione "Selezione host di archiviazione delle VM".

![Account di archiviazione necessari in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Numero di core Azure necessari
Questo risultato è il numero totale di core da configurare prima del failover o del failover di test di tutte le VM compatibili. Se i core disponibili nella sottoscrizione sono troppo pochi, Site Recovery non creerà le VM al momento del failover di test o del failover.

![Numero di core Azure necessari in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Infrastruttura locale necessaria
Questa cifra indica il numero totale di server di configurazione e di altri server di elaborazione da configurare in quantità sufficiente per proteggere tutte le VM compatibili. A seconda delle [dimensioni consigliate per il server di configurazione](https://aka.ms/asr-v2a-on-prem-components) supportate, lo strumento potrebbe consigliare altri server. L'indicazione si basa sul valore più grande tra quello della varianza giornaliera o del numero massimo di VM protette (presupponendo una media di tre dischi per VM), a seconda del valore raggiunto per primo nel server di configurazione o nel server di elaborazione aggiuntivo. I dettagli della varianza totale giornaliera e del numero totale dei dischi protetti sono disponibili nella sezione "On-premises summary" (Riepilogo ambiente locale).

![Infrastruttura locale necessaria in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Analisi di simulazione
Questa analisi indica quante violazioni possono verificarsi durante il periodo di profilatura quando si imposta una larghezza di banda inferiore per soddisfare il valore RPO desiderato solo il 90% del tempo. Possono verificarsi una o più violazioni del valore RPO in un giorno. Il grafico illustra il valore RPO di picco del giorno.
In base a questa analisi è possibile stabilire se il numero di violazioni del valore RPO per il totale dei giorni e il valore RPO di picco raggiunto ogni giorno siano accettabili con la larghezza di banda inferiore specificata. Se sono accettabili, è possibile allocare la larghezza di banda inferiore per la replica, altrimenti allocare la larghezza di banda superiore come suggerito per soddisfare il valore RPO desiderato il 100% del tempo.

![Analisi di simulazione in Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Dimensioni raccomandate per i batch di VM per la replica iniziale
In questa sezione viene consigliato il numero di VM che possono essere protette in parallelo per completare la replica iniziale entro 72 ore con la larghezza di banda suggerita per soddisfare il valore RPO desiderato il 100% del tempo impostato. Questo valore è configurabile. Per modificarlo durante la generazione di report, usare il parametro *GoalToCompleteIR*.

Il grafico mostra un intervallo di valori di larghezza di banda e un numero calcolato di VM per batch per completare la replica iniziale in 72 ore, in base alle dimensioni medie rilevate per tutte le VM compatibili.

Nell'anteprima pubblica il report non specifica quali VM includere in un batch. È possibile usare lo spazio su disco visualizzato nella sezione "Compatible VM" (VM compatibili) per determinare le dimensioni di ogni VM e selezionarle per un batch oppure è possibile selezionare le VM in base alle caratteristiche note del carico di lavoro. Il tempo di completamento della replica iniziale varia in modo proporzionale allo spazio su disco effettivo della VM, allo spazio usato e alla velocità effettiva della rete disponibile.

![Dimensioni raccomandate per i batch di VM](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Stima dei costi
Il grafico offre la visualizzazione di riepilogo del costo stimato totale del ripristino di emergenza in Azure per l'area di destinazione scelta e la valuta specificata per la generazione del report.

![Riepilogo della stima dei costi](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Il riepilogo consente di conoscere il costo che è necessario sostenere per archiviazione, calcolo, rete e licenza in caso di protezione di tutte le VM compatibili in Azure con Azure Site Recovery. Il costo viene calcolato sulle VM compatibili e non su tutte le VM profilate.  
 
È possibile visualizzare i costi su base mensile o annuale. Vedere altre informazioni sulle [aree di destinazione supportate](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e sulle [valute supportate](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components** (Costo per componente): il costo totale per il ripristino di emergenza è suddiviso tra quattro componenti, ovvero calcolo, archiviazione, rete e costo della licenza di Azure Site Recovery. Il costo viene calcolato in base all'utilizzo che si verificherà durante la replica e in fase di esercitazione sul ripristino di emergenza per il calcolo, l'archiviazione (Premium e Standard), la connessione ExpressRoute/VPN configurata tra il sito locale e Azure e la licenza di Azure Site Recovery.

**Cost by states** (Costo per stato). Il costo totale del ripristino di emergenza viene classificato in base a due diversi stati: Replication (Replica) e DR-Drill (Esercitazione sul ripristino di emergenza). 

**Costo della replica**: costo che verrà addebitato durante la replica. Copre il costo dell'archiviazione, della rete e della licenza di Azure Site Recovery. 

**Costo dell'esercitazione sul ripristino di emergenza**: costo che verrà addebitato durante i failover di test. Durante il failover di test, Azure Site Recovery attiva macchine virtuali. Il costo dell'esercitazione sul ripristino di emergenza copre il costo di calcolo e di archiviazione delle VM in esecuzione. 

**Azure storage cost per Month/Year** (Costo di archiviazione di Azure al mese/all'anno). Mostra il costo di archiviazione totale che verrà addebitato per l'archiviazione Premium e Standard per la replica e l'esercitazione sul ripristino di emergenza.
È possibile visualizzare un'analisi dettagliata dei costi per VM nel foglio [Cost Estimation](site-recovery-vmware-deployment-planner-cost-estimation.md) (Stima costi).

### <a name="growth-factor-and-percentile-values-used"></a>Fattore di crescita e valori percentili usati
Questa sezione nella parte inferiore del foglio indica il valore percentile usato per tutti i contatori delle prestazioni delle VM profilate (il valore predefinito è il 95° percentile) e il fattore di crescita (il valore predefinito è il 30%) usato in tutti i calcoli.

![Fattore di crescita e valori percentili usati](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Raccomandazioni con la larghezza di banda disponibile come input

![Raccomandazioni con la larghezza di banda disponibile come input](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Si possono verificare situazioni in cui non è possibile impostare una larghezza di banda oltre un dato valore di Mbps per la replica di Site Recovery. Lo strumento consente di immettere la larghezza di banda disponibile (con il parametro -Bandwidth durante la generazione di report) e determinare il valore RPO ottenibile in minuti. Con questo valore RPO ottenibile è possibile decidere se è necessario configurare altra larghezza di banda o se è sufficiente una soluzione di ripristino di emergenza con questo valore RPO.

![Valore RPO ottenibile per 500 Mbps di larghezza di banda](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Selezione host di archiviazione delle VM

![Selezione host di archiviazione delle VM](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Disk Storage Type** (Tipo di archiviazione disco): account di archiviazione Standard o Premium usato per replicare tutte le VM corrispondenti indicate nella colonna **VMs to Place** (VM da posizionare).

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri che può essere usato per la denominazione dell'account di archiviazione. È possibile usare un prefisso personalizzato, ma il suggerimento dello strumento segue la [convenzione di denominazione delle partizioni per gli account di archiviazione](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Nome account suggerito): nome dell'account di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Log Storage Account** (Account di archiviazione log): tutti i log di replica vengono archiviati in un account di archiviazione Standard. Per le VM che eseguono la replica in un account di archiviazione Premium, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione log. Un singolo account di archiviazione log Standard può essere usato da più account di archiviazione di replica Premium. Le VM replicate negli account di archiviazione Standard usano lo stesso account di archiviazione per i log.

**Suggested Log Account Name** (Nome account log suggerito): nome dell'account log di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Placement Summary** (Riepilogo di selezione host): riepilogo del carico totale delle VM nell'account di archiviazione al momento della replica e del failover di test o del failover. Comprende il numero totale di VM mappate all'account di archiviazione, il numero totale di operazioni di I/O al secondo in lettura/scrittura per tutte le VM inserite in questo account di archiviazione, il totale delle operazioni di I/O al secondo in scrittura (replica), le dimensioni totali configurate per tutti i dischi e il numero totale di dischi.

**VMs to Place** (Macchine virtuali da inserire): elenco di tutte le VM da inserire nell'account di archiviazione specificato per ottenere uso e prestazioni ottimali.

## <a name="compatible-vms"></a>VM compatibili
![Foglio di calcolo di Excel delle VM compatibili](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM Name** (Nome macchina virtuale): nome o indirizzo IP della VM usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (VMDK) collegati alle VM. Per distinguere le VM vCenter con nomi o indirizzi IP duplicati, i nomi includono il nome dell'host ESXi. L'host ESXi elencato è quello in cui la VM è stata inserita quando lo strumento l'ha rilevata durante il periodo di profilatura.

**VM Compatibility** (Compatibilità VM): i valori sono **Yes** e **Yes**\*. **Yes**\* è per i casi in cui la VM è idonea per l'[Archiviazione Premium di Azure](https://aka.ms/premium-storage-workload). Qui, il disco con varianza elevata o operazioni di I/O al secondo profilato è idoneo per la categoria P20 o P30, ma le dimensioni del disco prevedono la mappatura alla categoria P10 o P20. In base alle dimensioni, l'account di archiviazione decide a quale tipo di disco di archiviazione Premium mappare un disco. Ad esempio: 
* <128 GB rientrano nella categoria P10.
* Da 128 GB a 256 GB rientrano nella categoria P15.
* Da 256 GB a 512 GB rientrano nella categoria P20.
* Da 512 GB a 1024 GB rientrano nella categoria P30.
* Da 1025 GB a 2048 GB rientrano nella categoria P40.
* Da 2049 GB a 4095 GB rientrano nella categoria P50.

Se in virtù delle caratteristiche del carico di lavoro un disco appartiene alla categoria P20 o P30, ma le dimensioni ne determinano l'associazione a un tipo di disco di archiviazione Premium inferiore, ad esempio, lo strumento contrassegna tale VM con **Yes**\* (Sì). Lo strumento consiglia anche di modificare le dimensioni del disco di origine per renderlo idoneo al tipo di disco di archiviazione Premium raccomandato oppure di modificare il tipo di disco di destinazione dopo il failover.

**Storage Type** (Tipo di archiviazione): Standard o Premium.

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri dell'account di archiviazione.

**Storage Account** (Account di archiviazione): nome che usa il prefisso dell'account di archiviazione suggerito.

**R/W IOPS (with Growth Factor)** (Operazioni di I/O al secondo in lettura/scrittura - con fattore di crescita): picco di operazioni di I/O al secondo in lettura/scrittura del carico di lavoro nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura di una VM non è sempre costituito dalla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM, perché il picco di operazioni di I/O al secondo in lettura/scrittura della VM è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Data Churn in Mbps (with Growth Factor)** (Varianza dati in Mbps - con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Azure VM Size** (Dimensioni VM Azure): dimensioni ideali del mapping per Servizi cloud di Azure per questa VM locale. Il mapping si basa sulla memoria, sul numero di dischi/core/schede di interfaccia di rete e operazioni di I/O al secondo in lettura/scrittura della VM locale. La raccomandazione prevede sempre le dimensioni minime della VM di Azure corrispondenti a tutte le caratteristiche della VM locale.

**Number of Disks** (Numero di dischi): numero totale dei dischi nella VM (VMDK).

**Disk size (GB)** (Dimensioni disco - GB): dimensioni di installazione totale di tutti i dischi della VM. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI.  Attualmente Azure Site Recovery supporta VM Windows Server basate su EFI (Windows Server 2012, 2012 R2 e 2016), a condizione che il numero di partizioni nel disco di avvio sia inferiore a 4 e che i settori di avvio abbiano dimensioni di 512 byte. Per proteggere le VM basate su EFI, la versione del servizio Mobility di Azure Site Recovery deve essere la 9.13 o successive. Per le VM basate su EFI è supportato solo il failover. Il failback non è supportato.  

**OS Type** (Tipo di sistema operativo): tipo di sistema operativo della VM. Può essere Windows o Linux o un altro ancora, in base al modello scelto da VMware vSphere durante la creazione della VM.  

## <a name="incompatible-vms"></a>VM incompatibili

![Foglio di calcolo di Excel delle VM incompatibili
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM Name** (Nome macchina virtuale): nome o indirizzo IP della VM usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (VMDK) collegati alle VM. Per distinguere le VM vCenter con nomi o indirizzi IP duplicati, i nomi includono il nome dell'host ESXi. L'host ESXi elencato è quello in cui la VM è stata inserita quando lo strumento l'ha rilevata durante il periodo di profilatura.

**VM Compatibility** (Compatibilità VM): indica perché la VM è incompatibile per l'uso con Site Recovery. I motivi vengono descritti per ogni disco incompatibile della VM e, in base ai [limiti di archiviazione](https://aka.ms/azure-storage-scalbility-performance) pubblicati, possono essere uno dei seguenti:

* Dimensioni disco superiori a 4095 GB. Archiviazione di Azure attualmente non supporta dischi dati di dimensioni superiori a 4095 GB.

* Disco del sistema operativo superiore a 2048 GB. Archiviazione di Azure attualmente non supporta dischi del sistema operativo di dimensioni superiori a 2048 GB.

* Le dimensioni totali della VM (replica + failover di test) superano i limiti supportati dall'account di archiviazione (35 TB). Questa incompatibilità si verifica in genere quando un singolo disco della VM ha una caratteristica di prestazioni che supera i limiti massimi supportati da Azure o da Site Recovery per l'archiviazione Standard. In questo caso la VM rientra nell'area dell'archiviazione Premium. Le dimensioni massime supportate da un account di archiviazione Premium sono tuttavia pari a 35 TB e non è possibile proteggere una singola VM su più account di archiviazione. Si noti anche che, quando un failover di test viene eseguito in una VM protetta, viene eseguito nello stesso account di archiviazione in cui è in corso la replica. In questo caso, configurare il doppio delle dimensioni del disco per far sì che la replica prosegua e il failover di test venga completato in parallelo.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 7500 operazioni per disco.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 80.000 operazioni per ogni VM.

* La varianza media dei dati supera il limite supportato da Site Recovery di 10 MB/s per le dimensioni I/O medie del disco.

* La varianza media dei dati supera il limite supportato da Site Recovery di 25 MB/s per le dimensioni di I/O medie della VM (somma della varianza di tutti i dischi).

* Il picco di varianza dei dati per tutti i dischi nella VM supera il limite massimo supportato da Site Recovery di 54 MB/s per VM.

* Le operazioni di I/O al secondo in scrittura superano il limite supportato da Site Recovery di 840 operazioni per disco.

* L'archiviazione snapshot calcolata supera il limite supportato di 10 TB.

* La varianza totale dei dati al giorno supera il limite di 2 TB supportato per ogni giorno da un server di elaborazione.


**R/W IOPS (with Growth Factor)** (Operazioni di I/O al secondo in lettura/scrittura - Con fattore di crescita): picco di operazioni di I/O al secondo per i carichi di lavoro nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura della VM non è sempre costituito dalla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM, perché il picco di operazioni di I/O al secondo in lettura/scrittura della VM è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Peak Data Churn in Mbps (with Growth Factor)** (Picco di varianza dati in Mbps - Con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Number of Disks** (Numero di dischi): numero totale dei dischi VMDK nella VM.

**Disk size (GB)** (Dimensioni disco - GB): dimensioni di installazione totale di tutti i dischi della VM. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): quantità di RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI.  Attualmente Azure Site Recovery supporta VM Windows Server basate su EFI (Windows Server 2012, 2012 R2 e 2016), a condizione che il numero di partizioni nel disco di avvio sia inferiore a 4 e che i settori di avvio abbiano dimensioni di 512 byte. Per proteggere le VM basate su EFI, la versione del servizio Mobility di Azure Site Recovery deve essere la 9.13 o successive. Per le VM basate su EFI è supportato solo il failover. Il failback non è supportato.

**OS Type** (Tipo di sistema operativo): tipo di sistema operativo della VM. Può essere Windows o Linux o un altro ancora, in base al modello scelto da VMware vSphere durante la creazione della VM. 

## <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery
La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti si basano su test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. Per risultati ottimali, anche dopo la pianificazione della distribuzione è sempre consigliabile eseguire test approfonditi delle applicazioni con un failover di test per ottenere il quadro reale delle prestazioni dell'applicazione.

**Destinazione archiviazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |10 MB/s | 842 GB per disco

**Varianza dati di origine** | **Limite massimo**
---|---
Varianza media dei dati per VM| 25 MB/s 
Picco di varianza dei dati su tutti i dischi in una VM | 54 MB/s
Varianza massima dei dati al giorno supportata da un server di elaborazione | 2 TB 

Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%. Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro. I numeri precedenti presuppongono un backlog tipico di circa cinque minuti, ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.


## <a name="cost-estimation"></a>Stima dei costi
Vedere altre informazioni sulla [stima dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md). 


## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulla [stima dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md).
