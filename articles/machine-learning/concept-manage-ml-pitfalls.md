---
title: Evitare l'overfitting e lo sbilanciamento dei dati con AutoML
titleSuffix: Azure Machine Learning
description: Identificare e gestire i problemi comuni dei modelli ML con soluzioni di Azure Machine Learning automatizzate.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 171b355f40939efb31e96a4bf8b2d77e97d19f25
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147104"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Impedire l'overfitting e lo sbilanciamento dei dati con Machine learning automatizzato

L'overfitting e lo sbilanciamento dei dati sono ostacoli comuni quando si creano modelli di Machine Learning. Per impostazione predefinita, l'apprendimento automatico di Azure Machine Learning fornisce grafici e metriche che consentono di identificare questi rischi e implementa le procedure consigliate per attenuarli. 

## <a name="identify-over-fitting"></a>Identificare l'overfitting

Nell'apprendimento automatico l'overfitting si verifica quando un modello si adatta troppo ai dati di training e, di conseguenza, non è in grado di prevedere accuratamente i dati di test non visualizzati. In altre parole, il modello ha semplicemente memorizzato modelli e rumore specifici nei dati di training, ma non è sufficientemente flessibile per eseguire stime sui dati reali.

Considerare i seguenti modelli sottoposti a training e le relative precisioni di training e di test.

| Modello | Accuratezza di training | Accuratezza di test |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Si consideri il modello **A**. In base a un malinteso comune, se l'accuratezza di test sui dati non visualizzati è inferiore all'accuratezza di training, si parla di overfitting. L'accuratezza di test, tuttavia, deve essere sempre inferiore all'accuratezza di training e la distinzione tra overfitting e adattamento appropriato si riduce a *quanto* è meno accurato. 

Quando si confrontano i modelli **A** e **B**, il modello **A** è migliore perché ha una maggiore accuratezza di test e anche se tale accuratezza è leggermente inferiore al 95%, non si tratta di una differenza significativa che suggerisca la presenza di overfitting. Non è possibile scegliere il modello **B** semplicemente perché le precisioni di training e di test sono più vicine.

Il modello **C** rappresenta un caso chiaro di overfitting: l'accuratezza di training è molto elevata, ma quella di test non lo è altrettanto. Questa distinzione è soggettiva, ma deriva dalla conoscenza del problema e dei dati e dalle grandezze di errore accettabili.

## <a name="prevent-over-fitting"></a>Impedire l'overfitting

Nei casi più evidenti, un modello con overfitting presuppone che le combinazioni di valori di funzionalità visualizzate durante il training determineranno sempre lo stesso output per la destinazione.

Il modo migliore per evitare l'overfitting consiste nel seguire le procedure consigliate di AML, tra cui:

* Uso di più dati di training ed eliminazione della distorsione statistica
* Prevenzione della perdita di destinazione
* Uso di un numero minore di funzionalità
* **Regolarizzazione e ottimizzazione degli iperparametri**
* **Limitazioni della complessità del modello**
* **Convalida incrociata**

Nel contesto di Machine Learning automatizzato, i primi tre elementi precedenti sono le **procedure consigliate implementate**. Gli ultimi tre elementi in grassetto sono le **procedure consigliate implementate da AML** per impostazione predefinita per la protezione da overfitting. In impostazioni diverse da quelle del Machine Learning automatizzato, è opportuno seguire tutte le sei procedure consigliate per evitare i modelli con overfitting.

### <a name="best-practices-you-implement"></a>Procedure consigliate implementate dall'utente

L'uso di **un numero maggiore di dati** è il modo migliore e più semplice per evitare l'overfitting e consente in genere di aumentare l'accuratezza. Quando si usa un numero maggiore di dati, diventa più difficile per il modello memorizzare modelli esatti e il modello è forzato a raggiungere soluzioni più flessibili per soddisfare più condizioni. È anche importante riconoscere la **distorsione statistica** per garantire che i dati di training non includano modelli isolati che non esisteranno nei dati di stima reali. Questo scenario può essere difficile da risolvere, perché l'overfitting tra i set di training e di test potrebbe non essere presente, ma è possibile che lo sia rispetto ai dati di test attivi.

