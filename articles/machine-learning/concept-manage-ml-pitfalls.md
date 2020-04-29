---
title: Evitare l'overfitting & dati sbilanciati con AutoML
titleSuffix: Azure Machine Learning
description: Identificare e gestire i problemi comuni dei modelli ML con soluzioni automatiche di Machine Learning Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 76f920ad6aae68defb567a7a6623d1ffd488af5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80874858"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Impedisci l'overfitting e i dati sbilanciati con Machine Learning automatico

I dati in eccesso e sbilanciati sono trappole comuni quando si creano modelli di apprendimento automatico. Per impostazione predefinita, l'apprendimento automatico automatico di Azure Machine Learning fornisce grafici e metriche che consentono di identificare questi rischi e implementa le procedure consigliate per attenuarli. 

## <a name="identify-over-fitting"></a>Identificare il overfitting

L'overfitting nell'apprendimento automatico si verifica quando un modello si adatta troppo ai dati di training e, di conseguenza, non è in grado di prevedere accuratamente i dati di test non visualizzati. In altre parole, il modello ha semplicemente memorizzato modelli specifici e rumore nei dati di training, ma non è sufficientemente flessibile per eseguire stime sui dati reali.

Considerare i modelli sottoposti a training seguenti e le relative precisioni di training e di test.

| Modello | Accuratezza del training | Accuratezza test |
|-------|----------------|---------------|
| Una | 99,9% | 95% |
| b | 87% | 87% |
| C | 99,9% | 45% |

Considerando il modello **a**, si verifica un malinteso comune che se l'accuratezza dei test sui dati non visualizzati è inferiore alla precisione di training, il modello viene sovramontato. Tuttavia, l'accuratezza dei test deve essere sempre inferiore alla precisione del training e la distinzione tra adattamento e adattamento rispetto a quello appropriato è *molto* meno accurata. 

Quando si confrontano i modelli **a** e **B**, Model **a** è un modello migliore perché ha una maggiore precisione del test e anche se l'accuratezza del test è leggermente inferiore al 95%, non si tratta di una differenza significativa che suggerisce che l'overfitting è presente. Non è possibile scegliere il modello **B** semplicemente perché le precisioni di training e test sono più vicine.

Il modello **C** rappresenta un caso chiaro di overfitting; l'accuratezza del training è molto elevata, ma l'accuratezza dei test non è quasi così elevata. Questa distinzione è soggettiva, ma deriva dalla conoscenza del problema e dei dati e dalle grandezze di errore accettabili.

## <a name="prevent-over-fitting"></a>Impedisci l'overfitting

Nei casi più eclatanti, un modello in eccesso presuppone che le combinazioni di valori di funzionalità visualizzate durante il training determineranno sempre lo stesso output per la destinazione.

Il modo migliore per evitare l'overfitting consiste nel seguire le procedure consigliate di ML, tra cui:

* Uso di più dati di training ed eliminazione della distorsione statistica
* Prevenzione della perdita di destinazione
* Utilizzo di meno funzionalità
* **Regolarizzazione e ottimizzazione degli iperparametri**
* **Limitazioni della complessità del modello**
* **Convalida incrociata**

Nel contesto di Machine Learning automatiche, i primi tre elementi precedenti sono le **procedure consigliate implementate**. Per impostazione predefinita, gli ultimi tre elementi in grassetto sono **implementati** in modo automatico per le procedure consigliate per la protezione dall'overfitting. In impostazioni diverse da quelle automatiche di Machine Learning, è opportuno seguire tutte e sei le sei procedure consigliate per evitare i modelli in eccesso.

### <a name="best-practices-you-implement"></a>Procedure consigliate per l'implementazione

L'uso di **più dati** è il modo più semplice e migliore possibile per evitare l'overfitting e, in genere, un bonus aggiunto aumenta l'accuratezza. Quando si usano più dati, diventa più difficile per il modello memorizzare i modelli esatti ed è forzato a raggiungere soluzioni più flessibili per soddisfare più condizioni. È anche importante riconoscere la **distorsione statistica**, per garantire che i dati di training non includano modelli isolati che non esistano nei dati di stima Live. Questo scenario può essere difficile da risolvere, perché potrebbe non essere necessario eseguire l'overfitting tra i set di training e di test, ma è possibile che si verifichi un'overfitting rispetto ai dati di test attivi.

La perdita di destinazione è un problema simile, in cui non è possibile visualizzare l'overfitting tra set di training o di test, ma viene invece visualizzato in fase di stima. La perdita di destinazione si verifica quando il modello "imbroglia" durante il training consentendo l'accesso ai dati che in genere non dovrebbero avere in fase di stima. Ad esempio, se il problema è stimare il lunedì quale sarà il prezzo di un prodotto venerdì, ma una delle funzionalità incluse accidentalmente i dati dal giovedì, ovvero i dati che il modello non avrà a tempo stimato, perché non è in grado di vedere il futuro. La perdita di destinazione è un semplice errore da perdere, ma è spesso caratterizzata da un'accuratezza eccessiva per il problema. Se si sta tentando di stimare il prezzo azionario ed eseguito il training di un modello con una precisione del 95%, è probabile che si verifichi una perdita di destinazione in alcune delle funzionalità.

La rimozione delle funzionalità può essere utile anche in caso di overfitting evitando che il modello disponga di un numero eccessivo di campi da usare per memorizzare modelli specifici, causando in tal modo una maggiore flessibilità. Può essere difficile misurare quantitativamente, ma se è possibile rimuovere le funzionalità e mantenere la stessa accuratezza, è probabile che il modello sia più flessibile e abbia ridotto il rischio di overfitting.

### <a name="best-practices-automated-ml-implements"></a>Procedure consigliate per l'implementazione automatica di ML

