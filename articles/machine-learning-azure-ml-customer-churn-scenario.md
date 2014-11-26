<properties title="Analyzing Customer Churn using Microsoft Azure Machine Learning" pageTitle="Analyzing Customer Churn using Microsoft Machine Learning | Azure" description="Case study of developing an integrated model for analyzing and scoring customer churn" metaKeywords="" services="" solutions="" documentationCenter="" authors="barga" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="barga" />

# Analisi della varianza dei clienti con Machine Learning di Microsoft Azure

In questo documento viene presentata un'implementazione di riferimento per un progetto di analisi della varianza dei clienti compilato usando Azure Machine Learning Studio e vengono illustrati i modelli generici associati per una risoluzione olistica del problema della varianza dei clienti a livello industriale. Verrà inoltre misurata l'accuratezza dei modelli compilati con Azure Machine Learning e verranno individuate le indicazioni per l'ulteriore sviluppo.

## Il problema della varianza dei clienti

Le aziende del mercato consumer, come tutte quelle dei settori commerciali, devono fare i conti con la varianza. In certi casi una varianza eccessiva può arrivare a influenzare i criteri decisionali. La soluzione tradizionale consiste nel prevedere i fattori con elevata propensione alla varianza e gestire le relative esigenze tramite un servizio di concierge, campagne di marketing o tramite l'applicazione di dispense speciali. Questi approcci possono variare da settore a settore e persino da un determinato raggruppamento di clienti a un altro all'interno di un settore (ad esempio quello delle telecomunicazioni). Il fattore comune è che le aziende hanno necessità di ridurre al minimo questi sforzi speciali rivolti alla fidelizzazione. Pertanto, una metodologia consisterebbe nell'assegnazione di un punteggio a ogni cliente con probabilità di varianza per poi gestire i primi N classificati. I clienti principali potrebbero essere anche i più redditizi. In scenari più sofisticati viene impiegata una funzione di redditività durante la selezione dei candidati per una dispensa speciale. Tuttavia, queste considerazioni rappresentano solo una parte della strategia olistica alla base della gestione della varianza. Le aziende devono inoltre tenere in considerazione il rischio (e la tolleranza al rischio associata), il livello e i costi dell'intervento e una segmentazione dei clienti plausibile.

## Prospettive e approcci di settore

La gestione sofisticata della varianza è un indicatore di maturità di un settore. L'esempio classico è dato dal settore delle telecomunicazioni, nel quale gli abbonati sono soliti passare frequentemente da un provider a un altro. Questa varianza volontaria rappresenta un motivo primario di preoccupazione. I provider, inoltre, hanno accumulato notevoli conoscenze riguardo agli *elementi che inducono varianza* ovvero i fattori che spingono i clienti a cambiare. La scelta dell'apparecchio o del dispositivo, ad esempio, è un fattore di varianza ben noto nel settore della telefonia mobile. Come risultato, un criterio diffuso è la sovvenzione promozionale di una parte del costo di un telefono per i nuovi abbonati e l'addebito del prezzo intero per i clienti esistenti o per un cambio di modello. Storicamente, tale criterio ha portato i clienti a passare da un provider all'altro per ottenere u nuovo sconto, il che, a sua volta, ha indotto i provider a raffinare le proprie strategie. L'elevata volatilità nelle offerte legate ai dispositivi è un fattore in grado di invalidare velocemente i modelli di varianza che si basano sui modelli di dispositivo attuali. In aggiunta, i telefoni mobili non sono solo dispositivi per le telecomunicazioni ma anche realtà legate alla moda (si consideri ad esempio l'iPhone) e tali indicatori sociali restano esclusi dall'ambito dei normali set di dati riferibili alle telecomunicazioni. Il risultato netto per l'uso dei modelli è l'impossibilità di implementare un criterio efficace solamente eliminando i motivi noti per la varianza. Una continua strategia basata sui modelli, compresi quelli classici che quantificano variabili categoriche (ad esempio gli alberi delle decisioni) è difatti **indispensabile**.

Usando set di Big Data sui clienti, le organizzazioni eseguono l'analisi di tali Big Data, sfruttandoli in modo particolare per quanto riguarda il rilevamento della varianza e individuando in questo metodo un efficace approccio al problema. Per altre informazioni sull'approccio con uso di Big Data al problema della varianza vedere la sezione relativa alle raccomandazioni su ETL.

## Metodologia per generare un modello di varianza dei clienti

Un processo comune di risoluzione dei problemi legati alla varianza dei clienti è illustrato nei diagrammi 1-3:

1.  Un modello di rischio consente di considerare il modo in cui le azioni influiscono su probabilità e rischio.
2.  Un modello di intervento permette di valutare il modo in cui il livello di intervento può influire sulle probabilità di varianza e su indicatori di marketing come il Customer Lifetime Value (CLV).
3.  Questa analisi contribuisce a un'analisi qualitativa che viene poi inserita in una campagna di marketing proattiva che ha come obiettivo i segmenti di clientela a cui recapitare l'offerta ottimizzata.