**La perdita di destinazione** è un problema simile, per cui non è possibile rilevare overfitting tra i set di dati di training e di test, ma è possibile rilevarlo in fase di stima. La perdita di destinazione si verifica quando il modello "bara" durante la fase di training potendo accedere a dati che normalmente non dovrebbe avere al momento della previsione. Se ad esempio problema è stimare il lunedì quale sarà il prezzo di un prodotto il venerdì, ma una delle funzionalità ha incluso accidentalmente i dati dal giovedì, questi sarebbero dati che il modello non avrà al tempo della stima, perché non è in grado di prevedere il futuro. La perdita di destinazione è un semplice errore da eliminare, ma è spesso caratterizzata da un'accuratezza per il problema. Se si tenta di stimare il prezzo azionario ed è stato eseguito il training di un modello con un'accuratezza del 95%, è probabile che si verifichi una perdita di destinazione in alcune delle funzionalità.

La **rimozione di funzionalità** può anche contribuire a risolvere i problemi di overfitting evitando che il modello disponga di troppi campi da usare per memorizzare modelli specifici, determinando in tal modo una maggiore flessibilità. Può essere difficile eseguire una misura quantitativa, ma se è possibile rimuovere le funzionalità e mantenere la stessa accuratezza, è probabile che il modello sia più flessibile e riduca il rischio di overfitting.

### <a name="best-practices-automated-ml-implements"></a>Procedure consigliate per l'implementazione di Machine Learning automatizzato

La **regolarizzazione** è il processo che consente di ridurre una funzione di costo per penalizzare i modelli complessi e quelli con overfitting. Sono disponibili tipi diversi di funzioni di regolarizzazione, ma in generale tutti penalizzano le dimensioni, la varianza e la complessità dei coefficienti del modello. Il Machine Learning automatizzato usa L1 (Lasso), L2 (Ridge) ed ElasticNet (L1 e L2 simultaneamente) in combinazioni diverse con impostazioni di iperparametri del modello differenti che controllano l'overfitting. In termini semplici, la funzione di Machine Learning automatizzato può variare quanto un modello viene regolato e scegliere il risultato migliore.

Il Machine Learning automatizzato implementa anche **limitazioni di complessità del modello** per evitare l'overfitting. Nella maggior parte dei casi questa implementazione è specifica per gli algoritmi delle foreste o degli alberi delle decisioni, in cui la profondità massima dell'albero e il numero totale di alberi usati nelle tecniche della foresta o dell'insieme sono limitati.

La **convalida incrociata** è il processo di acquisizione di molti subset di dati di training completi e di esecuzione del training di un modello su ogni subset. Il concetto è che un modello potrebbe essere "fortunato" ed essere estremamente preciso per un subset, ma se usa molti subset il modello non riuscirà a ottenere ogni volta questa elevata accuratezza. Quando si esegue la convalida incrociata, si fornisce un set di dati di controllo della convalida, si specifica il numero di subset della convalida e il Machine Learning automatizzato consente di eseguire il training del modello e di ottimizzare gli iperparametri per ridurre gli errori nel set di convalida. Un numero di subset della convalida incrociata può presentare overfitting, ma usandone un numero elevato si riduce la probabilità che il modello finale sia caratterizzato da overfitting. Lo svantaggio è che la convalida incrociata comporta tempi di training più lunghi e un costo maggiore, perché invece di eseguire il training di un modello una volta, viene eseguito il training una volta per ogni *n* subset di convalida incrociata. 

