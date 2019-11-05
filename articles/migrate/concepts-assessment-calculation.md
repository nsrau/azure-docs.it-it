---
title: Calcoli delle valutazioni in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: hamusa
ms.openlocfilehash: d72e5a6dea8b411b6214e7749b8993f9f5a6e7a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466912"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Calcoli di valutazione in Azure Migrate

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali. Consente anche di tenere traccia delle istanze di cloud privato e pubblico in Azure. L'hub offre strumenti Azure Migrate per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti.

Server assessment è uno strumento di Azure Migrate che consente di valutare i server locali per la migrazione ad Azure. Questo articolo fornisce informazioni sulle modalità di calcolo delle valutazioni.

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Specifica la località di Azure in cui si vuole eseguire la migrazione.<br/><br/>Server Assessment supporta attualmente le aree di destinazione: Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, East Uniti, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
**Tipo di archiviazione** | Specifica il tipo di dischi che si vuole usare per l'archiviazione in Azure. <br/><br/> Per il dimensionamento locale è possibile specificare il tipo di disco di archiviazione di destinazione come gestito da Premium, gestito da SDD Standard o gestito da HDD Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di archiviazione di destinazione come automatico, gestito da Premium, gestito da HDD Standard o gestito da SDD Standard. Quando si specifica il tipo di archiviazione come automatico, l'indicazione relativa al disco si basa sui dati sulle prestazioni dei dischi: le operazioni di input/output al secondo (IOPS) e la velocità effettiva. <br/><br/>Se si specifica il tipo di archiviazione come Premium o standard, la valutazione indicherà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola del 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti Premium. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Si noti che Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze riservate (RIs)** | Questa proprietà consente di specificare [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. Le stime dei costi nella valutazione quindi prendono in considerazione gli sconti per il RI. I servizi di installazione remota sono attualmente supportati solo per le offerte con pagamento in base al consumo in Azure Migrate.
**Criteri di ridimensionamento** | Imposta i criteri da usare per la *corretta dimensione* delle macchine virtuali per Azure. È possibile optare per il dimensionamento in *base alle prestazioni* o per le dimensioni delle macchine virtuali *in locale* senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Imposta la durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando i criteri di ridimensionamento sono *basati sulle prestazioni*.
**Utilizzo percentile** | Specifica il valore percentile del set di campionamento delle prestazioni da considerare per il corretto dimensionamento. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Serie macchina virtuale** | Consente di specificare la serie di VM che si vuole considerare per il corretto dimensionamento. Se, ad esempio, si dispone di un ambiente di produzione che non si prevede di migrare a macchine virtuali di serie a in Azure, è possibile escludere una serie A dall'elenco o dalla serie e il corretto dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | Visualizza l' [offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Azure Migrate stima il costo di conseguenza.
**Valuta** | Mostra la valuta di fatturazione per l'account.
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se le macchine virtuali non eseguono 24 ore al giorno, 7 giorni alla settimana in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) per cui verranno eseguite e le stime dei costi vengono gestite di conseguenza. Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. L'impostazione predefinita è sì.

## <a name="how-are-assessments-calculated"></a>Come vengono calcolate le valutazioni?

Una valutazione in Azure Migrate server assessment viene calcolata usando i metadati raccolti sui server locali. Se l'origine di individuazione è un'importazione utilizzando un oggetto. File CSV, la valutazione viene calcolata utilizzando i metadati forniti dall'utente sui server. Il calcolo della valutazione viene gestito in tre fasi. Per ogni server, il calcolo della valutazione inizia con un'analisi di idoneità di Azure, seguita dal ridimensionamento e infine dalla stima dei costi mensili. Un server si sposta in una fase successiva solo se passa quello precedente. Se ad esempio un server non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e il ridimensionamento e i costi non vengono eseguiti per tale server.

## <a name="azure-suitability-analysis"></a>Analisi di idoneità di Azure

Non tutti i computer sono idonei per l'esecuzione in Azure. Server Assessment valuta ogni computer locale per la relativa idoneità per la migrazione di Azure. Assegna anche ogni computer valutato a una delle categorie di idoneità seguenti:
- **Pronto per Azure**: il computer può essere migrato così com'è in Azure senza alcuna modifica. Verrà avviato in Azure con il supporto completo di Azure.
- Idoneo **per Azure in modo condizionale**: il computer potrebbe iniziare in Azure, ma potrebbe non avere il supporto completo di Azure. Ad esempio, un computer in cui è in esecuzione una versione precedente di Windows Server non è supportato in Azure. È necessario prestare attenzione prima di eseguire la migrazione di questi computer in Azure e seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di conformità.
- **Non pronto per Azure**: la macchina virtuale non viene avviata in Azure. Ad esempio, se un computer locale ha un disco con più di 64 terabyte (TB) collegato, non può essere ospitato in Azure. È necessario seguire le indicazioni di correzione suggerite nella valutazione per risolvere il problema di conformità prima di eseguire la migrazione della macchina virtuale in Azure. Il calcolo della dimensione e dei costi non viene eseguito per i computer contrassegnati come non idonei per Azure.
- **Conformità sconosciuta**: Azure migrate non è stato in grado di determinare la conformità del computer a causa di metadati insufficienti raccolti dall'ambiente locale.

Server Assessment esamina le proprietà del computer e il sistema operativo guest per determinare la conformità di Azure del computer locale.

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment esamina le seguenti proprietà della macchina virtuale locale per determinare se può essere eseguita in Azure.

**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con un tipo di avvio BIOS e non UEFI. | Pronto in modo condizionale se il tipo di avvio è UEFI.
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core (128) supportati per una macchina virtuale di Azure.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> Se non è presente alcuna cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | Le dimensioni della memoria del computer devono essere uguali o inferiori alla memoria massima (3892 gigabyte [GB] nella serie M di Azure Standard_M128m&nbsp;<sup>2</sup>) consentite per una macchina virtuale di Azure. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> Se non è presente alcuna cronologia, viene usata la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | La dimensione allocata di un disco deve essere 32 TB o inferiore. Anche se Azure supporta dischi da 64 TB con Ultra SSD dischi, Azure Migrate: la valutazione del server verifica attualmente la presenza di 32 TB come limiti delle dimensioni del disco, poiché non supporta ancora Ultra SSD. <br/><br/> Il numero di dischi collegati al computer deve essere 65 o inferiore, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
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

## <a name="sizing"></a>Ridimensionamento

Dopo che un computer è stato contrassegnato come pronto per Azure, la valutazione del server apporta consigli sul dimensionamento, che implicano l'identificazione della VM di Azure e dello SKU del disco appropriati per la macchina virtuale locale. Queste raccomandazioni variano a seconda delle proprietà di valutazione specificate.

- Se la valutazione usa il *dimensionamento in base alle prestazioni*, Azure migrate considera la cronologia delle prestazioni del computer per identificare le dimensioni della VM e il tipo di disco in Azure. Nel caso dei server con l'origine di individuazione come importazione, vengono considerati i valori di utilizzo delle prestazioni specificati dall'utente. Questo metodo è particolarmente utile se la macchina virtuale locale è stata eccessivamente allocata, ma l'utilizzo è basso e si vuole ridimensionare la macchina virtuale in Azure per ridurre i costi. Questo metodo consente di ottimizzare le dimensioni durante la migrazione.
- Se non si vogliono considerare i dati sulle prestazioni per le dimensioni delle macchine virtuali e si vuole portare i computer locali così come sono in Azure, è possibile impostare i criteri di ridimensionamento *su come in locale*. Quindi, server Assessment ridimensiona le macchine virtuali in base alla configurazione locale senza considerare i dati di utilizzo. In questo caso, le attività di ridimensionamento dei dischi sono basate sul tipo di archiviazione specificato nelle proprietà di valutazione (HDD Standard, SDD Standard o dischi Premium).

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per il dimensionamento basato sulle prestazioni, la valutazione del server inizia con i dischi collegati alla macchina virtuale, seguiti dalle schede di rete. Esegue quindi il mapping di uno SKU di VM di Azure in base ai requisiti di calcolo della VM locale. Il dispositivo Azure Migrate profilare l'ambiente locale per raccogliere i dati sulle prestazioni per CPU, memoria, dischi e scheda di rete.

**Passaggi per la raccolta dei dati sulle prestazioni:**

1. Per le macchine virtuali VMware, l'appliance Azure Migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi. Per le VM Hyper-V, il punto di esempio in tempo reale viene raccolto a ogni intervallo di 30 secondi. Per i server fisici, il punto di esempio in tempo reale viene raccolto a ogni intervallo di 5 minuti. 
2. Il dispositivo esegue il rollup dei punti di esempio raccolti ogni 10 minuti e invia il valore massimo per gli ultimi 10 minuti alla valutazione del server. 
3. Server Assessment archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese. Quindi, a seconda delle proprietà di valutazione specificate per la *cronologia delle prestazioni* e l' *utilizzo percentile*, identifica il punto dati appropriato da usare per il dimensionamento corretto. Se ad esempio la cronologia delle prestazioni è impostata su 1 giorno e l'utilizzo percentile è il 95 ° percentile, server Assessment usa i punti di esempio di 10 minuti per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore del 95 ° percentile per il corretto dimensionamento. 
4. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, IOPS del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura) e velocità effettiva della rete (in uscita) il dispositivo raccoglie.