![][0]

Questo approccio proattivo rappresenta il modo migliore per gestire la varianza, ma porta con sé una certa complessità: è necessario sviluppare un archetipo multimodello e tracciare dipendenze tra i modelli. L'interazione tra modelli può essere incapsulata come illustrato nel diagramma seguente:

![][1]

*Figura 4: Archetipo multimodello unificato*

Le interazioni tra i differenti modelli è fondamentale per lo sviluppo di un approccio olistico alla fidelizzazione. Ogni modello subisce inevitabilmente un degrado con il passare degli anni, pertanto, l'architettura è un ciclo implicito (simile all'archetipo impostato dallo standard di data mining CRISP-DM [***3***]).

Il ciclo generale di segmentazione/scomposizione di rischio-decisione-marketing è ancora una struttura generalizzata, applicabile a molti problemi di business. L'analisi della varianza è semplicemente un elemento altamente rappresentativo di questo gruppo di problemi, poiché presenta tutti i tratti di una complessa problematica di business che non consente una soluzione predittiva semplificata. Gli aspetti sociali dell'approccio moderno alla varianza non sono particolarmente evidenziati nell'approccio, ma gli aspetti sociali sono incapsulati nell'archetipo di modellazione, come lo sarebbero in qualsiasi altro modello.

Un'aggiunta interessante in questo ambito è l'analisi dei Big Data. Negli attuali settori commerciali delle telecomunicazioni e della rivendita al dettaglio vengono raccolti dati esaurienti sui clienti e risulta immediatamente evidente come la necessità di connettività multimodello diverrà un trend comune, date le tendenze emergenti quali "Internet delle cose" e l'universalità dei dispositivi. Ciò che consentirà alle aziende di impiegare soluzioni intelligenti e organizzate su più livelli.

 

# Implementazione di un archetipo di modellazione in ML Studio di Microsoft Azure

Dato il problema appena descritto sopra, come è possibile implementare un modello integrato e un sistema di punteggio? In questa sezione verrà dimostrato come ottenere ciò usando Cloud ML Studio di Microsoft Azure.

L'approccio multimodello è indispensabile quando si progetta un archetipo globale per la varianza. Anche la parte (predittiva) dell'approccio correlata al punteggio deve essere multimodello.

Nel diagramma seguente viene mostrato il prototipo creato, che impiega quattro diversi algoritmi di punteggio in Cloud ML Studio per prevedere la varianza. Il motivo per usare un approccio multimodello non è solo il poter creare un classificatore basato su insiemi per aumentare l'accuratezza, ma anche la protezione da inserimenti superflui e la possibilità di migliorare la selezione futura.

![][2]

*Figura 5: Prototipo di un approccio di modellazione di varianza*

Nelle sezioni seguenti verranno forniti maggiori dettagli sul prototipo di modello di punteggio implementato usando Cloud ML Studio.

## Selezione e preparazione dei dati

I dati usati per creare i modelli e classificare i clienti sono stati ottenuti da una soluzione verticale CRM, con i dati offuscati per proteggere la privacy dei clienti. I dati effettivi contengono informazioni su 8.000 abbonati negli Stati Uniti e provengono da una combinazione di tre origini: dati di provisioning (metadati di sottoscrizione), dati di attività (uso del sistema) e dati relativi al supporto tecnico. I dati non includono informazioni commerciali sui clienti. Ad esempio, non sono inclusi metadati relativi alla fedeltà o a punteggi di credito.

Per semplicità, i processi ETL e di pulizia dei dati sono esclusi dall'ambito, in quanto si presuppone che la preparazione dei dati sia già stata completata altrove.

La selezione di funzionalità per la modellazione si basa su un punteggio preliminare di importanza del set di indicatori, compresi nel processo che usa il modulo foresta casuale. Per l'implementazione in Cloud ML Studio, sono stati calcolati la media, la mediana e gli intervalli per le funzionalità rappresentative. Ad esempio, sono stati aggiunti aggregati per i dati qualitativi, ad esempio i valori minimi e massimi per l'attività degli utenti.

Sono state inoltre acquisite informazioni temporali relative ai sei mesi precedenti. Sono stati usati solo gli ultimi sei mesi perché in seguito all'analisi dei dati di un anno intero è stato rilevato che, sebbene statisticamente esistessero trend significativi, l'effetto della varianza dopo sei mesi diminuiva notevolmente.

L'aspetto più importante è rappresentato dal fatto che l'intero processo, compresi ETL, selezione delle funzionalità e modellazione, è stato inserito in Cloud ML Studio usando origini dati in Microsoft Azure.

Nei diagrammi che seguono sono illustrati i dati impiegati:

![][3]

*Figura 6: Estratto dell'origine dati (offuscati)*

![][4]

*Figura 7: Funzionalità estratte dall'origine dati*
 

## Algoritmi usati nel prototipo

Per la realizzazione del prototipo sono stati impiegati i seguenti quattro algoritmi di Machine Learning (senza personalizzazione):

1.  Regressione logistica (LR)
2.  Albero delle decisioni con boosting (BT)
3.  Percezione media (AP)
4.  Macchina a vettori di supporto (SVM)

Nel diagramma seguente viene illustrata una porzione dell'area di progettazione dell'esperimento, che indica la sequenza secondo cui sono stati creati i modelli:

![][5]

*Figura 8: Creazione di modelli ML in Cloud ML Studio*

## Metodi di assegnazione del punteggio

I quattro modelli sono stati classificati usando un set di dati di training etichettati.

È stato anche inviato il set di dati di punteggio a un modello paragonabile usando l'edizione desktop di SAS Enterprise Miner 12 ed è stata misurata l'accuratezza del modello SAS oltre a quella di tutti e quattro i modelli di Cloud ML di Azure.

# Risultati

In questa sezione vengono presentati i risultati relativi all'accuratezza dei modelli in base al set di dati di punteggio.

## Accuratezza e precisione del punteggio

In generale, l'implementazione in Cloud ML di Azure si attesta, come accuratezza su un 10-15% al di sotto di SAS (AUC).

La metrica più importante in ambito di varianza è tuttavia il tasso di errata classificazione, ovvero, tra i primi X candidati alla varianza, secondo le previsioni del classificatore, quali di essi **non** sono variati, ricevendo tuttavia il trattamento speciale? Nel diagramma seguente vengono confrontati tali tassi di errata classificazione per tutti i modelli:

![][6]

*Figura 9: Area sottesa dalla curva del prototipo Passau*

### Uso di AUC per il confronto dei risultati

L'area sottesa dalla curva (AUC, Area Under Curve) è una metrica che rappresenta una misura globale di *separabilità* tra le distribuzioni di punteggi per popolazioni positive e negative. È simile al grafico ROC (Receiver Operator Characteristic) tradizionale, ma un'importante differenza è rappresentata dal fatto che la metrica AUC non richiede un valore soglia, ma fornisce invece un riepilogo dei risultati di **tutte** le scelte possibili. Al contrario, il grafico ROC tradizionale mostra il tasso positivo sull'asse verticale e il tasso di falsi positivi su quello orizzontale, con conseguente variazione della soglia di classificazione.

La metrica AUC viene in genere usata come misura di valore per diversi algoritmi (o diversi sistemi) perché consente il confronto dei modelli mediante i relativi valori AUC. Si tratta di un approccio molto diffuso in settori quali quello della meteorologia, delle bioscienze e in molti altri. AUC rappresenta quindi uno strumento popolare per la gestione delle prestazioni dei classificatori.

### Confronto dei tassi di errata classificazione

I tassi di errata classificazione nel set di dati in questione sono stati confrontati usando i dati CRM di circa 8.000 sottoscrizioni.

-   Il tasso di errata classificazione SAS è risultato essere il 10-15%.
-   Il tasso di errata classificazione di Azure ML è risultato essere del 15-20% per i primi 200-300 soggetti classificati per la varianza.

Nel settore delle telecomunicazioni è importante rivolgersi solamente a quei clienti che presentano il maggior rischio di varianza, offrendo loro un servizio concierge o un altro tipo di trattamento speciale. In tale ambito, l'implementazione Cloud ML di Azure offre risultati equivalenti a quelli ottenibili con il modello SAS.

Analogamente, l'accuratezza è più importante della precisione perché ciò che interessa maggiormente è una corretta classificazione dei soggetti inclini alla varianza.

Il seguente diagramma da Wikipedia illustra la relazione in un grafico vivace e di facile comprensione:

![][7]

*Figura 10: Compromesso tra accuratezza e precisione*

### Risultati di accuratezza e precisione per il modello basato sull'albero delle decisioni con boosting

La figura seguente illustra i risultati non elaborati dell'assegnazione del punteggio usando il prototipo di Cloud ML di Azure per il modello con albero delle decisioni con boosting a due classi, che risulta essere il più accurato dei quattro modelli:

![][8]

*Figura 11: Caratteristiche dell'albero delle decisioni con boosting*

## Confronto delle prestazioni

È stata confrontata la velocità con cui è stato assegnato il punteggio usando Cloud ML di Azure e un modello paragonabile creato usando l'edizione desktop di SAS Enterprise Miner 12.1.

La tabella seguente riepiloga le prestazioni degli algoritmi:

*Tabella 1. Prestazioni generali (accuratezza) degli algoritmi*

