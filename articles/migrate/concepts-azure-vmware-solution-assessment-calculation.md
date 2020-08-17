---
title: Calcoli di AVS assessment in Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dei calcoli di AVS assessment nel servizio Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 3469b612081f9cb96beec98a065e0827d1c04b4c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261838"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Panoramica della valutazione del server (migrazione alla soluzione VMware di Azure)

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali. Consente anche di tenere traccia delle istanze di cloud privato e pubblico in Azure. L'hub offre strumenti Azure Migrate per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti.

Server assessment è uno strumento di Azure Migrate che consente di valutare i server locali per la migrazione alle macchine virtuali IaaS di Azure e alla soluzione Azure VMware (AVS). Questo articolo fornisce informazioni sul modo in cui vengono calcolate le valutazioni di Azure VMware Solution (AVS).

> [!NOTE]
> La valutazione della soluzione VMware di Azure (AVS) è attualmente in anteprima e può essere creata solo per le macchine virtuali VMware.

## <a name="types-of-assessments"></a>Tipi di valutazioni

Le valutazioni create con server assessment sono uno snapshot temporizzato dei dati. È possibile creare due tipi di valutazioni con Azure Migrate: Valutazione server.

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md)locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md)e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione. [Altre informazioni](concepts-assessment-calculation.md)
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

La valutazione della soluzione VMware di Azure (AVS) in server Assessment offre due opzioni per i criteri di ridimensionamento:

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti delle macchine virtuali locali. | **Dimensioni consigliate del nodo**: basate sui dati di utilizzo della CPU e della memoria con il tipo di nodo, il tipo di archiviazione e l'impostazione di ITF selezionati per la valutazione.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni consigliate del nodo**: in base alle dimensioni della macchina virtuale locale con il tipo di nodo, il tipo di archiviazione e l'impostazione di ITF selezionati per la valutazione.

## <a name="how-do-i-run-an-assessment"></a>Ricerca per categorie eseguire una valutazione?

Esistono due modi per eseguire una valutazione.