Una volta determinato il valore di utilizzo effettivo, le dimensioni di archiviazione, rete e calcolo vengono gestite come indicato di seguito.

> [!NOTE]
> Per i server aggiunti tramite l'importazione, i dati sulle prestazioni forniti dall'utente vengono utilizzati direttamente per il dimensionamento corretto.

**Ridimensionamento dell'archiviazione**: Azure migrate tenta di eseguire il mapping di ogni disco collegato al computer a un disco in Azure.

> [!NOTE]
> Azure Migrate server Assessment supporta solo dischi gestiti per la valutazione.

  - Server Assessment aggiunge le operazioni di i/o in lettura e scrittura di un disco per ottenere il totale di IOPS necessari. Analogamente, vengono aggiunti i valori di velocità effettiva di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco.
  - Se il tipo di archiviazione è stato specificato come automatico, in base ai valori di IOPS e velocità effettiva validi, server Assessment determina se è necessario eseguire il mapping del disco a un disco rigido standard, un'unità SSD standard o un disco Premium in Azure. Se il tipo di archiviazione è impostato su HDD Standard/SSD/Premium, server Assessment tenta di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato (dischi HDD Standard/SSD/Premium).
  - Se Server Assessment non riesce a trovare un disco con i IOPS e la velocità effettiva necessari, contrassegna il computer come non idoneo per Azure.
  - Se Server Assessment rileva un set di dischi appropriati, seleziona i dischi che supportano il percorso specificato nelle impostazioni di valutazione.
  - Se sono presenti più dischi idonei, la valutazione del server seleziona il disco con il costo più basso.
  - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, vengono usati i dati di configurazione del disco (dimensioni del disco) per trovare un disco SSD standard in Azure.

