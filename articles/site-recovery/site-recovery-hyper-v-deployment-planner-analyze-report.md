---
title: Azure Site Recovery Deployment Planner per distribuzioni da Hyper-V ad Azure | Microsoft Docs
description: Questo articolo illustra l'analisi del report generato da Azure Site Recovery Deployment Planner per uno scenario da Hyper-V ad Azure.
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
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 714c2074f643d2b168c054c5af467b550f57daba
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analizzare il report di Azure Site Recovery Deployment Planner
Il report di Microsoft Excel generato contiene i fogli seguenti:

## <a name="on-premises-summary"></a>Riepilogo ambiente locale
Il foglio di lavoro On-premises summary (Riepilogo ambiente locale) offre una panoramica dell'ambiente Hyper-V profilato ![Riepilogo ambiente locale](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Start Date** (Data di inizio) ed **End Date** (Data di fine): date di inizio e fine dei dati di profilatura considerati per la generazione di report. Per impostazione predefinita, la data di inizio è la data in cui inizia la profilatura e la data di fine è la data in cui termina. Può trattarsi dei valori di 'StartDate' ed 'EndDate' se il report viene generato con questi parametri.

**Total number of profiling days** (Numero totale di giorni di profilatura): numero totale di giorni di profilatura tra le date di inizio e fine per i quali viene generato il report.

**Number of compatible virtual machines** (Numero di macchine virtuali compatibili): numero totale di macchine virtuali compatibili per le quali vengono calcolati la larghezza di banda di rete necessaria, il numero necessario di account di archiviazione e i core di Microsoft Azure.

**Total number of disks across all compatible virtual machines** (Numero totale di dischi tra tutte le macchine virtuali compatibili): numero totale di dischi tra tutte le macchine virtuali compatibili.

**Average number of disks per compatible virtual machine** (Numero medio di dischi per macchina virtuale compatibile): numero medio di dischi calcolato tra tutte le VM compatibili.

**Average disk size (GB)** (Dimensioni medie disco - GB): dimensioni medie dei dischi calcolate tra tutte le VM compatibili.

**Desired RPO (minutes)** (RPO desiderato - minuti): obiettivo del punto di ripristino predefinito o valore passato per il parametro "DesiredRPO" al momento della generazione di report per stimare la larghezza di banda necessaria.

**Desired bandwidth (Mbps)** (Larghezza di banda desiderata - Mbps): valore passato per il parametro "Bandwidth" al momento della generazione di report per stimare il valore RPO ottenibile.

**Observed typical data churn per day (GB)** (Varianza dei dati giornaliera tipica osservata - GB): varianza media dei dati osservata in tutti i giorni di profilatura.

## <a name="recommendations"></a>Raccomandazioni  
Il foglio Recommendations (Raccomandazioni) del report per lo scenario da Hyper-V ad Azure contiene i dettagli seguenti, in base al valore RPO desiderato selezionato:

![Raccomandazioni del report per lo scenario da Hyper-V ad Azure](media/site-recovery-hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Dati del profilo
![Dati del profilo](media/site-recovery-hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Profiled data period**(Periodo profilatura dati): periodo durante il quale la profilatura è stata in esecuzione. Per impostazione predefinita, lo strumento include nel calcolo tutti i dati profilati. Se nella generazione del report sono state usate le opzioni StartDate ed EndDate, viene generato il report per il periodo specifico. 

**Number of Hyper-V servers profiled** (Numero di server Hyper-V profilati): numero di server Hyper-V per cui viene generato il report delle macchine virtuali. Fare clic sul numero per visualizzare il nome dei server Hyper-V. Verrà visualizzato il foglio On-premises Storage Requirement (Requisito di archiviazione locale) in cui sono elencati tutti i server con il relativo requisito di archiviazione.    

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino per la distribuzione. Per impostazione predefinita, la larghezza di banda di rete viene calcolata per i valori RPO di 15, 30 e 60 minuti. A seconda della selezione, i valori interessati verranno aggiornati nel foglio. Se durante la generazione del report è stato usato il parametro DesiredRPOinMin, tale valore verrà visualizzato nel risultato di Desired RPO (RPO desiderato).

### <a name="profiling-overview"></a>Panoramica della profilatura
![Panoramica della profilatura](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total Profiled Virtual Machines** (Totale macchine virtuali profilate): numero totale di VM per le quali sono disponibili dati profilati. Se il parametro VMListFile presenta nomi di macchine virtuali che non sono state profilate, tali macchine virtuali non verranno considerate nella generazione del report e verranno escluse dal conteggio totale delle macchine virtuali profilate.

**Compatible Virtual Machines** (Macchine virtuali compatibili): numero di macchine virtuali che possono essere protette in Azure con Azure Site Recovery. Si tratta del numero totale di macchine virtuali compatibili per le quali vengono calcolati la larghezza di banda di rete necessaria, il numero di account di archiviazione e il numero di core di Azure. I dettagli di ogni VM compatibile sono disponibili nella sezione "VM compatibili".

**Incompatible Virtual Machines** (Macchine virtuali incompatibili): numero di macchine virtuali profilate incompatibili con la protezione tramite Azure Site Recovery. I motivi dell'incompatibilità sono indicati nella sezione "Macchine virtuali incompatibili". Se VMListFile contiene nomi di VM non profilate, tali VM vengono escluse dal conteggio delle VM incompatibili. Queste VM sono elencate con la dicitura "Data not found" (Dati non trovati) alla fine della sezione Incompatible VMs (VM incompatibili).

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino desiderato, in minuti. Il report viene generato per tre valori RPO: 15 (impostazione predefinita), 30 e 60 minuti. La raccomandazione relativa alla larghezza di banda nel report varia in funzione del valore selezionato nell'elenco a discesa Desired RPO (RPO desiderato) nella parte superiore destra del foglio. Se il report è stato generato usando il parametro -DesiredRPO con un valore personalizzato, questo valore personalizzato viene visualizzato come predefinito nell'elenco a discesa Desired RPO (RPO desiderato).

### <a name="required-network-bandwidth-mbps"></a>Larghezza di banda di rete necessaria (Mbps)
![Larghezza di banda di rete necessaria](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**To meet RPO 100% of the time** (Per soddisfare il valore RPO il 100% del tempo): larghezza di banda consigliata in Mbps da allocare per soddisfare il valore RPO desiderato il 100% del tempo. Questa larghezza di banda deve essere dedicata alla replica differenziale stazionaria di tutte le VM compatibili per evitare violazioni di RPO.

**To meet RPO 90% of the time** (Per soddisfare il valore RPO il 90% del tempo): se a causa dei prezzi della banda larga o per altri motivi non è possibile impostare la larghezza di banda necessaria per soddisfare il valore RPO desiderato il 100% del tempo, è possibile scegliere di passare a una larghezza di banda inferiore che possa soddisfare il valore RPO desiderato il 90% del tempo. Per comprendere le implicazioni dell'impostazione di questa larghezza di banda inferiore, il report offre un'analisi ipotetica del numero e della durata delle violazioni del valore RPO prevedibili.

**Achieved Throughput** (Velocità effettiva ottenuta): velocità effettiva dal server in cui è stato eseguito il comando GetThroughput all'area di Microsoft Azure in cui si trova l'account di archiviazione. Questo valore di velocità effettiva indica il livello stimato che è possibile ottenere quando si proteggono le macchine virtuali compatibili con Azure Site Recovery, a condizione che le caratteristiche di archiviazione e di rete del server Hyper-V rimangano uguali a quelle del server da cui è stato eseguito lo strumento.

Per tutte le distribuzioni aziendali di Azure Site Recovery è consigliabile usare [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Account di archiviazione necessari
Il grafico seguente indica il numero totale di account di archiviazione (Standard e Premium) necessari per proteggere tutte le VM compatibili. Per informazioni sull'account di archiviazione da usare per ogni VM, vedere la sezione "Selezione host di archiviazione delle VM".

![Account di archiviazione necessari](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Numero di core Azure necessari
Questo risultato è il numero totale di core da configurare prima del failover o del failover di test di tutte le VM compatibili. Se i core disponibili nella sottoscrizione sono troppo pochi, Azure Site Recovery non creerà le macchine virtuali al momento del failover di test o del failover.
![Required number of Azure cores](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png) (Numero di core Azure necessari)


### <a name="additional-on-premises-storage-requirement"></a>Requisito di archiviazione locale aggiuntivo

Spazio di archiviazione totale disponibile necessario nei server Hyper-V per eseguire correttamente la replica iniziale e la replica differenziale, in modo da garantire che la replica delle macchine virtuali non provochi tempo di inattività non desiderato per le applicazioni di produzione. I dettagli dei requisiti di ogni volume sono disponibili in [On-premises Storage Requirement](#on-premises-storage-requirement) (Requisito di archiviazione locale). 

Per comprendere il motivo per cui per la replica è necessario spazio disponibile, vedere la sezione [Requisito di archiviazione locale](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication).

![Requisito di archiviazione locale e frequenza di copia](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Frequenza di copia massima
È necessario impostare la frequenza di copia massima consigliata per la replica per ottenere l'obiettivo del punto di ripristino desiderato. Il valore predefinito è 5 minuti. Per ottenere un obiettivo del punto di ripristino migliore, è possibile impostare una frequenza di copia di 30 secondi.

### <a name="what-if-analysis"></a>Analisi di simulazione
![Analisi di simulazione](media/site-recovery-hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Questa analisi indica quante violazioni possono verificarsi durante il periodo di profilatura quando si imposta una larghezza di banda inferiore per soddisfare il valore RPO desiderato solo il 90% del tempo. Possono verificarsi una o più violazioni del valore RPO in un giorno. Il grafico illustra il valore RPO di picco del giorno. In base a questa analisi è possibile stabilire se il numero di violazioni del valore RPO per il totale dei giorni e il valore RPO di picco raggiunto ogni giorno siano accettabili con la larghezza di banda inferiore specificata. Se sono accettabili, è possibile allocare una larghezza di banda inferiore per la replica, altrimenti allocare una larghezza di banda superiore come suggerito per soddisfare il valore RPO desiderato il 100% del tempo. 

### <a name="recommendation-for-successful-initial-replication"></a>Raccomandazioni per il corretto completamento della replica iniziale
In questa sezione vengono indicati il numero di batch consigliato per le macchine virtuali da proteggere e la larghezza di banda minima necessaria per completare correttamente la replica iniziale. 

![Suddivisione in batch per la replica iniziale](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

La macchina virtuale deve essere protetta in base all'ordine dei batch specificato. Ogni batch prevede un elenco specifico di macchine virtuali. Le macchine virtuali del batch 1 devono essere protette prima di quelle del batch 2. Le macchine virtuali del batch 2 devono essere protette prima di quelle del batch 3 e così via. Una volta completata la replica iniziale delle macchine virtuali del batch 1, è possibile abilitare la replica per le macchine virtuali del batch 2. Analogamente, una volta completata la replica iniziale delle macchine virtuali del batch 2, è possibile abilitare la replica per le macchine virtuali del batch 3 e così via. Se l'ordine dei batch non viene rispettato, potrebbe non essere disponibile larghezza di banda sufficiente per la replica iniziale delle macchine virtuali, che verranno protette in un secondo momento. Di conseguenza, le macchine virtuali potrebbero non completare mai la replica iniziale oppure alcune macchine virtuali protette potrebbero passare alla modalità di risincronizzazione. Il foglio relativo alla suddivisione in batch per la replica iniziale per il valore RPO selezionato contiene informazioni dettagliate sulle macchine virtuali da includere in ogni batch.

Il grafico mostra la distribuzione della larghezza di banda per la replica iniziale e la replica differenziale tra i batch nell'ordine specificato. Quando si proteggono le macchine virtuali del primo batch, è disponibile tutta la larghezza di banda per la replica iniziale. Una volta completata la replica iniziale per il primo batch, una parte della larghezza di banda sarà necessaria per la replica differenziale. La larghezza di banda rimanente sarà disponibile per la replica iniziale delle macchine virtuali del secondo batch. La barra relativa al batch 2 mostra la larghezza di banda necessaria per la replica differenziale delle macchine virtuali del batch 1 e la larghezza di banda disponibile per la replica iniziale delle macchine virtuali del batch 2. Analogamente, la barra relativa al batch 3 mostra la larghezza di banda necessaria per la replica differenziale per i batch precedenti (macchine virtuali del batch 1 e del batch 2) e la larghezza di banda disponibile per la replica iniziale per il batch 3 e così via.  Una volta completata la replica iniziale di tutti i batch, l'ultima barra mostra la larghezza di banda necessaria per la replica differenziale per tutte le macchine virtuali protette. 

**Perché è necessaria la suddivisione in batch per la replica iniziale?**
Il tempo di completamento della replica iniziale varia in base alle dimensioni del disco delle macchine virtuali, allo spazio su disco usato e alla velocità effettiva di rete disponibile. Nel foglio relativo alla suddivisione in batch per la replica iniziale per il valore RPO selezionato sono disponibili informazioni dettagliate.

### <a name="cost-estimation"></a>Stima dei costi
Il grafico mostra una vista di riepilogo del costo totale stimato per il ripristino di emergenza in Azure per l'area di destinazione scelta e la valuta specificata per la generazione del report.
![Riepilogo della stima dei costi](media/site-recovery-hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png) Il riepilogo consente di conoscere il costo che è necessario sostenere per archiviazione, calcolo, rete e licenza in caso di protezione di tutte le macchine virtuali compatibili in Azure con Azure Site Recovery. Il costo viene calcolato per le macchine virtuali compatibili e non per tutte le macchine virtuali profilate.  
 
È possibile visualizzare il costo su base mensile o annuale. Vedere altre informazioni su [aree di destinazione supportate](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e [valute supportate](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components** (Costo per componente): il costo totale per il ripristino di emergenza è suddiviso tra quattro componenti, ovvero calcolo, archiviazione, rete e costo della licenza di Azure Site Recovery. Il costo viene calcolato in base al consumo durante la replica e l'esercitazione sul ripristino di emergenza per le risorse di calcolo e archiviazione (Premium e Standard), la connessione ExpressRoute/VPN configurata tra il sito locale e Azure e la licenza di Azure Site Recovery.

**Cost by states** (Costo per stato): il costo totale per il ripristino di emergenza è suddiviso in categorie in base a due diversi stati, ovvero la replica e l'esercitazione sul ripristino di emergenza. 

**Costo della replica**: costo che verrà addebitato durante la replica. Include il costo di archiviazione, rete e licenza di Azure Site Recovery. 

**Costo dell'esercitazione sul ripristino di emergenza**: costo che verrà addebitato durante i failover di test. Durante il failover di test, Azure Site Recovery avvia le macchine virtuali. Il costo dell'esercitazione sul ripristino di emergenza include il costo di calcolo e archiviazione delle macchine virtuali in esecuzione. 

**Azure storage cost per Month/Year** (Costo di archiviazione mensile/annuale di Azure): mostra il costo di archiviazione totale che verrà addebitato per l'archiviazione Premium e Standard per la replica e l'esercitazione sul ripristino di emergenza.
È possibile visualizzare un'analisi dettagliata dei costi per macchina virtuale nel foglio [Cost Estimation](site-recovery-hyper-v-deployment-planner-cost-estimation.md) (Stima dei costi).

### <a name="growth-factor-and-percentile-values-used"></a>Fattore di crescita e valori percentili usati
Questa sezione nella parte inferiore del foglio indica il valore percentile usato per tutti i contatori delle prestazioni delle VM profilate (il valore predefinito è il 95° percentile) e il fattore di crescita (il valore predefinito è il 30%) usato in tutti i calcoli.

![Fattore di crescita e valori percentili usati](media/site-recovery-hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Raccomandazioni con la larghezza di banda disponibile come input
![Panoramica della profilatura con input della larghezza di banda](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Si possono verificare situazioni in cui non è possibile impostare una larghezza di banda oltre un dato valore di Mbps per la replica di Azure Site Recovery. Lo strumento consente di immettere la larghezza di banda disponibile (con il parametro -Bandwidth durante la generazione di report) e determinare il valore RPO ottenibile in minuti. Con questo valore RPO ottenibile, è possibile stabilire se effettuare il provisioning di larghezza di banda aggiuntiva o se è sufficiente una soluzione di ripristino di emergenza con questo valore RPO.

![RPO ottenibile](media/site-recovery-hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Raccomandazione per la selezione host di archiviazione delle VM 

![Selezione host di archiviazione delle VM](media/site-recovery-hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Disk Storage Type** (Tipo di archiviazione disco): account di archiviazione Standard o Premium usato per replicare tutte le VM corrispondenti indicate nella colonna **VMs to Place** (VM da posizionare).

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri che può essere usato per la denominazione dell'account di archiviazione. È possibile usare un prefisso personalizzato, ma il suggerimento dello strumento segue la [convenzione di denominazione delle partizioni per gli account di archiviazione](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Nome account suggerito): nome dell'account di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Log Storage Account** (Account di archiviazione log): tutti i log di replica vengono archiviati in un account di archiviazione Standard. Per le VM che eseguono la replica in un account di archiviazione Premium, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione log. Un singolo account di archiviazione log Standard può essere usato da più account di archiviazione di replica Premium. Le VM replicate negli account di archiviazione Standard usano lo stesso account di archiviazione per i log.

**Suggested Log Account Name** (Nome account log suggerito): nome dell'account log di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Placement Summary** (Riepilogo di selezione host): riepilogo del carico totale delle VM nell'account di archiviazione al momento della replica e del failover di test o del failover. Comprende il numero totale di VM mappate all'account di archiviazione, il numero totale di operazioni di I/O al secondo in lettura/scrittura per tutte le VM inserite in questo account di archiviazione, il totale delle operazioni di I/O al secondo in scrittura (replica), le dimensioni totali configurate per tutti i dischi e il numero totale di dischi.

**VMs to Place** (Macchine virtuali da inserire): elenco di tutte le VM da inserire nell'account di archiviazione specificato per ottenere uso e prestazioni ottimali.

## <a name="compatible-vms"></a>VM compatibili
Il report di Microsoft Excel generato da Azure Site Recovery Deployment Planner fornisce tutti i dettagli relativi alle macchine virtuali compatibili nel foglio "Compatible VMs" (Macchine virtuali compatibili).

![VM compatibili](media/site-recovery-hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM Name** (Nome macchina virtuale): nome della macchina virtuale usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (dischi rigidi virtuali) collegati alle macchine virtuali. I nomi includono i nomi host Hyper-V in cui si trovavano le macchine virtuali quando lo strumento le ha rilevate durante il periodo di profilatura.

**VM Compatibility** (Compatibilità VM): i valori sono **Yes** e **Yes**\*. **Yes**\* è per i casi in cui la VM è idonea per l'[Archiviazione Premium di Azure](https://aka.ms/premium-storage-workload). In questo caso, il disco profilato per valori elevati di operazioni di I/O al secondo o varianza rientra in una categoria di dimensioni del disco Premium più elevata rispetto a quella mappata al disco. In base alle dimensioni, l'account di archiviazione decide a quale tipo di disco di archiviazione Premium mappare un disco. 
* <128 GB rientrano nella categoria P10.
* Da 128 GB a 256 GB rientrano nella categoria P15.
* Da 256 GB a 512 GB rientrano nella categoria P20.
* Da 512 GB a 1024 GB rientrano nella categoria P30.
* Da 1025 GB a 2048 GB rientrano nella categoria P40.
* Da 2049 GB a 4095 GB rientrano nella categoria P50.

Se, ad esempio, in virtù delle caratteristiche del carico di lavoro un disco appartiene alla categoria P20 o P30, ma le dimensioni sono mappate a un tipo di disco di archiviazione Premium inferiore, lo strumento contrassegna la macchina virtuale con **Yes**\* (Sì). Lo strumento consiglia anche di modificare le dimensioni del disco di origine per renderlo idoneo al tipo di disco di archiviazione Premium raccomandato oppure di modificare il tipo di disco di destinazione dopo il failover.

**Storage Type** (Tipo di archiviazione): Standard o Premium.

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri dell'account di archiviazione.

**Storage Account** (Account di archiviazione): nome che usa il prefisso dell'account di archiviazione suggerito.

**Peak R/W IOPS (with Growth Factor)** (Picco operazioni di I/O al secondo in lettura/scrittura - con fattore di crescita): carico di lavoro di picco di operazioni di I/O al secondo in lettura/scrittura nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura di una macchina virtuale non corrisponde sempre alla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della macchina virtuale, perché il picco di operazioni di I/O al secondo in lettura/scrittura della macchina virtuale è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Peak Data Churn in MBps (with Growth Factor)** (Picco varianza dati in Mbps - con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Azure VM Size** (Dimensioni VM Azure): dimensioni ideali del mapping per Servizi cloud di Azure per questa VM locale. Il mapping si basa sulla memoria, sul numero di dischi/core/schede di interfaccia di rete e operazioni di I/O al secondo in lettura/scrittura della VM locale. La raccomandazione si riferisce sempre alle dimensioni minime della macchina virtuale di Azure corrispondenti a tutte le caratteristiche della macchina virtuale locale.

**Number of Disks** (Numero di dischi): numero totale dei dischi (dischi rigidi virtuali) nella macchina virtuale.

**Disk size (GB)** (Dimensioni disco - GB): dimensioni totali di tutti i dischi della macchina virtuale. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI.

## <a name="incompatible-vms"></a>VM incompatibili
Il report di Microsoft Excel generato da Azure Site Recovery Deployment Planner fornisce tutti i dettagli relativi alle macchine virtuali incompatibili nel foglio "Incompatible VMs" (Macchine virtuali incompatibili).

![VM incompatibili](media/site-recovery-hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM Name** (Nome macchina virtuale): nome della macchina virtuale usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (dischi rigidi virtuali) collegati alle macchine virtuali. I nomi includono i nomi host Hyper-V in cui si trovavano le macchine virtuali quando lo strumento le ha rilevate durante il periodo di profilatura.

**VM Compatibility** (Compatibilità macchina virtuale): indica perché la macchina virtuale specifica non è compatibile per l'uso con Azure Site Recovery. I motivi vengono descritti per ogni disco incompatibile della VM e, in base ai [limiti di archiviazione](https://aka.ms/azure-storage-scalbility-performance) pubblicati, possono essere uno dei seguenti:

* Il disco ha dimensioni superiori a 4095 GB. Archiviazione di Azure attualmente non supporta dischi dati di dimensioni superiori a 4095 GB.

* Il disco del sistema operativo ha dimensioni superiori a 2047 GB per una macchina virtuale di generazione 1 (tipo di avvio BIOS).  Azure Site Recovery non supporta dischi del sistema operativo di dimensioni superiori a 2047 GB per le macchine virtuali di generazione 1.

* Il disco del sistema operativo ha dimensioni superiori a 300 GB per una macchina virtuale di generazione 2 (tipo di avvio EFI). Azure Site Recovery non supporta dischi del sistema operativo di dimensioni superiori a 300 GB per le macchine virtuali di generazione 2.

* Il nome della macchina virtuale contiene uno dei caratteri seguenti “” [] ` che non sono supportati.  Lo strumento non può ottenere i dati profilati di macchine virtuali che contengono uno di questi caratteri nel nome. 

* Il disco rigido virtuale è condiviso da due o più macchine virtuali. Azure non supporta le macchine virtuali con disco rigido virtuale condiviso.

* Le macchine virtuali con Virtual Fiber Channel non sono supportate. Azure Site Recovery non supporta le macchine virtuali con Virtual Fiber Channel.

* Il cluster Hyper-V non contiene il gestore di replica. Azure Site Recovery non supporta una macchina virtuale in un cluster Hyper-V se il gestore di replica Hyper-V non è configurato per il cluster.

* La macchina virtuale non offre disponibilità elevata. Azure Site Recovery non supporta una macchina virtuale del nodo del cluster Hyper-V con dischi rigidi virtuali archiviati nel disco locale invece che nel disco del cluster. 

* Le dimensioni totali della macchina virtuale (replica + failover di test) superano il limite supportato dall'account di archiviazione Premium (35 TB). Questa incompatibilità si verifica in genere quando un singolo disco della macchina virtuale ha caratteristiche di prestazioni che superano i limiti massimi supportati da Azure o da Azure Site Recovery per l'archiviazione Standard. In questo caso la VM rientra nell'area dell'archiviazione Premium. Le dimensioni massime supportate da un account di archiviazione Premium sono tuttavia pari a 35 TB e non è possibile proteggere una singola VM su più account di archiviazione. Si noti anche che, quando un failover di test viene eseguito in una macchina virtuale protetta e per tale failover di test è configurato un disco non gestito, il failover viene eseguito nello stesso account di archiviazione in cui è in corso la replica. In questo caso, è necessaria una quantità aggiuntiva di spazio di archiviazione equivalente a quella necessaria per la replica. La replica può così proseguire e il failover di test può venire completato in parallelo. Quando per il failover di test è configurato un disco gestito, non è necessario allocare spazio aggiuntivo per la macchina virtuale usata per il failover di test.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 7500 operazioni per disco.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 80.000 operazioni per ogni VM.

* La varianza media dei dati supera il limite supportato da Azure Site Recovery di 10 MBps per dimensioni I/O medie del disco.

* Il numero medio di operazioni di I/O al secondo in scrittura effettive supera il limite supportato da Azure Site Recovery di 840 operazioni per disco.

* L'archiviazione snapshot calcolata supera il limite supportato di 10 TB.

**Peak R/W IOPS (with Growth Factor)** (Picco operazioni di I/O al secondo in lettura/scrittura - con fattore di crescita): carico di lavoro di picco di operazioni di I/O al secondo nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura della VM non è sempre costituito dalla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM, perché il picco di operazioni di I/O al secondo in lettura/scrittura della VM è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Peak Data Churn in MBps (with Growth Factor)** (Picco varianza dati in Mbps - con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Number of Disks** (Numero di dischi): numero totale di dischi rigidi virtuali nella macchina virtuale.

**Disk size (GB)** (Dimensioni disco - GB): dimensioni di installazione totale di tutti i dischi della VM. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): quantità di RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI.

## <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery
La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti si basano su test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. Per risultati ottimali, anche dopo la pianificazione della distribuzione è sempre consigliabile eseguire test approfonditi delle applicazioni con un failover di test per ottenere il quadro reale delle prestazioni dell'applicazione.
 
**Destinazione archiviazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MBps | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MBps | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MBps |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MBps | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MBps | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |10 MBps | 842 GB per disco

Questi limiti rappresentano valori medi presupponendo una sovrapposizione di I/O del 30%. Azure Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro. I numeri precedenti presuppongono un backlog tipico di circa cinque minuti, ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="on-premises-storage-requirement"></a>Requisito di archiviazione locale

Il foglio di lavoro indica i requisiti di spazio di archiviazione totale disponibile per ogni volume dei server Hyper-V (dove si trovano i dischi rigidi virtuali) per la corretta esecuzione della replica iniziale e della replica differenziale. Prima di abilitare la replica, aggiungere ai volumi lo spazio di archiviazione necessario, in modo da garantire che la replica non provochi tempo di inattività non desiderato per le applicazioni di produzione. 

Azure Site Recovery Deployment Planner identifica i requisiti ottimali per lo spazio di archiviazione in base alle dimensioni dei dischi rigidi virtuali e alla larghezza di banda di rete usata per la replica.

![Requisito di archiviazione locale](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Perché è necessario spazio disponibile nel server Hyper-V per la replica?
* Quando si abilita la replica di una macchina virtuale, Azure Site Recovery crea uno snapshot di ogni disco rigido virtuale della macchina virtuale per la replica iniziale. Mentre è in corso la replica iniziale, le nuove modifiche vengono scritte nei dischi dall'applicazione. Azure Site Recovery tiene traccia delle modifiche differenziali nei file di log e ciò richiede spazio di archiviazione aggiuntivo.  Fino al completamento della replica iniziale, i file di log vengono archiviati in locale. Se non è disponibile spazio sufficiente per i file di log e lo snapshot (AVHDX), la replica passa in modalità di risincronizzazione e non viene mai completata. Nel peggiore dei casi, è necessario spazio disponibile aggiuntivo pari alle dimensioni del disco rigido virtuale per la replica iniziale.
* Una volta completata la replica iniziale, viene avviata la replica differenziale. Azure Site Recovery tiene traccia delle modifiche differenziali nei file di log che vengono archiviati nel volume in cui si trovano i dischi rigidi virtuali della macchina virtuale. Questi file di log vengono replicati in Azure in base alla frequenza di copia configurata. A seconda della larghezza di banda di rete disponibile, può essere necessaria una determinata quantità di tempo per la replica dei file di log in Azure. Se non è disponibile spazio sufficiente per archiviare i file di log, la replica viene messa in pausa e lo stato della replica della macchina virtuale passa a Risincronizzazione richiesta.
* Se la larghezza di banda di rete non è sufficiente per eseguire il push dei file di log in Azure, i file di log si accumulano nel volume. Nello scenario peggiore, quando le dimensioni dei file di log aumentano fino a raggiungere il 50% delle dimensioni del disco rigido virtuale, la replica della macchina virtuale passa a Risincronizzazione richiesta. Nel peggiore dei casi, è necessario spazio disponibile aggiuntivo pari al 50% delle dimensioni del disco rigido virtuale per la replica differenziale.

**Hyper-V host** (Host Hyper-V): elenco dei server Hyper-V profilati. Se un server fa parte di un cluster Hyper-V, tutti i nodi del cluster vengono raggruppati insieme.

**Volume (VHD path)** (Volume - percorso disco rigido virtuale): ogni volume di un host Hyper-V in cui sono presenti dischi rigidi virtuali o dischi VHDX. 

**Free space available (GB)** (Spazio disponibile - GB): spazio disponibile nel volume.

**Total storage space required on the volume (GB)** (Spazio di archiviazione totale necessario nel volume - GB): spazio di archiviazione totale disponibile necessario nel volume per la corretta esecuzione della replica iniziale e della replica differenziale. 

**Total additional storage to be provisioned on the volume for successful replication** (Spazio di archiviazione aggiuntivo totale di cui eseguire il provisioning nel volume per il completamento della replica): indica lo spazio aggiuntivo totale di cui è necessario eseguire il provisioning nel volume per la corretta esecuzione della replica iniziale e della replica differenziale.

## <a name="initial-replication-batching"></a>Suddivisione in batch per la replica iniziale 

### <a name="why-do-i-need-initial-replication-ir-batching"></a>Perché è necessaria la suddivisione in batch per la replica iniziale?
Se tutte le macchine virtuali venissero protette contemporaneamente, lo spazio di archiviazione disponibile richiesto sarebbe molto maggiore e in assenza di spazio sufficiente la replica delle macchine virtuali passerebbe in modalità di risincronizzazione. Anche il requisito di larghezza di banda di rete per completare correttamente la replica iniziale di tutte le macchine virtuali contemporaneamente sarebbe molto maggiore. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Suddivisione in batch per la replica iniziale per l'obiettivo del punto di ripristino selezionato
Questo foglio di lavoro fornisce una vista dettagliata di ogni batch per la replica iniziale. Per ogni obiettivo del punto di ripristino viene creato un foglio distinto relativo alla suddivisione in batch per la replica iniziale. 

Dopo aver seguito le raccomandazioni relative ai requisiti per lo spazio di archiviazione locale per ogni volume, le informazioni principali necessarie per la replica sono costituite dall'elenco delle macchine virtuali che è possibile proteggere in parallelo. Queste macchine virtuali vengono raggruppate in un batch. Possono esserci più batch. È necessario proteggere le macchine virtuali in base all'ordine dei batch specificato. Proteggere prima di tutto le macchine virtuali del batch 1 e, una volta completata la replica iniziale, proteggere le macchine virtuali del batch 2 e così via. È possibile ottenere l'elenco dei batch e delle macchine virtuali corrispondenti da questo foglio. 

![Dettagli batch 1 per la replica iniziale](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)
![Dettagli batch 2 per la replica iniziale](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Ogni batch fornisce le informazioni seguenti 
**Hyper-V host** (Host Hyper-V): host Hyper-V della macchina virtuale da proteggere.
Virtual machine (Macchina virtuale): macchina virtuale da proteggere. 

**Comments** (Commenti): se sono necessarie azioni per un volume specifico di una macchina virtuale, i commenti vengono indicati in questa posizione.  Se, ad esempio, non è disponibile spazio sufficiente in un volume, nel commento viene indicato di aggiungere spazio per proteggere la macchina virtuale.

**Volume (VHD path)** (Volume - percorso disco rigido virtuale): nome del volume in cui si trovano i dischi rigidi virtuali della macchina virtuale. Free space available on the volume (GB) (Spazio disponibile nel volume - GB): spazio su disco disponibile nel volume per la macchina virtuale. Per il calcolo dello spazio disponibile nei volumi, viene considerato lo spazio su disco usato per la replica differenziale dalle macchine virtuali dei batch precedenti i cui dischi rigidi virtuali si trovano nello stesso volume.  

Si presupponga, ad esempio che le macchine virtuali VM1, VM2 e VM3 si trovino nel volume E:\percorsoVHD. Prima della replica, lo spazio disponibile nel volume è di 500 GB. VM1 fa parte del batch 1, VM2 fa parte del batch 2 e VM3 fa parte del batch 3.  Per VM1, lo spazio disponibile è 500 GB. Per VM2, lo spazio disponibile è 500 meno lo spazio su disco necessario per la replica differenziale per VM1.  Se VM1 richiede 300 GB di spazio per la replica differenziale, lo spazio disponibile per VM2 è 500 GB - 300 GB = 200 GB.  Analogamente, VM2 richiede 300 GB per la replica differenziale. Lo spazio disponibile per VM3 è 200 GB - 300 GB = -100 GB.

**Storage required on the volume for initial replication (GB)** (Spazio di archiviazione necessario nel volume per la replica iniziale - GB): spazio di archiviazione disponibile necessario nel volume per la replica iniziale della macchina virtuale.

**Storage required on the volume for delta replication (GB)** (Spazio di archiviazione necessario nel volume per la replica differenziale - GB): spazio di archiviazione disponibile necessario nel volume per la replica differenziale della macchina virtuale.

**Additional storage required based on deficit to avoid replication failure (GB)** (Spazio di archiviazione aggiuntivo necessario in base al disavanzo per evitare errori di replica - GB): spazio di archiviazione aggiuntivo necessario nel volume per la macchina virtuale.  Corrisponde al requisito massimo di spazio di archiviazione disponibile nel volume per la replica iniziale e la replica differenziale.

**Minimum bandwidth required for initial replication (Mbps)** (Larghezza di banda minima necessaria per la replica iniziale - Mbps): larghezza di banda minima necessaria per la replica iniziale della macchina virtuale.

**Minimum bandwidth required for initial replication (Mbps)** (Larghezza di banda minima necessaria per la replica differenziale - Mbps): larghezza di banda minima necessaria per la replica differenziale della macchina virtuale.

### <a name="network-utilization-details-for-each-batch"></a>Dettagli relativi all'utilizzo di rete per ogni batch 
La tabella di ogni batch fornisce un riepilogo dell'utilizzo di rete del batch.

**Bandwidth available for Batch** (Larghezza di banda disponibile per il batch): larghezza di banda disponibile per il batch dopo aver considerato la larghezza di banda per la replica differenziale del batch precedente.

**Approximate bandwidth available for initial replication of batch** (Larghezza di banda approssimativa disponibile per la replica iniziale del batch): larghezza di banda disponibile per la replica iniziale delle macchine virtuali del batch. 

**Approximate bandwidth consumed for delta replication of batch** (Larghezza di banda approssimativa utilizzata per la replica differenziale del batch): larghezza di banda necessaria per la replica differenziale delle macchine virtuali del batch. 

**Estimated Initial Replication time for Batch (HH:MM)** (Tempo stimato per la replica iniziale per il batch - HH:MM): tempo stimato per la replica iniziale in ore:minuti.

## <a name="cost-estimation"></a>Stima dei costi
Vedere altre informazioni sulla [stima dei costi](site-recovery-hyper-v-deployment-planner-cost-estimation.md). 

## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulla [stima dei costi](site-recovery-hyper-v-deployment-planner-cost-estimation.md).