> [!NOTE]
> La convalida incrociata non è abilitata per impostazione predefinita e deve essere configurata nelle impostazioni di Machine Learning automatizzato. Dopo la configurazione della convalida incrociata e dopo che stato specificato un set di dati di convalida, tuttavia, il processo viene automatizzato. Altre informazioni sulla [configurazione della convalida incrociata in auto ml](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificare i modelli con sbilanciamento dei dati

Lo sbilanciamento dei dati è comunemente presente nei dati per gli scenari di classificazione di apprendimento automatico e si riferiscono a dati che contengono un rapporto sproporzionato delle osservazioni in ogni classe. Questo squilibrio può comportare un effetto percepito erroneamente come positivo dell'accuratezza di un modello, perché i dati di input presentano una distorsione verso una classe, che determina che il modello sottoposto a training simuli tale distorsione. 

Le esecuzioni del Machine Learning automatizzato, inoltre, generano automaticamente i grafici seguenti, che consentono di comprendere la correttezza delle classificazioni del modello e di identificare i modelli potenzialmente interessati dallo sbilanciamento dei dati.

Grafico| Descrizione
---|---
[Matrice di confusione](how-to-understand-automated-ml.md#confusion-matrix)| Valuta le etichette classificate correttamente rispetto alle etichette effettive dei dati. 
[Precisione-richiamo](how-to-understand-automated-ml.md#precision-recall-chart)| Valuta il rapporto tra le etichette corrette rispetto al rapporto tra le istanze di etichette dei dati trovate 
[Curve ROC](how-to-understand-automated-ml.md#roc)| Valuta il rapporto tra le etichette corrette rispetto al rapporto tra le etichette falso positive.

## <a name="handle-imbalanced-data"></a>Gestire lo sbilanciamento dei dati 

Come parte del suo obiettivo di semplificare il flusso di lavoro di Machine Learning, l'apprendimento **automatico è integrato in funzionalità** che consentono di gestire dati sbilanciati, ad esempio 

- Una **colonna di ponderazione**: la ml automatizzata supporta una colonna di pesi come input, causando la ponderazione delle righe nei dati, che possono essere usate per rendere una classe più o meno "importante".

- Gli algoritmi usati da Machine Learning automatiche rilevano uno squilibrio quando il numero di campioni della classe minoritaria è uguale o inferiore al 20% del numero di campioni nella classe di maggioranza, in cui la classe minoritaria fa riferimento a quella con minor numero di campioni e la classe di maggioranza fa riferimento a quella con la maggior parte degli esempi. Successivamente, AutoML eseguirà un esperimento con dati sottocampionati per verificare se l'uso di pesi di classe risolverebbe questo problema e migliora le prestazioni. Se si verifica un miglioramento delle prestazioni tramite questo esperimento, viene applicato questo rimedio.

- Uso di una metrica delle prestazioni più adatta allo sbilanciamento dei dati. Ad esempio, il AUC_weighted è una metrica primaria che calcola il contributo di ogni classe in base al numero relativo di campioni che rappresentano tale classe, quindi risulta più affidabile rispetto allo squilibrio.

Le tecniche seguenti sono opzioni aggiuntive per la gestione di dati sbilanciati al **di fuori del**Machine Learning automatico. 

- Ricampionamento per equilibrare lo sbilanciamento delle classi, eseguendo il campionamento verso l'alto delle classi più piccole o quello verso il basso delle classi più grandi. Tali metodi richiedono competenze per eseguire l'elaborazione e l'analisi.

- Esaminare le metriche delle prestazioni per i dati sbilanciati. Il punteggio F1, ad esempio, è una media ponderata delle curve di precisione e richiamo. Le curve di precisione misura l'esattezza di un classificatore e la precisione bassa indica un numero elevato di falsi positivi, mentre le curve di richiamo misura la completezza di un classificatore e un richiamo minimo indica un numero elevato di falsi negativi.

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli tramite il Machine Learning automatizzato.

+ Seguire l'[Esercitazione: Eseguire il training di un modello di regressione con Machine Learning automatizzato](tutorial-auto-train-models.md)

+ Configurare le impostazioni per un esperimento di training automatico:
  + In Azure Machine Learning Studio, [usare questi passaggi](how-to-use-automated-ml-for-ml-models.md).
  + Con Python SDK, [seguire questi passaggi](how-to-configure-auto-train.md).


