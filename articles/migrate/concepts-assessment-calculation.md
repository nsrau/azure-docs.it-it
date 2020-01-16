---
title: Valutazioni in Azure Migrate
description: Informazioni sulle valutazioni in Azure Migrate.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a8912263432bc0e9cd7172c4b6c9b118132863d3
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029110"
---
# <a name="about-assessments-in-azure-migrate"></a>Informazioni sulle valutazioni in Azure Migrate

Questo articolo descrive il modo in cui vengono calcolate le valutazioni in [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Le valutazioni vengono eseguite su gruppi di computer locali, per stabilire se sono pronte per la migrazione a Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Ricerca per categorie eseguire una valutazione?
È possibile eseguire una valutazione usando Azure Migrate: server Assessment o un altro strumento di Azure o di terze parti. Dopo aver creato un progetto di Azure Migrate, è necessario aggiungere lo strumento desiderato. [Altre informazioni] (how-to-add-tool-first-time.md

### <a name="collect-compute-data"></a>Raccogli dati di calcolo

I dati sulle prestazioni per le impostazioni di calcolo vengono raccolti come segue:

1. Il [dispositivo Azure migrate](migrate-appliance.md) raccoglie un punto di esempio in tempo reale:

    - **VM VMware**: per le macchine virtuali VMware, l'appliance Azure migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi.
    - **Macchine virtuali Hyper-v**: per le macchine virtuali Hyper-v, il punto di esempio in tempo reale viene raccolto a ogni intervallo di 30 secondi.
    - **Server fisici**: per i server fisici, il punto di esempio in tempo reale viene raccolto a ogni intervallo di cinque minuti. 
    
2. Il dispositivo esegue il rollup dei punti di esempio (20 secondi, 30 secondi, cinque minuti) per creare un singolo punto dati ogni 10 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore massimo da tutti gli esempi e quindi lo invia ad Azure.
3. Server Assessment archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese.
4. Quando si crea una valutazione, server Assessment identifica il punto dati appropriato da usare per il dimensionamento corretto, in base ai valori percentile per la *cronologia delle prestazioni* e l' *utilizzo percentile*.

    - Se, ad esempio, la cronologia delle prestazioni è impostata su una settimana e l'utilizzo percentile è il 95 ° percentile, server Assessment Ordina i punti di campionamento di 10 minuti per l'ultima settimana in ordine crescente e sceglie il valore 95 ° percentile per il corretto dimensionamento. 
    - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se si sceglie il 99 ° percentile.
    - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, è necessario selezionare il 99 ° percentile per l'utilizzo percentile.

5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, IOPS del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura) e velocità effettiva della rete (in uscita) il dispositivo raccoglie.

Per eseguire valutazioni in server Assessment, prepararsi per la valutazione in locale e in Azure e configurare l'appliance Azure Migrate per individuare continuamente i computer locali. Dopo aver individuato i computer, è possibile raccoglierli in gruppi per valutarli. Per valutazioni più dettagliate e affidabili, è possibile visualizzare ed eseguire il mapping delle dipendenze tra i computer, per scoprire come eseguirne la migrazione.

- Informazioni sull'esecuzione di valutazioni per le [macchine virtuali VMware](tutorial-prepare-vmware.md), le [VM Hyper-V](tutorial-prepare-hyper-v.md)e i [server fisici](tutorial-prepare-physical.md).
- Informazioni sulla valutazione dei server [importati con un file CSV](tutorial-assess-import.md).
- Informazioni sulla configurazione della [visualizzazione delle dipendenze](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Valutazioni in server Assessment 

Le valutazioni create con Azure Migrate server assessment sono uno snapshot temporizzato dei dati. Lo strumento Server Assessment fornisce due tipi di valutazione.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni basate sui dati sulle prestazioni raccolti | Le indicazioni sulle dimensioni della macchina virtuale sono basate sui dati di utilizzo della CPU e della memoria.<br/><br/> L'indicazione del tipo di disco (disco rigido/SSD standard o dischi gestiti Premium) si basa sui IOPS e la velocità effettiva dei dischi locali.
**Così come sono in locale** | Valutazioni che non utilizzano dati sulle prestazioni per apportare raccomandazioni. | Le indicazioni sulle dimensioni della macchina virtuale sono basate sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato è basato sul tipo di archiviazione selezionato per la valutazione.

## <a name="collecting-performance-data"></a>Raccolta dei dati sulle prestazioni

I dati sulle prestazioni vengono raccolti come segue:

1. Il [dispositivo Azure migrate](migrate-appliance.md) raccoglie un punto di esempio in tempo reale:

    - **VM VMware**: per le macchine virtuali VMware, l'appliance Azure migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi.
    - **Macchine virtuali Hyper-v**: per le macchine virtuali Hyper-v, il punto di esempio in tempo reale viene raccolto a ogni intervallo di 30 secondi.
    - **Server fisici**: per i server fisici, il punto di esempio in tempo reale viene raccolto a ogni intervallo di cinque minuti. 
    
2. Il dispositivo esegue il rollup dei punti di esempio (20 secondi, 30 secondi, cinque minuti) per creare un singolo punto dati ogni 10 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore massimo da tutti gli esempi e quindi lo invia ad Azure.
3. Server Assessment archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese.
4. Quando si crea una valutazione, server Assessment identifica il punto dati appropriato da usare per il dimensionamento corretto, in base ai valori percentile per la *cronologia delle prestazioni* e l' *utilizzo percentile*.

    - Se, ad esempio, la cronologia delle prestazioni è impostata su una settimana e l'utilizzo percentile è il 95 ° percentile, server Assessment Ordina i punti di campionamento di 10 minuti per l'ultima settimana in ordine crescente e sceglie il valore 95 ° percentile per il corretto dimensionamento. 
    - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se si sceglie il 99 ° percentile.
    - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, è necessario selezionare il 99 ° percentile per l'utilizzo percentile.

5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, IOPS del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura) e velocità effettiva della rete (in uscita) il dispositivo raccoglie.
## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Di seguito è riportato il contenuto di una valutazione in Azure Migrate: server assessment.

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Percorso in cui si desidera eseguire la migrazione. Server Assessment supporta attualmente le aree di Azure di destinazione seguenti:<br/><br/> Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, East Uniti, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Nord Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
*Disco di archiviazione di destinazione (ridimensionamento)* * | Tipo di dischi da usare per l'archiviazione in Azure. <br/><br/> Specificare il disco di archiviazione di destinazione come gestito Premium, gestito da SSD standard o gestito da HDD standard.
**Disco di archiviazione di destinazione (dimensionamento basato sulle prestazioni)** | Specificare il tipo di disco di archiviazione di destinazione come automatico, gestito Premium, gestito da HDD standard o gestito da SSD standard.<br/><br/> **Automatico**: l'indicazione del disco è basata sui dati sulle prestazioni dei dischi (operazioni di input/output al secondo (IOPS) e velocità effettiva).<br/><br/>**Premium/standard**: la valutazione consiglia uno SKU del disco all'interno del tipo di archiviazione selezionato.<br/><br/> Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola pari al 99,9%, considerando l'uso di Managed disks Premium. In questo modo si garantisce che tutti i dischi della valutazione siano consigliati come dischi gestiti Premium.<br/><br/> Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze riservate (RIs)** | Specificare le [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure, in modo che le stime dei costi nella valutazione tengano conto degli sconti.<br/><br/> I servizi di installazione remota sono attualmente supportati solo per le offerte con pagamento in base al consumo in Azure Migrate.
**Criteri di ridimensionamento** | Usato per dimensionare correttamente la VM in Azure.<br/><br/> Usare il ridimensionamento così come o il dimensionamento in base alle prestazioni.
**Cronologia delle prestazioni** | Utilizzato con il dimensionamento basato sulle prestazioni. Consente di specificare la durata della valutazione dei dati sulle prestazioni.
**Utilizzo percentile** | Utilizzato con il dimensionamento basato sulle prestazioni. Specifica il valore percentile dell'esempio di prestazioni da utilizzare per il dimensionamento corretto. 
**Serie macchina virtuale** | Specificare la serie di VM di Azure che si vuole considerare per il corretto dimensionamento. Se, ad esempio, non si dispone di un ambiente di produzione che richiede macchine virtuali serie A in Azure, è possibile escludere una serie A dall'elenco o dalla serie.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. Applicato in base ai dati di utilizzo del computer per le macchine virtuali (CPU, memoria, disco e rete). Viene considerato un problema, ad esempio l'utilizzo stagionale, una breve cronologia delle prestazioni e probabilmente aumenti nell'utilizzo futuro.<br/><br/> Ad esempio, una macchina virtuale con 10 core con utilizzo del 20% normalmente produce una macchina virtuale a due core. Con un fattore di comfort di 2.0 x, il risultato è invece una macchina virtuale a quattro core.
**Offerta** | Visualizza l' [offerta Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Server Assessment stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione per l'account.
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se le macchine virtuali di Azure non vengono eseguite 24 ore al giorno, 7 giorni alla settimana, è possibile specificare la durata (giorni al mese e ore al giorno) che verranno eseguite. Le stime dei costi vengono gestite di conseguenza.<br/><br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se è impostato su Sì (impostazione predefinita), per le macchine virtuali Windows vengono considerati i prezzi non Windows Azure.

[Esaminare le procedure consigliate per la creazione della](best-practices-assessment.md) valutazione con server assessment.

## <a name="how-are-assessments-calculated"></a>Come vengono calcolate le valutazioni? 

Valutazioni in Azure Migrate: la valutazione del server viene calcolata usando i metadati raccolti sui computer locali. Se si esegue una valutazione nei computer importati con un. Il file CSV fornisce i metadati per il calcolo. I calcoli si verificano in tre fasi:

1. **Calcolo della conformità di Azure**: valutare se i computer sono idonei per la migrazione ad Azure.
2. **Calcolare le indicazioni sul dimensionamento**: stima delle dimensioni di calcolo, archiviazione e rete. 
2. **Calcolare i costi mensili**: calcolare i costi di calcolo e archiviazione mensili stimati per l'esecuzione delle macchine in Azure dopo la migrazione.

I calcoli sono ordinati e un server del computer si sposta in una fase successiva solo se passa a quello precedente. Se, ad esempio, un server ha esito negativo per Azure, viene contrassegnato come non idoneo per Azure e il dimensionamento e i costi non vengono eseguiti per tale server.



## <a name="calculate-readiness"></a>Calcola conformità

Non tutti i computer sono idonei per l'esecuzione in Azure. Server Assessment valuta ogni computer locale e le assegna una categoria di conformità. 
- **Pronto per Azure**: il computer può essere migrato così com'è in Azure senza alcuna modifica. Verrà avviato in Azure con il supporto completo di Azure.
- Idoneo **per Azure in modo condizionale**: il computer potrebbe iniziare in Azure, ma potrebbe non avere il supporto completo di Azure. Ad esempio, un computer in cui è in esecuzione una versione precedente di Windows Server non è supportato in Azure. Prima di eseguire la migrazione di questi computer ad Azure, è necessario prestare attenzione. Seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di conformità.
- **Non pronto per Azure**: il computer non verrà avviato in Azure. Se, ad esempio, un disco del computer locale è superiore a 64-TBs, non può essere ospitato in Azure. Seguire le indicazioni di correzione per risolvere il problema prima della migrazione. 
- **Conformità sconosciuta**: Azure migrate non è stato in grado di determinare la conformità di un computer a causa di metadati insufficienti.

Per calcolare la conformità, server Assessment esamina le proprietà del computer e le impostazioni del sistema operativo riepilogate nelle tabelle seguenti. 

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment esamina le seguenti proprietà della macchina virtuale locale per determinare se può essere eseguita in Azure.

**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con un tipo di avvio BIOS e non UEFI. | Pronto in modo condizionale se il tipo di avvio è UEFI.
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core (128) supportati per una macchina virtuale di Azure.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> Se non è presente alcuna cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | Le dimensioni della memoria del computer devono essere uguali o inferiori alla memoria massima (3892 gigabyte [GB] nella serie M di Azure Standard_M128m&nbsp;<sup>2</sup>) consentite per una macchina virtuale di Azure. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> Se non è presente alcuna cronologia, viene usata la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | La dimensione allocata di un disco deve essere 32 TB o inferiore. Anche se Azure supporta dischi da 64 TB con Ultra SSD dischi, Azure Migrate: la valutazione del server attualmente verifica la presenza di 32 TB come limiti di dimensioni del disco, poiché non supporta ancora Ultra SSD. <br/><br/> Il numero di dischi collegati al computer deve essere 65 o inferiore, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
**Rete** | Un computer deve avere 32 o un minor numero di interfacce di rete (NIC) collegati. | Idoneo se rientra nei limiti.

### <a name="guest-operating-system"></a>Sistema operativo guest
Insieme alle proprietà della macchina virtuale, valutazione server esamina il sistema operativo guest dei computer per determinare se può essere eseguito in Azure.

> [!NOTE]
> Per le macchine virtuali VMware, server Assessment usa il sistema operativo specificato per la macchina virtuale in server vCenter per gestire l'analisi del sistema operativo guest. Per le macchine virtuali Linux in esecuzione su VMware, attualmente non identifica la versione del kernel esatta del sistema operativo guest.

La logica seguente viene usata da server assessment per identificare la conformità di Azure in base al sistema operativo.

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Idoneo per Azure
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2003, 2003 R2 | Questi sistemi operativi hanno superato la data di fine del supporto e necessitano di un [contratto di supporto personalizzato (CSA)](https://aka.ms/WSosstatement) per il supporto in Azure. | Pronto per Azure in modo condizionale. Provare ad aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Questi sistemi operativi hanno superato la data di fine del supporto. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Si consiglia di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Client Windows 7, 8 e 10 | Azure offre supporto solo con [la sottoscrizione di Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Idoneo per Azure con condizioni
Windows 10 Pro Desktop | Azure fornisce supporto con [i diritti di hosting multi-tenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Idoneo per Azure con condizioni
Windows Vista, XP Professional | Questi sistemi operativi hanno superato la data di fine del supporto. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Si consiglia di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Linux | Azure approva questi [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md). Altri sistemi operativi Linux potrebbero avviarsi in Azure, ma è consigliabile aggiornare il sistema operativo a una versione approvata prima di eseguire la migrazione ad Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Idoneo per Azure con condizioni se la versione non è approvata.
Altri sistemi operativi<br/><br/> Ad esempio Oracle Solaris, Apple Mac OS, FreeBSD e così via. | Azure non approva questi sistemi operativi. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Prima di eseguire la migrazione ad Azure, è consigliabile installare un sistema operativo supportato.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure.
Sistemi operativi a 32 bit | Il computer potrebbe essere avviato in Azure, ma Azure potrebbe non fornire supporto completo. | Pronto per Azure in modo condizionale. Provare ad aggiornare il sistema operativo del computer da un sistema operativo a 32 bit a un sistema operativo a 64 bit prima di eseguire la migrazione ad Azure.

## <a name="calculate-sizing-as-is-on-premises"></a>Calcolo delle dimensioni (così come sono in locale)

Dopo che la macchina virtuale è stata contrassegnata come pronta per Azure, il dimensionamento del server consente di identificare lo SKU del disco e della VM di Azure. Se si usa il dimensionamento in locale, server Assessment non considera la cronologia delle prestazioni delle VM e dei dischi.

**Dimensionamento del calcolo**: ALLOCA uno SKU di VM di Azure in base alle dimensioni allocate in locale.
**Dimensioni di archiviazione/disco**: la valutazione del server esamina il tipo di archiviazione specificato in proprietà valutazione (HDD standard/SSD/Premium) e consiglia di conseguenza il tipo di disco. Il tipo di archiviazione predefinito è dischi Premium.
**Dimensionamento della rete**: server Assessment considera la scheda di rete nel computer locale.


## <a name="calculate-sizing-performance-based"></a>Calcolare il dimensionamento (basato sulle prestazioni)

Dopo che un computer è stato contrassegnato come pronto per Azure, se si usa il dimensionamento in base alle prestazioni, la valutazione del server consente di apportare indicazioni sul dimensionamento come segue:

- Server Assessment considera la cronologia delle prestazioni del computer per identificare le dimensioni della macchina virtuale e il tipo di disco in Azure.
- Se i server sono stati importati utilizzando un file CSV, verranno utilizzati i valori specificati. Questo metodo è particolarmente utile se il computer locale è stato allocato in modo eccessivo, l'utilizzo è effettivamente basso e si desidera dimensionare correttamente la VM in Azure per ridurre i costi. 
- Se non si vogliono usare i dati sulle prestazioni, reimpostare i criteri di ridimensionamento su così come sono in locale, come descritto nella sezione precedente.

### <a name="calculate-storage-sizing"></a>Calcolare il dimensionamento dell'archiviazione

Per il ridimensionamento dello spazio di archiviazione, Azure Migrate prova a eseguire il mapping di ogni disco collegato al computer a un disco in Azure e funziona nel modo seguente:

1. Server Assessment aggiunge le operazioni di i/o in lettura e scrittura di un disco per ottenere il totale di IOPS necessari. Analogamente, vengono aggiunti i valori di velocità effettiva di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco.
2. Se il tipo di archiviazione è stato specificato come automatico, in base ai valori di IOPS e velocità effettiva validi, server Assessment determina se il disco deve essere mappato a un disco rigido standard, un'unità SSD standard o un disco Premium in Azure. Se il tipo di archiviazione è impostato su HDD Standard/SSD/Premium, server Assessment tenta di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato (dischi HDD Standard/SSD/Premium).
3. I dischi sono selezionati come segue:
    - Se Server Assessment non riesce a trovare un disco con i IOPS e la velocità effettiva necessari, contrassegna il computer come non idoneo per Azure.
    - Se Server Assessment rileva un set di dischi appropriati, seleziona i dischi che supportano il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, la valutazione del server seleziona il disco con il costo più basso.
    - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, vengono usati i dati di configurazione del disco (dimensioni del disco) per trovare un disco SSD standard in Azure.

### <a name="calculate-network-sizing"></a>Calcolare il dimensionamento della rete

Server Assessment tenta di trovare una macchina virtuale di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
- Per ottenere le prestazioni di rete effettive della VM locale, server Assessment aggrega i dati trasmessi al secondo (MBps) dal computer (rete in uscita), in tutte le schede di rete e applica il fattore di comfort. Usa questo numero per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
- Oltre alle prestazioni di rete, valutazione server considera anche se la VM di Azure può supportare il numero di schede di rete necessario.
- Se non sono disponibili dati sulle prestazioni di rete, valutazione server considera solo il numero di schede di rete per il dimensionamento delle macchine virtuali.


### <a name="calculate-compute-sizing"></a>Calcolare il dimensionamento del calcolo

Dopo aver calcolato i requisiti di archiviazione e di rete, valutazione server considera i requisiti di CPU e memoria per trovare una dimensione di VM adatta in Azure.
- Azure Migrate esamina i core e la memoria usati in modo efficace per trovare le dimensioni appropriate per la macchina virtuale in Azure.
- Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
- Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Viene quindi applicato il percorso e le impostazioni del piano tariffario per la raccomandazione finale sulle dimensioni della macchina virtuale.
- Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.


### <a name="calculate-confidence-ratings"></a>Calcolo delle classificazioni di confidenza

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità che varia da uno (più basso) a cinque stelle (più alto).
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- Le classificazioni di attendibilità non sono applicabili per *come valutazioni locali* .
- Per il dimensionamento basato sulle prestazioni, è necessario disporre di una valutazione del server:
    - Dati di utilizzo per la CPU e la memoria della macchina virtuale.
    - I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
    - I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

   Se uno di questi numeri di utilizzo non è disponibile in server vCenter, la raccomandazione di dimensioni potrebbe non essere affidabile.

A seconda della percentuale di punti dati disponibili, la classificazione di attendibilità per la valutazione viene eseguita come segue.

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0-20% | 1 stella
   21-40% | 2 stelle
   41-60% | 3 stelle
   61-80% | 4 stelle
   81-100% | 5 stelle

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate alle valutazioni dei server importati utilizzando. File CSV in Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:

- L'ambiente non è stato profilato per la durata di creazione della valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- Durante il periodo di calcolo della valutazione sono state arrestate alcune macchine virtuali. Se una macchina virtuale viene spenta per un periodo di tempo specifico, server Assessment non può raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo di calcolo della valutazione. Ad esempio, se è stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create nell'ambiente solo una settimana fa, la cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare la valutazione al dimensionamento locale.

## <a name="calculate-monthly-costs"></a>Calcola i costi mensili

Una volta completate le indicazioni sul dimensionamento, Azure Migrate calcola i costi di calcolo e di archiviazione per dopo la migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale.
    - Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta.
    - Aggrega il costo in tutti i computer per calcolare il costo totale di calcolo mensile.
- **Costo di archiviazione**: il costo mensile di archiviazione per un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer, come indicato di seguito:
    - Server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer.
    - Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Esaminare](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni. 