| **LR**        | **BT**           | **AP**             | **SVM**       |
|---------------|------------------|--------------------|---------------|
| Modello medio | Modello migliore | Prestazioni scarse | Modello medio |

I modelli ospitati in Cloud ML di Azure hanno superato SAS del 15-25% in velocità di esecuzione ma l'accuratezza è risultata praticamente alla pari.

# Discussione e raccomandazioni

Nel settore delle telecomunicazioni sono state sviluppate numerose pratiche per l'analisi della varianza:

-   Derivare metriche per quattro categorie fondamentali:

    -   **Entità (ad esempio sottoscrizioni)**. Fornire informazioni di base sulle sottoscrizioni e/o sui clienti soggetti a varianza.
    -   **Attività**. Ottenere tutte le informazioni possibili sull'utilizzo relative all'entità, ad esempio il numero di accessi e così via.
    -   **Supporto tecnico**. Raccogliere informazioni dai log del supporto tecnico per indicare se l'abbonato ha avuto problemi o interazioni con il supporto tecnico.
    -   **Dati commerciali e competitivi**. Ottenere tutte le informazioni possibili in merito al cliente (possono essere indisponibili o difficili da tracciare).
-   Usare l'importanza per alimentare la selezione delle funzionalità. Questo implica che l'albero della foresta con boosting/casuale sia sempre un approccio promettente.

L'uso delle quattro categorie sopra crea l'illusione che un semplice approccio *deterministico*, basato su indici formati su fattori ragionevoli per categoria, dovrebbe essere sufficiente per identificare i clienti a rischio di varianza. Sfortunatamente, per quanto questa nozione appaia plausibile, si tratta di un intendimento errato. Il motivo è dato dal fatto che la varianza è un effetto temporale e i fattori che contribuiscono ad essa si trovano in genere in uno stato temporaneo. Ciò che induce un cliente a valutare un cambio di gestore oggi, potrebbe essere diverso domani e sicuramente sarà ancora diverso tra sei mesi. Pertanto, un modello *probabilistico* è una necessità.

Questa importante osservazione è spesso sottovalutata in ambiente business, dove in genere di preferisce un approccio orientato alla BI, principalmente perché è una soluzione più semplice e ammette un'automazione semplificata.

Tuttavia, la promessa dell'analisi self-service con Cloud ML di Azure è data dal fatto che quattro categorie di informazioni, classificate per divisione o dipartimento, diventano un'origine preziosa per le attività di Machine Learning rivolte alla varianza.

Un'altra entusiasmante funzionalità in Cloud ML di Azure è la possibilità di aggiungere un modulo personalizzato nel repository dei moduli predefiniti già disponibili. Essenzialmente, tale capacità consente di creare un'opportunità per selezionare raccolte e creare modelli per mercati verticali. Si tratta di un importante elemento di differenziazione per Cloud ML di Azure nel mercato.

Si prevede di trattare questo argomento in futuro, specialmente per quanto riguarda l'analisi dei Big Data.
  

# Conclusioni

In questo documento viene descritto un approccio intelligente alla gestione di un problema comune, la varianza dei clienti, usando un framework generico. Si è considerato un prototipo per la classificazione di modelli e lo si è implementato usando Cloud ML di Microsoft Azure. Infine, sono state valutate l'accuratezza e le prestazioni della soluzione prototipo rispetto ad algoritmi paragonabili in SAS.

**Per altre informazioni:**

Il documento è stato di aiuto? Gradiremmo ricevere commenti e suggerimenti. Indicare, su una scala da 1 (scadente) a 5 (eccellente) come si classificherebbe questo articolo e indicare perché si è assegnato tale punteggio. Ad esempio:

-   Si sta assegnando un punteggio alto perché l'articolo contiene degli ottimi esempi, schermate eccellenti, il linguaggio è chiaro o per un altro motivo?
-   Si sta assegnando un punteggio basso perché gli esempi sono scarsi, le schermate poco chiare o il linguaggio è incomprensibile?

Questi commenti e suggerimenti aiuteranno Microsoft a migliorare la qualità dei white paper prodotti.

[Invia commenti e suggerimenti][Invia commenti e suggerimenti].
 

# Riferimenti

[1][1] Predictive analytics: beyond the predictions, W. McKnight, Information Management, luglio/agosto 2011, pp.18-20.

[2][1] <http://en.wikipedia.org/wiki/Accuracy_and_precision>

[3][2] <http://www.the-modeling-agency.com/crisp-dm.pdf>

[4][3] Big Data Marketing

[5][4] <http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn>
 

# Appendice

![][9]

*Figura 12. Snapshot d una presentazione sul prototipo di varianza*

  [0]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
  [1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
  [2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
  [3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
  [4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
  [5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
  [6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
  [7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
  [8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
  [Invia commenti e suggerimenti]: mailto:sqlfback@microsoft.com
  [9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