- Valutare i computer usando i metadati del server raccolti da un'appliance Azure Migrate leggera. L'appliance individua i computer locali. Invia quindi i dati sulle prestazioni e i metadati del computer a Azure Migrate.
- Valutare i computer usando i metadati del server importati in un formato con valori delimitati da virgole (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Ricerca per categorie valutare con l'appliance?

Se si sta distribuendo un'appliance Azure Migrate per individuare i server locali, seguire questa procedura:

1. Configurare Azure e l'ambiente locale per lavorare con la valutazione del server.
2. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento Server assessment.
3. Distribuire un'appliance Azure Migrate leggera. L'appliance individua continuamente computer locali e invia i dati sulle prestazioni e i metadati del computer a Azure Migrate. Distribuire l'appliance come macchina virtuale. Non è necessario installare alcun elemento nei computer che si desidera valutare.

Quando l'appliance inizia l'individuazione del computer, è possibile raccogliere i computer che si vuole valutare in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **soluzione VMware di Azure (AVS)**.

Per creare la prima valutazione della soluzione VMware di Azure (AVS [), seguire questa procedura.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Ricerca per categorie valutare con i dati importati?

Se si sta valutando i server usando un file CSV, non è necessario un dispositivo. Eseguire invece i passaggi seguenti:

1. Configurare Azure per l'uso con la valutazione del server.
2. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento Server assessment.
3. Scaricare un modello CSV e aggiungervi i dati del server.
4. Importare il modello in server assessment.
5. Individuare i server aggiunti con l'importazione, raccoglierli in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **soluzione VMware di Azure (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Se si usa Azure Migrate Appliance per la valutazione, informazioni sui metadati e i dati sulle prestazioni raccolti per [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>In che modo l'appliance calcola i dati sulle prestazioni?

Se si usa il dispositivo per l'individuazione, raccoglie i dati sulle prestazioni per le impostazioni di calcolo con i passaggi seguenti:

1. L'appliance raccoglie un punto di esempio in tempo reale.

    - **VM VMware**: un punto di esempio viene raccolto ogni 20 secondi.

2. L'Appliance combina i punti di esempio per creare un singolo punto dati ogni 10 minuti. Per creare il punto dati, l'appliance seleziona i valori di picco di tutti gli esempi. Invia quindi il punto dati ad Azure.
3. Server Assessment archivia tutti i punti dati di 10 minuti per l'ultimo mese.
4. Quando si crea una valutazione, server Assessment identifica il punto dati appropriato da usare per rightsizing. L'identificazione si basa sui valori percentile per la *cronologia delle prestazioni* e l' *utilizzo percentile*.

    - Se ad esempio la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è il 95 ° percentile, server Assessment Ordina i punti di campionamento di 10 minuti per l'ultima settimana. Li ordina in ordine crescente e sceglie il valore del 95 ° percentile per rightsizing.
    - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se è stato selezionato il 99 ° percentile.
    - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, selezionare il 99 ° percentile per l'utilizzo percentile.

5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per le metriche raccolte dal dispositivo:

    - Uso della CPU
    - Utilizzo RAM
    - IOPS del disco (lettura e scrittura)
    - Velocità effettiva del disco (lettura e scrittura)
    - Velocità effettiva di rete (in e in uscita)

I dati sulle prestazioni seguenti vengono raccolti ma non usati nei consigli per il dimensionamento per le valutazioni AVS:
  - I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
  - I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

## <a name="how-are-avs-assessments-calculated"></a>Come vengono calcolate le valutazioni AVS?

Server Assessment usa i metadati e i dati sulle prestazioni dei computer locali per calcolare le valutazioni. Se si distribuisce il dispositivo Azure Migrate, la valutazione usa i dati raccolti dal dispositivo. Tuttavia, se si esegue una valutazione importata utilizzando un file CSV, è necessario fornire i metadati per il calcolo.

I calcoli si verificano in queste tre fasi:

1. **Calcolo della conformità della soluzione VMware di Azure (AVS)**: se le VM locali sono adatte per la migrazione alla soluzione VMware di Azure (AVS).
2. **Calcolare il numero di nodi AVS e l'utilizzo tra i nodi**: numero stimato di nodi AVS necessari per eseguire le VM e la CPU, la memoria e l'utilizzo dello spazio di archiviazione proiettati in tutti i nodi.
3. **Stima dei costi mensili**: i costi mensili stimati per tutti i nodi della soluzione VMware di Azure (AVS) che eseguono le macchine virtuali locali.

I calcoli sono nell'ordine precedente. Un server del computer si sposta in una fase successiva solo se passa quello precedente. Ad esempio, se un server ha esito negativo, la fase di idoneità AVS viene contrassegnata come non adatta per Azure. Il dimensionamento e i calcoli dei costi non vengono eseguiti per quel server

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Cosa c'è in una valutazione della soluzione VMware di Azure (AVS)?

Ecco cosa è incluso in una valutazione AVS in server assessment:


| **Proprietà** | **Dettagli** 
| - | - 
| **Posizione di destinazione** | Specifica la posizione del cloud privato AVS in cui si vuole eseguire la migrazione.<br/><br/> AVS assessment in server Assessment supporta attualmente le aree di destinazione seguenti: Stati Uniti orientali, Europa occidentale, Stati Uniti occidentali. 
| **Tipo di archiviazione** | Specifica il motore di archiviazione da usare in AVS.<br/><br/> Le valutazioni AVS supportano solo rete VSAN come tipo di archiviazione predefinito. 
**Istanze riservate (RIs)** | Questa proprietà consente di specificare istanze riservate in AVS. I servizi di installazione remota non sono attualmente supportati per i nodi AVS. 
**Tipo di nodo** | Specifica il [tipo di nodo AVS](../azure-vmware/concepts-private-clouds-clusters.md) usato per eseguire il mapping delle macchine virtuali locali. Il tipo di nodo predefinito è AV36. <br/><br/> Azure Migrate consiglierà un numero di nodi necessario per la migrazione delle macchine virtuali in AVS. 
**Impostazione di ITF, livello RAID** | Specifica l'errore applicabile da tollerare e le combinazioni RAID. L'opzione di ITF selezionata combinata con il requisito del disco della macchina virtuale locale determina lo spazio di archiviazione rete VSAN totale richiesto in AVS. 
**Criterio di dimensionamento** | Imposta i criteri da usare per *dimensionare correttamente* le VM per AVS. È possibile optare per il dimensionamento in *base alle prestazioni* o *come locale* senza considerare la cronologia delle prestazioni. 
**Cronologia delle prestazioni** | Imposta la durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando i criteri di ridimensionamento sono *basati sulle prestazioni*. 
**Utilizzo percentile** | Specifica il valore percentile del set di campionamento delle prestazioni da considerare per il corretto dimensionamento. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. 
**Offerta** | Visualizza l' [offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Azure Migrate stima il costo di conseguenza.
**Valuta** | Mostra la valuta di fatturazione per l'account. 
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%. 
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Anche se non ha alcun effetto sui prezzi delle soluzioni VMware di Azure a causa del prezzo basato sui nodi, i clienti possono comunque applicare le licenze del sistema operativo locali (basate su Microsoft) in AVS usando i vantaggi di Azure Hybrid. Altri fornitori di sistemi operativi software dovranno fornire le proprie condizioni di licenza, ad esempio RHEL. 
**oversubscription vCPU** | Specifica il rapporto tra il numero di core virtuali legati a un core fisico nel nodo AVS. Il valore predefinito nei calcoli è 4 vCPU: 1 core fisico in AVS. <br/><br/> Gli utenti dell'API possono impostare questo valore come intero. Si noti che vCPU oversubscription > 4:1 può influito sui carichi di lavoro a seconda dell'utilizzo della CPU. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analisi di idoneità per la soluzione VMware di Azure (AVS)

Le valutazioni AVS in server Assessment valutano ogni macchina virtuale locale per la relativa idoneità per AVS esaminando le proprietà del computer. Assegna anche ogni computer valutato a una delle categorie di idoneità seguenti:

- **Pronto per AVS**: è possibile eseguire la migrazione del computer in Azure (AVS) senza alcuna modifica. Verrà avviato in AVS con supporto AVS completo.
- **Pronto con le condizioni**: la macchina virtuale potrebbe avere problemi di compatibilità con la versione corrente di vSphere, nonché richiedere gli strumenti VMware e altre impostazioni prima che sia possibile ottenere le funzionalità complete dalla macchina virtuale in AVS.
- **Non pronto per AVS**: la macchina virtuale non viene avviata in AVS. Ad esempio, se la macchina virtuale VMware locale dispone di un dispositivo esterno collegato, ad esempio un CD-ROM, l'operazione VMware VMotion avrà esito negativo (se si usa VMware VMotion).
- **Conformità sconosciuta**: Azure migrate non è stato in grado di determinare la conformità del computer a causa di metadati insufficienti raccolti dall'ambiente locale.

Server Assessment esamina le proprietà del computer per determinare la conformità di Azure del computer locale.

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment esamina la seguente proprietà della macchina virtuale locale per determinare se può essere eseguita in una soluzione VMware di Azure (AVS).


| **Proprietà** | **Dettagli** | **Stato di conformità con AVS** 
| - | - | - 
| **Protocollo Internet** | Attualmente la soluzione Azure VMware non supporta indirizzi Internet IPv6.<br/><br/> Se vengono rilevati indirizzi IPv6 nel computer, contattare il team MSFT AVS GBB locale per ricevere indicazioni per la correzione.| Protocollo Internet predisposto in modo condizionale


### <a name="guest-operating-system"></a>Sistema operativo guest

Attualmente, le valutazioni AVS non esaminano il sistema operativo come parte dell'analisi di idoneità. È probabile che tutti i sistemi operativi in esecuzione su macchine virtuali locali vengano eseguiti in una soluzione VMware di Azure (AVS).

Insieme alle proprietà della macchina virtuale, valutazione server esamina il sistema operativo guest dei computer per determinare se può essere eseguito in Azure.


## <a name="sizing"></a>Ridimensionamento

Dopo che un computer è stato contrassegnato come pronto per AVS, AVS assessment in server Assessment apporta consigli sul dimensionamento dei nodi, che implicano l'identificazione dei requisiti di VM locali appropriati e la ricerca del numero totale di nodi AVS richiesti. Queste raccomandazioni variano a seconda delle proprietà di valutazione specificate.

- Se la valutazione usa il *dimensionamento in base alle prestazioni*, Azure migrate considera la cronologia delle prestazioni del computer per apportare le indicazioni di dimensionamento appropriate per AVS. Questo metodo è particolarmente utile se la macchina virtuale locale è stata eccessivamente allocata, ma l'utilizzo è basso e si vuole dimensionare correttamente la VM in AVS per ridurre i costi. Questo metodo consente di ottimizzare le dimensioni durante la migrazione.
- Se non si vogliono considerare i dati sulle prestazioni per il dimensionamento delle macchine virtuali e si vuole portare i computer locali così come sono in AVS, è possibile impostare i criteri di ridimensionamento *su come in locale*. Quindi, server Assessment ridimensiona le macchine virtuali in base alla configurazione locale senza considerare i dati di utilizzo. 


### <a name="ftt-sizing-parameters"></a>Parametri di ridimensionamento dell'ITF

Il motore di archiviazione usato in AVS è rete VSAN. i criteri di archiviazione rete VSAN definiscono i requisiti di archiviazione per le macchine virtuali. Questi criteri garantiscono il livello di servizio richiesto per le macchine virtuali, perché determinano la modalità di allocazione dell'archiviazione alla macchina virtuale. Le combinazioni di transazioni di ITF disponibili sono: 

**Tolleranza errori** | **Configurazione RAID** | **Numero minimo di host richiesti** | **Considerazioni sul dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (Mirroring) | 3 | Una macchina virtuale da 100 GB utilizza 200 GB.
1 | RAID-5 (Erasure Coding) | 4 | Una macchina virtuale da 100 GB utilizza 133,33 GB
2 | RAID-1 (Mirroring) | 5 | Una macchina virtuale da 100 GB utilizza 300 GB.
2 | RAID-6 (Erasure Coding) | 6 | Una macchina virtuale da 100 GB utilizza 150 GB.
3 | RAID-1 (Mirroring) | 7 | Una macchina virtuale da 100 GB utilizza 400 GB.

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per il dimensionamento basato sulle prestazioni, la valutazione del server inizia con i dischi collegati alla macchina virtuale, seguiti dalle schede di rete. Esegue quindi il mapping dei requisiti della macchina virtuale a un numero di nodi appropriato per AVS. Il dispositivo Azure Migrate profilare l'ambiente locale per raccogliere i dati sulle prestazioni per CPU, memoria, dischi e scheda di rete.

**Passaggi per la raccolta dei dati sulle prestazioni:**

1. Per le macchine virtuali VMware, l'appliance Azure Migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi. 
2. Il dispositivo esegue il rollup dei punti di esempio raccolti ogni 10 minuti e invia il valore massimo per gli ultimi 10 minuti alla valutazione del server.
3. Server Assessment archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese. Quindi, a seconda delle proprietà di valutazione specificate per la *cronologia delle prestazioni* e l' *utilizzo percentile*, identifica il punto dati appropriato da usare per il dimensionamento corretto. Se ad esempio la cronologia delle prestazioni è impostata su 1 giorno e l'utilizzo percentile è il 95 ° percentile, server Assessment usa i punti di esempio di 10 minuti per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore del 95 ° percentile per il corretto dimensionamento.
4. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, IOPS del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura) e velocità effettiva di rete (in uscita) che l'appliance raccoglie.

Una volta determinato il valore di utilizzo effettivo, le dimensioni di archiviazione, rete e calcolo vengono gestite come indicato di seguito.

**Ridimensionamento dell'archiviazione**: Azure migrate usa lo spazio su disco della macchina virtuale locale totale come parametro di calcolo per determinare i requisiti di archiviazione AVS rete VSAN, oltre all'impostazione di ITF selezionata dal cliente. ITF: gli errori da tollerare, oltre a richiedere un numero minimo di nodi per ogni opzione di ITF, determineranno l'archiviazione rete VSAN totale necessaria combinata con il requisito del disco della macchina virtuale.

**Dimensionamento della rete**: Attualmente Server Assessment non prende in considerazione le impostazioni di rete per le valutazioni della soluzione Azure VMware.

**Dimensionamento del calcolo**: dopo aver calcolato i requisiti di archiviazione, la valutazione del server considera i requisiti di CPU e memoria per determinare il numero di nodi necessari per AVS in base al tipo di nodo.

- In base ai criteri di ridimensionamento, la valutazione del server esamina i dati delle VM basate sulle prestazioni o la configurazione della macchina virtuale locale. L'impostazione del fattore di comfort consente di specificare il fattore di crescita del cluster. Attualmente è abilitato per impostazione predefinita l'hyperthreading, quindi un nodo a 36 Core avrà 72 vCore. Vengono usati 4 vCore per computer fisico per determinare le soglie della CPU per cluster usando lo standard VMware che prevede di non superare un utilizzo dell'80% per consentire la manutenzione o la gestione degli errori senza compromettere la disponibilità del cluster. Attualmente non sono disponibili sostituzioni per modificare i valori di oversubscription, che potrebbero essere presenti nelle versioni future.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale

Se si usa *come dimensionamento locale*, server Assessment non considera la cronologia delle prestazioni delle VM e dei dischi. Al contrario, alloca i nodi AVS in base alla dimensione allocata in locale. Il tipo di archiviazione predefinito è rete VSAN in AVS.

## <a name="confidence-ratings"></a>Classificazioni di confidenza

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità che varia da uno (più basso) a cinque stelle (più alto).

- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- Le classificazioni di attendibilità non sono applicabili per *come valutazioni locali* .
- Per il dimensionamento in base alle prestazioni, le valutazioni AVS in server Assessment richiedono i dati di utilizzo per la CPU e la memoria della macchina virtuale. I dati seguenti vengono raccolti ma non usati nei consigli per il dimensionamento per AVS:
  - I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
  - I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

  Se uno di questi numeri di utilizzo non è disponibile in server vCenter, la raccomandazione di dimensioni potrebbe non essere affidabile.

A seconda della percentuale di punti dati disponibili, la classificazione di attendibilità per la valutazione viene eseguita come segue.


| **Disponibilità dei punti dati** | **Classificazione di attendibilità** |
| - | - |
| 0-20% | 1 stella |
| 21-40% | 2 stelle |
| 41-60% | 3 stelle |
| 61-80% | 4 stelle |
| 81-100% | 5 stelle |

### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:

- L'ambiente non è stato profilato per la durata di creazione della valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- Durante il periodo di calcolo della valutazione sono state arrestate alcune macchine virtuali. Se una macchina virtuale viene spenta per un periodo di tempo specifico, server Assessment non può raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo di calcolo della valutazione. Ad esempio, se è stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create nell'ambiente solo una settimana fa, la cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare la valutazione al dimensionamento locale.

## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Una volta completate le indicazioni sul dimensionamento, Azure Migrate calcola il costo totale di esecuzione dei carichi di lavoro locali in AVS moltiplicando il numero di nodi AVS richiesti dal prezzo del nodo. Il costo per macchina virtuale viene calcolato dividendo il costo totale per il numero di macchine virtuali nella valutazione. 
- Il calcolo prende in considerazione il numero di nodi necessari, il tipo di nodo e il percorso.
- Aggrega il costo in tutti i nodi per calcolare il costo mensile totale.
- I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.

Dato che i prezzi per la soluzione VMware di Azure (AVS) sono per nodo, il costo totale non prevede costi di calcolo e distribuzione dei costi di archiviazione. [Altre informazioni](../azure-vmware/introduction.md)

Si noti che, poiché la soluzione VMware di Azure (AVS) è in anteprima, i prezzi dei nodi nella valutazione sono i prezzi di anteprima. Per informazioni aggiuntive, contattare il team locale di MSFT AVS GBB.

## <a name="migration-tool-guidance"></a>Linee guida per lo strumento di migrazione

Nel report di idoneità per Azure per la valutazione della soluzione Azure VMware è possibile visualizzare gli strumenti consigliati seguenti: 
- **VMware HCX o Enterprise**: per i computer VMware, la soluzione VMware Hybrid Cloud Extension (HCx) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione VMware di Azure (AVS). [Altre informazioni](../azure-vmware/hybrid-cloud-extension-installation.md).
- **Sconosciuto**: Per i computer importati tramite un file con estensione csv, lo strumento di migrazione predefinito è sconosciuto. Tuttavia, per i computer VMware è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Passaggi successivi

Creare una valutazione per le [VM AVS VMware](how-to-create-azure-vmware-solution-assessment.md).
