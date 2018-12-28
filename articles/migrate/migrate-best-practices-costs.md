---
title: Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 9d1acabd07e7c01445c55a57be9b0c9a36140aa5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163770"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure

Quando si pianifica e si progetta una migrazione in Azure, una particolare attenzione ai costi ne garantisce il successo a lungo termine. Durante un progetto di migrazione, è di fondamentale importanza che tutti i team (finanziario, gestionale, delle applicazioni e così via) comprendano i costi associati.
- Prima della migrazione, stimare la spesa necessaria in base a obiettivi di budget mensili, trimestrali e annuali è fondamentale per il successo.
- Dopo la migrazione è necessario ottimizzare i costi, monitorare continuamente i carichi di lavoro e pianificare i modelli di utilizzo futuro. Le risorse su cui è stata effettuata una migrazione potrebbero presentare inizialmente un tipo di carico di lavoro ed evolvere nel corso del tempo in un tipo diverso in base a utilizzo, costi e requisiti aziendali mutevoli.

Questo articolo descrive le procedure consigliate per la determinazione dei costi e il ridimensionamento prima e dopo la migrazione.  

> [!IMPORTANT]
> Le procedure consigliate e le opinioni descritte in questo articolo si basano sulle funzionalità dei servizi e della piattaforma di Azure disponibili al momento della redazione di questo documento. Caratteristiche e funzionalità mutano nel tempo. È possibile che non tutti i consigli siano applicabili alla distribuzione in uso e che sia pertanto necessario scegliere ciò che meglio si adatta alle proprie esigenze.


## <a name="before-migration"></a>Prima della migrazione 

Prima di spostare i carichi di lavoro nel cloud, stimare il costo mensile dell'esecuzione in Azure. La gestione proattiva dei costi del cloud consente di rispettare il budget delle spese operative (OpEx). Se il budget è limitato, tenerne conto prima della migrazione.  Per ridurre i costi è possibile considerare, laddove appropriato, la conversione dei carichi di lavoro in tecnologie serverless di Azure.

Le procedure consigliate in questa sezione forniscono supporto per stimare i costi, eseguire il ridimensionamento corretto per le macchine virtuali e le risorse di archiviazione, sfruttare i vantaggi di Azure Hybrid, usare VM riservate e stimare la spesa per il cloud nelle diverse sottoscrizioni.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Procedura consigliata: Stimare i costi mensili del carico di lavoro
 
Per prevedere i costi mensili per i carichi di lavoro migrati sono disponibili numerosi strumenti.

- **Calcolatore prezzi di Azure**: Selezionare i prodotti per cui si desidera calcolare una stima, ad esempio le macchine virtuali e le risorse di archiviazione. La stima viene calcolata immettendo i costi nel calcolatore prezzi.

 ![Calcolatore prezzi di Azure](./media/migrate-best-practices-costs/pricing.png) *Calcolatore prezzi di Azure*

- **Azure Migrate**: Per stimare i costi è necessario esaminare e calcolare tutte le risorse necessarie all'esecuzione dei carichi di lavoro in Azure. Per acquisire questi dati si crea un inventario degli asset, inclusi server, macchine virtuali, database e risorse di archiviazione. Per raccogliere queste informazioni è possibile usare Azure Migrate.

 - Con Azure Migrate l'ambiente locale viene individuato e valutato allo scopo di fornire un inventario.
 - Azure Migrate può eseguire il mapping e mostrare le dipendenze tra macchine virtuali in modo da offrire all'utente un quadro completo.
 - Una valutazione di Azure Migrate contiene il costo stimato.
    - Costi di calcolo: Se durante la creazione di una valutazione si usano le dimensioni consigliate della macchina virtuale di Azure, Azure Migrate userà l'API di fatturazione per calcolare i costi mensili stimati della macchina virtuale. Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta. Nella valutazione vengono aggregati i costi di tutte le macchine virtuali e calcolato il costo di calcolo totale mensile.
    - Costi di archiviazione: Azure Migrate calcola i costi di archiviazione mensili totali aggregando i costi di archiviazione di tutte le macchine virtuali comprese in una valutazione. È possibile calcolare il costo di archiviazione mensile di una macchina virtuale aggregando il costo mensile di tutti i dischi a essa collegati. 

    ![Azure Migrate](./media/migrate-best-practices-costs/assess.png) *Valutazione di Azure Migrate*