**Dimensionamento della rete**: server Assessment tenta di trovare una macchina virtuale di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
- Per ottenere le prestazioni di rete effettive della VM locale, server Assessment aggrega i dati trasmessi al secondo (MBps) dal computer (rete in uscita), in tutte le schede di rete e applica il fattore di comfort. Usa questo numero per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
- Oltre alle prestazioni di rete, valutazione server considera anche se la VM di Azure può supportare il numero di schede di rete necessario.
- Se non sono disponibili dati sulle prestazioni di rete, valutazione server considera solo il numero di schede di rete per il dimensionamento delle macchine virtuali.

> [!NOTE]
> La specifica del numero di schede di rete non è attualmente supportata per i server importati

**Dimensionamento del calcolo**: dopo aver calcolato i requisiti di archiviazione e di rete, valutazione server considera i requisiti di CPU e memoria per trovare una dimensione di macchina virtuale adatta in Azure.
- Azure Migrate esamina i core e la memoria usati in modo efficace per trovare le dimensioni appropriate per la macchina virtuale in Azure.
- Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
- Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Viene quindi applicato il percorso e le impostazioni del piano tariffario per la raccomandazione finale sulle dimensioni della macchina virtuale.
- Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale

Se si usa *come dimensionamento locale*, server Assessment non considera la cronologia delle prestazioni delle VM e dei dischi. Al contrario, alloca uno SKU di VM in Azure in base alle dimensioni allocate in locale. Analogamente al dimensionamento del disco, la valutazione del server esamina il tipo di archiviazione specificato nelle proprietà di valutazione (HDD Standard/SSD/Premium) e consiglia di conseguenza il tipo di disco. Il tipo di archiviazione predefinito è dischi Premium.

## <a name="confidence-ratings"></a>Classificazioni di confidenza
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

### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:

- L'ambiente non è stato profilato per la durata di creazione della valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- Durante il periodo di calcolo della valutazione sono state arrestate alcune macchine virtuali. Se una macchina virtuale viene spenta per un periodo di tempo specifico, server Assessment non può raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo di calcolo della valutazione. Ad esempio, se è stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create nell'ambiente solo una settimana fa, la cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare la valutazione al dimensionamento locale.

## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Una volta completate le indicazioni sul dimensionamento, Azure Migrate calcola i costi di calcolo e di archiviazione per dopo la migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale.
    - Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta.
    - Aggrega il costo in tutti i computer per calcolare il costo totale di calcolo mensile.
- **Costo di archiviazione**: il costo mensile di archiviazione per un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer, come indicato di seguito:
    - Server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer.
    - Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.


## <a name="next-steps"></a>Passaggi successivi

Creare una valutazione per le macchine virtuali [VMware](tutorial-assess-vmware.md) o [Hyper-V](tutorial-assess-hyper-v.md).