La regolarizzazione è il processo che consente di ridurre al minimo una funzione di costo per penalizzare i modelli complessi e sovraadattati. Sono disponibili tipi diversi di funzioni di regolarizzazione, ma in generale tutti penalizzano le dimensioni, la varianza e la complessità dei coefficienti del modello. Automatizzato ML USA L1 (lazo), L2 (Ridge) e ElasticNet (L1 e L2 simultaneamente) in combinazioni diverse con impostazioni di iperparametri del modello diverse che controllano l'overfitting. In termini semplici, la funzione di Machine Learning automatica può variare quanto un modello viene regolato e scegliere il risultato migliore.

Automatizzato ML implementa anche limitazioni di complessità dei modelli esplicite per impedire l'overfitting. Nella maggior parte dei casi questa implementazione è specifica per gli algoritmi delle foreste o degli alberi delle decisioni, in cui la profondità massima dell'albero è limitata e il numero totale di alberi utilizzati nelle tecniche della foresta o dell'insieme sono limitati.

La convalida incrociata (CV) è il processo di acquisizione di molti subset di dati di training completi e di training di un modello su ogni subset. Il concetto è che un modello potrebbe ottenere una "fortuna" e avere una grande accuratezza con un subset, ma usando molti subset, il modello non riuscirà a ottenere questa accuratezza elevata ogni volta. Quando si esegue il curriculum vitae, si fornisce un set di dati di dati di convalida, si specificano le riduzioni del CV (numero di subset) e il Machine Learning Machine Learning consente di eseguire il training del modello e ottimizzare gli iperparametri per ridurre al minimo gli errori nel set di convalida. Una riduzione del CV potrebbe essere più adatta, ma con molti di essi si riduce la probabilità che il modello finale sia troppo adatto. Il compromesso consiste nel fatto che il CV comporta tempi di training più lunghi e un costo maggiore, perché invece di eseguire il training di un modello una sola volta, viene eseguito il training una volta per ogni subset *n* CV. 

> [!NOTE]
> La convalida incrociata non è abilitata per impostazione predefinita. deve essere configurato in impostazioni di Machine Learning automatiche. Tuttavia, dopo la configurazione della convalida incrociata ed è stato fornito un set di dati di convalida, il processo viene automatizzato automaticamente. Vedere 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificare i modelli con dati sbilanciati

I dati sbilanciati sono comunemente presenti nei dati per gli scenari di classificazione di machine learning e si riferiscono a dati che contengono un rapporto sproporzionato delle osservazioni in ogni classe. Questo squilibrio può comportare un effetto positivo, erroneamente, dell'accuratezza di un modello, perché i dati di input hanno una distorsione verso una classe, il che comporta il training del modello per simulare tale distorsione. 

Poiché gli algoritmi di classificazione vengono in genere valutati in base all'accuratezza, il controllo del Punteggio di accuratezza di un modello è un modo efficace per identificare se è stato influenzato da dati sbilanciati. Ha avuto un'accuratezza molto elevata o un'accuratezza molto bassa per determinate classi?

Inoltre, le esecuzioni automatiche di ML generano automaticamente i grafici seguenti, che consentono di comprendere la correttezza delle classificazioni del modello e di identificare i modelli potenzialmente interessati da dati sbilanciati.

Grafico| Descrizione
---|---
[Matrice di confusione](how-to-understand-automated-ml.md#confusion-matrix)| Valuta le etichette classificate correttamente rispetto alle etichette effettive dei dati. 
[Precisione-richiamo](how-to-understand-automated-ml.md#precision-recall-chart)| Valuta il rapporto tra le etichette corrette e il rapporto tra le istanze di etichette trovate dei dati 
[Curve ROC](how-to-understand-automated-ml.md#roc)| Valuta il rapporto tra le etichette corrette e il rapporto tra le etichette false.

## <a name="handle-imbalanced-data"></a>Gestire i dati sbilanciati 

Come parte del suo obiettivo di semplificare il flusso di lavoro di Machine Learning, l'apprendimento automatico è integrato in funzionalità che consentono di gestire dati sbilanciati, ad esempio 

- Una **colonna di ponderazione**: la ml automatizzata supporta una colonna ponderata come input, causando la ponderazione delle righe nei dati, che possono rendere una classe più o meno "importante".

- Gli algoritmi usati da Machine Learning automatiche possono gestire in modo corretto squilibrio fino a 20:1, ovvero la classe più comune può avere più di 20 volte più righe nei dati rispetto alla classe meno comune.

Le tecniche seguenti sono opzioni aggiuntive per la gestione di dati sbilanciati al di fuori del Machine Learning automatico. 

- Ripetizione del campionamento anche allo squilibrio della classe, eseguendo il campionamento delle classi più piccole o sottocampionando le classi più grandi. Questi metodi richiedono competenze per elaborare e analizzare.

- Usare una metrica delle prestazioni più adatta ai dati sbilanciati. Il Punteggio F1, ad esempio, è una media ponderata di precisione e richiamo. La precisione misura l'esattezza di un classificatore: la precisione bassa indica un numero elevato di falsi positivi--, mentre richiama misura la completezza di un classificatore. il richiamo minimo indica un numero elevato di falsi negativi. 

## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi e informazioni su come creare modelli usando Machine Learning automatico:

+ Seguire l' [esercitazione: eseguire automaticamente il training di un modello di regressione con Azure Machine Learning](tutorial-auto-train-models.md)

+ Configurare le impostazioni per l'esperimento di training automatico:
  + In Azure Machine Learning Studio [usare questa procedura](how-to-use-automated-ml-for-ml-models.md).
  + Con Python SDK, [seguire questa procedura](how-to-configure-auto-train.md).