**Altre informazioni:**
- [Usare](https://azure.microsoft.com/pricing/calculator/) il calcolatore prezzi di Azure.
- [Panoramica](https://docs.microsoft.com/azure/migrate/migrate-overview) di Azure Migrate.
- [Informazioni sulle](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) valutazioni di Azure Migrate.
- [Altre informazioni](https://docs.microsoft.com/azure/dms/dms-overview) sul Servizio Migrazione del database.

## <a name="best-practice-right-size-vms"></a>Procedura consigliata: Ridimensionare le macchine virtuali in modo corretto

Quando si distribuiscono macchine virtuali di Azure per supportare i carichi di lavoro è possibile scegliere tra diverse opzioni. Ogni tipo di macchina virtuale presenta caratteristiche specifiche e differenti combinazioni di CPU, memoria e dischi. Le macchine virtuali sono raggruppate come indicato di seguito.

**Tipo** | **Dettagli** | **Uso**
--- | --- | ---
**Utilizzo generico** | Rapporto equilibrato tra CPU e memoria. | Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto.
**Ottimizzate per il calcolo** | Rapporto elevato tra CPU e memoria. | Soluzione ideale per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server di applicazioni.
**Ottimizzate per la memoria** | Rapporto elevato tra memoria e CPU. | Soluzione ideale per database relazionali, cache da medie a grandi dimensioni e analisi in memoria.
**Ottimizzate per l'archiviazione** | I/O e velocità effettiva del disco elevati. | Soluzione ideale per Big Data, database SQL e NoSQL.
**Ottimizzate per la GPU** | Macchine virtuali specializzate. GPU singole o multiple. | Prestazioni elevate di grafica e modifica di video.
**Prestazioni elevate** | CPU più veloce e potente. Macchine virtuali con interfacce di rete ad alta velocità effettiva opzionali (RDMA) | Applicazioni critiche ad alte prestazioni.

- È importante comprendere le differenze di prezzo tra le macchine virtuali e gli effetti a lungo termine sul budget.
- Ogni tipo di macchina virtuale presenta all'interno un numero di serie.
- Inoltre, quando si seleziona una macchina virtuale facente parte di una serie, è possibile ridimensionarla solo all'interno di tale serie. Ad esempio, DSv2\_2 può essere aumentata in DSv2\_4, ma non modificata in una serie diversa come Fsv2\_2.

**Altre informazioni:**
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) su tipi e ridimensionamento delle macchine virtuali e sul mapping delle dimensioni ai tipi.
- [Pianificare](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) il ridimensionamento della macchina virtuale.
- [Esaminare](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) un esempio di valutazione per la società fittizia Contoso.

## <a name="best-practice-select-the-right-storage"></a>Procedura consigliata: Selezionare la risorsa di archiviazione corretta

L'ottimizzazione e la gestione delle risorse di archiviazione locali (SAN o NAS) e delle reti necessarie per supportarle possono risultare costose e richiedere molto tempo. I dati (di archiviazione) dei file vengono generalmente migrati al cloud per ridurre i problemi operativi e di gestione. Microsoft offre varie opzioni per lo spostamento dei dati in Azure. È necessario prendere decisioni in merito a tale opzioni. La scelta del tipo di risorsa di archiviazione più adatta ai dati può consentire all'organizzazione di risparmiare diverse migliaia di dollari al mese. Alcune considerazioni:

- Non è necessario posizionare i dati a cui non si accede spesso e che non sono di tipo critico nella risorsa di archiviazione più costosa.
- Viceversa, i dati aziendali critici devono essere posizionati in opzioni di archiviazione di livello superiore.
- Durante la pianificazione della migrazione, accedere a un inventario di dati e classificarli per importanza per eseguirne il mapping alla risorsa di archiviazione più adatta. Prendere in considerazione budget e costi, nonché le prestazioni. I costi non devono necessariamente essere il motore principale dei processi decisionali. La selezione dell'opzione più economica potrebbe esporre il carico di lavoro a rischi di disponibilità e prestazioni. 

### <a name="storage-data-types"></a>Tipi di dati di archiviazione

Azure offre diversi tipi di dati di archiviazione.

**Tipo di dati** | **Dettagli** | **Utilizzo** 
--- | --- |  ---
**BLOB** | Ottimizzati per archiviare enormi quantità di oggetti non strutturati, come testo o dati binari<br/><br/> | Accedere ai dati tramite HTTP/HTTPS da qualsiasi posizione. | Uso per gli scenari di accesso casuale e streaming. Ad esempio, per rendere direttamente disponibili in un browser immagini e documenti, per eseguire lo streaming di video e audio e per archiviare i dati di ripristino di emergenza e backup.
**File** | Condivisioni di file gestite accessibili tramite SMB 3.0 | Per la migrazione delle condivisioni file in locale e per fornire più connessioni/accessi ai dati dei file.
**Dischi** | Basati su BLOB di pagine.<br/><br/> Tipo di disco (velocità): Standard (HDD o SSD) o Premium (SSD).<br/><br/>Gestione disco: Non gestito (l'utente sceglie le impostazioni del disco e di archiviazione) o gestito (l'utente seleziona il tipo di disco e Azure si occupa della gestione del disco). | Per le macchine virtuali, usare dischi Premium. Usare i dischi gestiti per gestione e scalabilità semplificate.
**Code** | Consentono di archiviare e recuperare grandi quantità di messaggi accessibili mediante chiamate autenticate (HTTP o HTTPS) | Connettere i componenti delle app con accodamento asincrono dei messaggi.
**Tabelle** | Archiviazione di tabelle. | Ora parte dell'API Tabella di Azure Cosmos DB.



### <a name="access-tiers"></a>Livelli di accesso
Archiviazione di Azure offre diverse opzioni per l'accesso ai dati BLOB in blocchi. Selezionando il livello di accesso adeguato è possibile archiviare i dati BLOB in blocchi nel modo economicamente più conveniente.

**Tipo** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Livello di archiviazione ad accesso frequente** | Costo di archiviazione maggiore rispetto al livello di accesso sporadico. Addebiti di accesso inferiori rispetto al livello di accesso sporadico.<br/><br/>Si tratta del livello predefinito. | Per i dati usati attivamente a cui si accede di frequente.
**Livello di archiviazione ad accesso sporadico** | Costo di archiviazione inferiore rispetto al livello di accesso frequente. Addebiti di accesso maggiori rispetto al livello di accesso frequente.<br/><br/> Archiviazione per almeno 30 giorni. | Archiviazione a breve termine, i dati sono disponibili ma l'accesso viene effettuato raramente.
**Archiviazione** | Usato per i BLOB in blocchi singoli.<br/><br/> L'opzione di archiviazione più conveniente. L'accesso ai dati è più costoso rispetto ai livelli di archiviazione ad accesso frequente e sporadico. | Per i dati che possono tollerare alcune ore di latenza di recupero e che rimarranno nel livello per almeno 180 giorni. 

### <a name="storage-account-types"></a>Tipo di account di archiviazione

Azure offre diversi tipi di account di archiviazione e livelli di prestazioni.

**Tipo di account** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Utilizzo generico v2 standard** | Supporta BLOB (blocco, pagina, accodamento), file, dischi, code e tabelle.<br/><br/> Supporta i livelli di archiviazione ad accesso frequente, sporadico e di archivio. Supporta l'archiviazione con ridondanza della zona. | Per la maggior parte degli scenari e dei tipi di dati. Gli account di archiviazione standard possono essere basati su unità HHD o SSD.
**Utilizzo generico v2 Premium** | Supporta i dati di archiviazione BLOB (BLOB di pagine). Supporta i livelli di archiviazione ad accesso frequente, sporadico e di archivio. Supporta l'archiviazione con ridondanza della zona.<br/><br/> Archiviati su unità SSD. | Microsoft ne consiglia l'uso per tutte le macchine virtuali.
**Utilizzo generico v1** | Non supporta la suddivisione in livelli di accesso. Non supporta l'archiviazione con ridondanza della zona | Per i casi in cui le applicazioni necessitano del modello di distribuzione classica di Azure.
**BLOB** | Account di archiviazione specializzato per archiviare gli oggetti non strutturati. Fornisce i BLOB in blocchi e accoda solo oggetti BLOB (nessun servizio archiviazione file, coda, tabella o disco). Fornisce la stessa durabilità, disponibilità, scalabilità e prestazioni dell'utilizzo generico v2. | In questi account non è possibile archiviare i BLOB di pagine. Di conseguenza, non consentono di archiviare file del disco rigido virtuale. È possibile impostare un livello di accesso frequente o sporadico.

### <a name="storage-redundancy-options"></a>Opzioni di ridondanza dell'archiviazione

Gli account di archiviazione possono usare diversi tipi di ridondanza per fornire resilienza e disponibilità elevata.

**Tipo** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Archiviazione con ridondanza locale (LRS)** | Protegge contro le interruzioni locali replicando i dati contenuti in una singola unità di archiviazione in un dominio di errore e di aggiornamento separati. Mantiene più copie dei dati in un data center. Offre una durabilità degli oggetti di almeno il 99,999999999% (undici 9) nel periodo di un anno. | Da prendere in considerazione se l'app archivia dati che possono essere ricostruiti facilmente.
**Archiviazione con ridondanza della zona (ZRS)** | Protegge contro le interruzioni del data center replicando i dati su tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità. Offre una durabilità degli oggetti di almeno 99,9999999999% (dodici 9) nel periodo di un anno mantenendo più copie dei dati in più data center o aree. | Da prendere in considerazione se si necessita di coerenza, durabilità e disponibilità elevata. Potrebbe non proteggere i dati in caso di un'emergenza a livello di area in cui più zone sono interessate in modo permanente.
**Archiviazione con ridondanza geografica (GRS)** | Protegge contro le interruzioni dell'intera area replicando i dati in un'area secondaria distante centinaia di miglia da quella primaria. Offre una durabilità degli oggetti di almeno 99,99999999999999% (sedici 9) nel periodo di un anno. | I dati di replica non sono disponibili a meno che Microsoft non avvii il failover all'area secondaria. Se si verifica il failover, è possibile accedere ai dati in lettura e scrittura.
**Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)** | Simile all'archiviazione con ridondanza geografica. Offre una durabilità degli oggetti di almeno 99,99999999999999% (sedici 9) nel periodo di un anno | Fornisce una disponibilità in lettura del 99,99% consentendo l'accesso in lettura dalla seconda area usata per l'archiviazione con ridondanza geografica.

**Altre informazioni:**
- [Rivedere](https://azure.microsoft.com/pricing/details/storage/) i prezzi di Archiviazione di Azure.
- [Informazioni su](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Importazione/Esportazione di Microsoft Azure per la migrazione di quantità elevate di dati in file e BLOB di Azure. 
- [Confrontare](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) BLOB, file e tipi di dati di archiviazione del disco.
- [Altre informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sui livelli di accesso.
- [Rivedere](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i diversi tipi di account di archiviazione.
- Informazioni su[ridondanza di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [archiviazione con ridondanza locale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e [archiviazione con ridondanza geografica e accesso in lettura](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Altre informazioni](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) su archiviazione file.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Procedura consigliata: Sfruttare i vantaggi di Azure Hybrid

Grazie agli anni di investimenti software in sistemi quali Windows Server e SQL Server, Microsoft gode di una posizione privilegiata per offrire ai clienti un valore nel cloud, con sconti considerevoli che altri provider di servizi cloud non necessariamente possono fornire. 

Un portfolio di prodotti locali Microsoft/Azure integrato genera vantaggi competitivi e relativi ai costi. Se è attualmente in uso un sistema operativo o un altro servizio di licenze software ottenuto tramite l'associazione di sicurezza, è possibile trasferire tali licenze al cloud con il Vantaggio Azure Hybrid.

**Altre informazioni:**

- [Visualizzare](https://azure.microsoft.com/pricing/hybrid-benefit/) il calcolatore del risparmio generato dal vantaggio di Azure Hybrid.
- [Altre informazioni](https://azure.microsoft.com/pricing/hybrid-benefit/) sul vantaggio di Azure Hybrid per Windows Server.
- [Rivedere](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance#bring-your-own-license-byol) la guida ai prezzi per le macchine virtuali di SQL Server in Azure.


## <a name="best-practice-use-reserved-vm-instances"></a>Procedura consigliata: Usare le istanze di macchina virtuale riservate

La maggior parte delle piattaforme cloud vengono configurate con pagamento in base al consumo. Questo modello presenta degli svantaggi in quanto l'utente non necessariamente avrà informazioni su come saranno i carichi di lavoro dinamici. Quando si specificano chiaramente le finalità di un carico di lavoro, si contribuisce alla pianificazione dell'infrastruttura.

Usando le istanze di macchina virtuale riservate di Azure, si paga in anticipo un'istanza di macchina virtuale per un periodo di uno o tre anni. 

- Il pagamento anticipato consente di accedere a uno sconto sulle risorse che si usano.
- È possibile ridurre notevolmente i costi di macchine virtuali, calcolo dei database SQL, Azure Cosmos DB o altre risorse risparmiando fino al 72% sui prezzi con pagamento in base al consumo. 
- Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.
- È possibile annullare le istanze riservate.

![Istanze riservate](./media/migrate-best-practices-costs/reserve.png)
*Macchine virtuali riservate di Azure*

**Altre informazioni:**
- [Informazioni sulle](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Prenotazioni di Azure.
- [Leggere](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) le domande frequenti sulle istanze riservate.
- [Guida ai prezzi](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance#bring-your-own-license-byol) per le macchine virtuali di SQL Server in Azure.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Procedura consigliata: Aggregare la spesa per il cloud tra più sottoscrizioni

È inevitabile che prima o poi sia necessaria più di una sottoscrizione di Azure. Ad esempio, potrebbe essere necessaria una sottoscrizione aggiuntiva per separare sviluppo e produzione o una piattaforma che richiede una sottoscrizione separata per ogni client. L'aggregazione dei report dei dati di tutte le sottoscrizioni in un'unica piattaforma è una funzionalità estremamente utile.

A tale scopo, è possibile usare le API di Gestione costi di Azure. Quindi, dopo l'aggregazione dei dati in una sola origine come SQL di Azure, è possibile visualizzare i dati aggregati tramite strumenti quali Power BI. È possibile creare report di sottoscrizioni aggregate e report dettagliati. Ad esempio, per gli utenti che necessitano di informazioni proattive per la gestione dei costi, è possibile creare viste specifiche dei costi in base a reparto, gruppo di risorse e così via. Non è necessario fornire l'accesso completo ai dati di fatturazione di Azure.

**Altre informazioni:**

- [Panoramica](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) dell'API per l'uso di Azure.
- [Informazioni sulla](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) connessione alle informazioni dettagliate sull'uso di Azure in Power BI Desktop.
- [Informazioni su come](https://docs.microsoft.com/azure/billing/billing-manage-access) gestire l'accesso alle informazioni di fatturazione per Azure usano il controllo degli accessi in base al ruolo.
 
## <a name="after-migration"></a>Dopo la migrazione

Dopo aver eseguito correttamente la migrazione dei carichi di lavoro e dopo aver raccolto i dati sul consumo per alcune settimane, si otterrà una chiara panoramica sui costi delle risorse.
- Procedendo con l'analisi dei dati, è possibile iniziare a generare un budget iniziale per le risorse e i gruppi di risorse di Azure.
- Quindi, man mano che si ottengono informazioni su dove viene speso il budget per il cloud, è possibile eseguire analisi per individuare come ridurre ulteriormente i costi.

Tra le procedure consigliate proposte in questa sezione vi è l'uso della Gestione costi di Azure per la definizione del budget e l'analisi dei costi, il monitoraggio delle risorse e l'implementazione dei budget per i gruppi di risorse nonché l'ottimizzazione di monitoraggio, archiviazione e macchine virtuali.

## <a name="best-practice-use-azure-cost-management"></a>Procedura consigliata: Usare Gestione costi di Azure

Microsoft offre il servizio di Gestione costi di Azure per tenere traccia della spesa come indicato di seguito:

- Consente di monitorare e controllare la spesa di Azure e di ottimizzare l'uso delle risorse.
- Esamina l'intera sottoscrizione e tutte le relative risorse generando elementi consigliati.
- Fornisce un'API completa per l'integrazione di strumenti esterni e sistemi finanziari per la generazione di report.
- Tiene traccia dell'utilizzo delle risorse e dei costi di gestione del cloud in una singola vista unificata.
- Fornisce informazioni operative e finanziarie avanzate che consentono di prendere decisioni ponderate.

Con Gestione costi è possibile:


- **Creare un budget**: Creare un budget per la responsabilità finanziaria.
    - È possibile considerare i servizi che si usano o per cui si effettua una sottoscrizione per un periodo specifico (mensile, trimestrale, annuale) e un ambito (sottoscrizioni/gruppi di risorse). Ad esempio, è possibile creare il budget per una sottoscrizione di Azure per un periodo di un mese, un trimestre o un anno.
    - Dopo la creazione, il budget viene visualizzato nell'analisi dei costi. Il confronto tra il budget e la spesa corrente è uno dei primi passaggi necessari per l'analisi di costi e spesa.
    - Quando le soglie massime del budget vengono raggiunte, è possibile inviare delle notifiche e-mail.
    - È possibile esportare i dati di gestione dei costi nella risorsa di archiviazione di Azure per analizzarli.

    ![Budget di Gestione costi](./media/migrate-best-practices-costs/budget.png) *Budget di Gestione costi di Azure*

- **Eseguire un'analisi dei costi**: L'analisi dei costi consente di esplorare e analizzare i costi dell'organizzazione per comprendere come vengono derivati e identificare le tendenze di spesa.
    - L'analisi dei costi è disponibile per gli utenti con contratto Enterprise.
    - È possibile visualizzare i dati di analisi dei costi per diversi ambiti, tra cui per dipartimento, account, sottoscrizione o gruppo di risorse.
    - È possibile ottenere un'analisi dei costi per visualizzare i costi totali per il mese corrente e i costi giornalieri accumulati. 

    ![Analisi di Gestione costi](./media/migrate-best-practices-costs/analysis.png) *Analisi di Gestione costi di Azure*
- **Ottenere consigli**: Ottenere consigli di Advisor che mostrano come ottimizzare e migliorare l'efficienza.


**Altre informazioni:**

- [Panoramica](https://docs.microsoft.com/azure/cost-management/overview) di Gestione costi di Azure.
- [Informazioni su](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) come ottimizzare gli investimenti per il cloud con Gestione costi di Azure.
- [Informazioni su come](https://docs.microsoft.com/azure/cost-management/use-reports) usare i report di Gestione costi di Azure.
- [Esercitazione](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) su come ottimizzare i costi grazie ai consigli.
- [Rivedere](https://docs.microsoft.com/rest/api/consumption/budgets) l'API per l'uso di Azure.

## <a name="best-practice-monitor-resource-utilization"></a>Procedura consigliata: Monitorare l'uso delle risorse

In Azure si paga a seconda delle risorse usate e non si paga quando queste non sono in uso. Per le macchine virtuali, la fatturazione si verifica al momento dell'allocazione della VM e non prevede addebiti dopo la deallocazione della stessa. A tal fine si dovrebbero monitorare le macchine virtuali in uso e verificare la loro dimensione.

- Per determinare la baseline è necessario valutare continuamente i carichi di lavoro della macchina virtuale.
- Se ad esempio il carico di lavoro viene usato intensamente dal lunedì al venerdì dalle 8:00 alle 18:00 ma raramente al di fuori di tali orari, è possibile effettuare il downgrade delle macchine virtuali quando fuori dagli orari di picco. Ciò potrebbe tradursi nella modifica delle dimensioni della macchina virtuale o nell'uso di set di scalabilità di macchine virtuali per eseguire le operazioni di aumento o riduzione della scalabilità in modo automatico.
- Alcune aziende scelgono di "posporre" le macchine virtuali, inserendole in un calendario che indica quando devono essere disponibili e quando non sono necessarie. 
- Oltre al monitoraggio delle macchine virtuali, è necessario monitorare altre risorse di rete, come ExpressRoute e i gateway di rete virtuali per identificare un uso eccessivo o troppo ridotto.
- È possibile monitorare l'utilizzo delle macchine virtuali tramite diversi strumenti Microsoft, tra cui Gestione costi di Azure, Monitoraggio di Azure e Azure Advisor. Sono disponibili anche strumenti di terze parti.  

**Altre informazioni:**
- Panoramica di [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Ottenere](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) consigli di Advisor sui costi.
- [Informazioni su come [ottimizzare i costi grazie ai consigli](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) ed [evitare addebiti imprevisti](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).
- [Informazioni sul](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) toolkit di ottimizzazione delle risorse di Azure

## <a name="best-practice-implement-resource-group-budgets"></a>Procedura consigliata: Implementare i budget dei gruppi di risorse

Spesso i gruppi di risorse vengono usati per stabilire i limiti dei costi. Insieme a questo modello di utilizzo, il team di Azure continua a sviluppare nuovi e migliorati modi per monitorare e analizzare le spese per le risorse su livelli diversi, tra cui la possibilità di creare i budget per gruppi di risorse e risorse.  

- Definire un budget per un gruppo di risorse consente di monitorare i costi a esso associati.
- È possibile attivare degli avvisi ed eseguire un'ampia gamma di playbook al raggiungimento o superamento del budget. 

**Altre informazioni:**

- [Informazioni su come](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) gestire i costi con i budget di Azure.
- [Seguire un'esercitazione](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) per creare e gestire un budget di Azure.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Procedura consigliata: Ottimizzare il periodo di conservazione dei dati di Monitoraggio di Azure

Quando si spostano le risorse in Azure e si abilita la registrazione diagnostica, vengono generati un gran numero di dati di log. In genere tali dati di log vengono inviati a un account di archiviazione su cui è stato eseguito il mapping a un'area di lavoro di Log Analytics.

- Più è esteso il periodo di conservazione dei dati di log, maggiore sarà il numero di dati a disposizione.
- Non tutti i dati di log sono uguali. Alcune risorse genereranno più dati di log rispetto ad altre.
- A fini normativi e di conformità, è probabile che sia necessario conservare i dati di log di alcune risorse più a lungo rispetto ad altri.
- È necessario trovare un buon bilanciamento tra l'ottimizzazione dei costi di archiviazione dei log e la conservazione dei dati di log necessari.
- Consigliamo di valutare e configurare le registrazioni subito dopo aver completato la migrazione, in modo da non dover affrontare spese per la conservazione di log non necessari.

**Altre informazioni:**

- [Informazioni sul](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) monitoraggio dell'utilizzo e dei costi stimati.
 
## <a name="best-practice-optimize-storage"></a>Procedura consigliata: Ottimizzare le risorse di archiviazione

Se sono già state applicate le procedure consigliate per la selezione delle risorse di archiviazione prima della migrazione, è probabile che si stiano già sfruttando alcuni vantaggi. Tuttavia, è possibile che siano presenti costi di archiviazione aggiuntivi che si possono comunque ottimizzare. Nel corso del tempo, BLOB e file diventano non aggiornati. È possibile che i dati non siano più in uso ma che i requisiti normativi richiedano di conservarli per un certo periodo. Di conseguenza, si potrebbe evitare di archiviare i dati nella risorsa di archiviazione a prestazioni elevate usata per la migrazione originale.

Identificare e spostare i dati non aggiornati in aree di archiviazione più economiche può avere un impatto notevole sul budget di archiviazione mensile e comportare un risparmio sui costi. Azure offre molti modi per identificare e quindi archiviare tali dati non aggiornati.

- È possibile sfruttare i livelli di accesso per l'archiviazione per utilizzo generico v2, spostando i dati meno importanti dal livello di archiviazione ad accesso frequente ai livelli ad accesso sporadico e di archiviazione.
- Usare StorSimple per spostare i dati non aggiornati in base a criteri personalizzati. 

**Altre informazioni:**
- [Altre informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sui livelli di accesso.
- [Panoramica](https://docs.microsoft.com/azure/azure-monitor/overview) di StorSimple e [Prezzi di StorSimple](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Procedura consigliata: Automatizzare l'ottimizzazione della macchina virtuale

L'obiettivo principale dell'esecuzione di una macchina virtuale nel cloud è quello di ottimizzare CPU, memoria e il disco usati. Se si individuano macchine virtuali che non sono ottimizzate o che presentano frequenti periodi di inutilizzo, è consigliabile arrestarle o ridimensionarle usando i set di scalabilità per le macchine virtuali.

È possibile ottimizzare una VM con Automazione di Azure, i set di scalabilità per le macchine virtuali, l'arresto automatico e soluzioni di terze parti o tramite script. 

**Altre informazioni:**

- [Informazioni su come](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) usare la scalabilità verticale automatica.
- [Pianificazione](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) di un avvio automatico della macchina virtuale.
- [Informazioni su come](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) avviare o arrestare le macchine virtuali nelle ore di inattività in Automazione di Azure.
- [Ottenere altre informazioni] su [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)e il [toolkit di ottimizzazione delle risorse di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Procedure consigliate: Usare le App per la logica e i runbook con l'API dei budget

Azure offre un'API REST che può accedere alle informazioni di fatturazione del tenant.

- È possibile usare l'API dei budget per integrare i sistemi e flussi di lavoro esterni che vengono attivati da metriche compilate dai dati dell'API.
- È possibile spostare forzatamente i dati di utilizzo e delle risorse negli strumenti di analisi dei dati scelti.
- Le API di utilizzo delle risorse di Azure e RateCard possono aiutare a prevedere e gestire i costi con precisione.
- Le API vengono implementate come provider di risorse e incluse nella famiglia di API esposte da Azure Resource Manager.
- L'API dei budget può essere integrata con le App per la logica di Azure e i runbook.

**Altre informazioni:**

- [Altre informazioni](https://docs.microsoft.com/rest/api/consumption/budgets) sull'API dei budget.
- [Informazioni dettagliate](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) sull'utilizzo di Azure con l'API di fatturazione.


## <a name="best-practice-implement-serverless-technologies"></a>Procedura consigliata: Implementare le tecnologie serverless

I carichi di lavoro delle macchine virtuali vengono spesso migrati nel loro stato invariato per evitare i tempi di inattività. Spesso le macchine virtuali possono ospitare attività intermittenti che richiedono un breve periodo per l'esecuzione o in alternativa diverse ore. Ad esempio, nel caso di macchine virtuali che eseguono attività pianificate, come l'Utilità di pianificazione di Windows o gli script di PowerShell. Quando queste attività non sono in esecuzione, si assorbono comunque i costi di archiviazione del disco e della macchina virtuale.

Dopo la migrazione e a seguito di una revisione completa di questi tipi di attività può essere consigliabile migrarli a tecnologie serverless, ad esempio i processi di Funzioni di Azure o di Azure Batch. Grazie a questa soluzione non è più necessario gestire e mantenere le macchine virtuali, ottenendo così un risparmio aggiuntivo sui costi. 

**Altre informazioni:**
- [Informazioni su](https://azure.microsoft.com/services/functions/) Funzioni di Azure
- [Altre informazioni su](https://azure.microsoft.com/en-us/services/batch/) Azure Batch
  
## <a name="next-steps"></a>Passaggi successivi 

Rivedere altre procedure consigliate:

- [Procedure consigliate](migrate-best-practices-security-management.md) per la sicurezza e la gestione dopo la migrazione.
- [Procedure consigliate](migrate-best-practices-networking.md) per le risorse di rete dopo la migrazione.

