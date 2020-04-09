---
title: Evitare l'overfitting & dati sbilanciati con AutoML
titleSuffix: Azure Machine Learning
description: Identifica e gestisci le insidie comuni dei modelli di Machine Learning con le soluzioni di apprendimento automatico automatizzate di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 76f920ad6aae68defb567a7a6623d1ffd488af5f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874858"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Impedisci l'overfitting e lo sbrigati con l'apprendimento automatico automatizzato

L'overfitting e i dati sbilanciati sono insidie comuni quando si creano modelli di apprendimento automatico. Per impostazione predefinita, l'apprendimento automatico automatizzato di Azure Machine Learning fornisce grafici e metriche che consentono di identificare questi rischi e implementa le procedure consigliate per ridurli. 

## <a name="identify-over-fitting"></a>Identificare l'overfitting

L'overfitting nell'apprendimento automatico si verifica quando un modello si adatta troppo bene ai dati di training e di conseguenza non è in grado di prevedere con precisione i dati di test non visti. In altre parole, il modello ha semplicemente memorizzato modelli specifici e rumore nei dati di training, ma non è sufficientemente flessibile per eseguire stime sui dati reali.

Considerare i seguenti modelli addestrati e le corrispondenti accuratezza del treno e del test.

| Modello | Precisione del treno | Precisione del test |
|-------|----------------|---------------|
| Una | 99,9% | 95% |
| b | 87% | 87% |
| C | 99,9% | 45% |

Considerando il modello **A**, esiste un malinteso comune che se l'accuratezza dei test su dati non visti è inferiore all'accuratezza del training, il modello è sovramontato. Tuttavia, l'accuratezza del test deve essere sempre inferiore alla precisione di allenamento e la distinzione per l'esaperadattamento e l'adattamento appropriato si riduce a *quanto* meno accurato. 

Quando si confrontano i modelli **A** e **B**, il modello **A** è un modello migliore perché ha una maggiore precisione di test e, sebbene la precisione del test sia leggermente inferiore al 95%, non è una differenza significativa che suggerisce che è presente un over-fitting. Non sceglieresti il modello **B** semplicemente perché le esattezze del treno e del test sono più vicine tra loro.

Il modello **C** rappresenta un chiaro caso di over-fitting; la precisione di allenamento è molto alta, ma la precisione del test non è più vicina. Questa distinzione è soggettiva, ma deriva dalla conoscenza del problema e dei dati e delle magnitudini dell'errore accettabili.

## <a name="prevent-over-fitting"></a>Prevenire l'over-fitting

Nei casi più eclatanti, un modello sovrafitted assumerà che le combinazioni di feature value viste durante il training risulteranno sempre nello stesso output per la destinazione.

Il modo migliore per prevenire l'over-fitting è seguire le best practice di ML, tra cui:

* Utilizzo di più dati di training ed eliminazione delle distorsioni statistiche
* Prevenire la perdita di bersagli
* Utilizzo di un numero inferiore di funzionalità
* **Regolarizzazione e ottimizzazione degli iperparametri**
* **Limitazioni della complessità del modello**
* **Convalida incrociata**

Nel contesto di ML automatizzato, i primi tre elementi precedenti sono **le procedure consigliate implementate.** Gli ultimi tre elementi in grassetto sono **le procedure consigliate implementate da ML per** impostazione predefinita per la protezione contro l'overfitting. In impostazioni diverse dall'automazione di ML, tutte e sei le best practice sono degne di essere seguite per evitare modelli sovrascionali.

### <a name="best-practices-you-implement"></a>Procedure consigliate implementate

L'utilizzo di **più dati** è il modo più semplice e migliore possibile per evitare l'over-fitting, e come un bonus aggiunto in genere aumenta la precisione. Quando si usano più dati, diventa più difficile per il modello memorizzare modelli esatti ed è forzato raggiungere soluzioni più flessibili per soddisfare più condizioni. È anche importante riconoscere la **distorsione statistica**per assicurarsi che i dati di training non includano modelli isolati che non esistono nei dati di stima in tempo reale. Questo scenario può essere difficile da risolvere, perché potrebbe non esserci un esagonale tra il treno e i set di test, ma potrebbe esserci un esagonale rispetto ai dati di test in tempo reale.

La perdita di destinazione è un problema simile, in cui potresti non vedere un esaper adattamento tra i set di treni/test, ma piuttosto viene visualizzato in fase di previsione. La perdita di destinazione si verifica quando il modello "trucchi" durante il training avendo accesso ai dati che normalmente non dovrebbe avere in fase di stima. Ad esempio, se il problema consiste nel prevedere il lunedì quale sarà il prezzo di una merce il venerdì, ma una delle tue funzionalità include accidentalmente i dati del giovedì, sarebbero dati che il modello non avrà in fase di previsione poiché non può vedere nel futuro. La perdita di bersaglio è un errore facile da perdere, ma è spesso caratterizzata da una precisione anormalmente elevata per il tuo problema. Se si sta tentando di prevedere il prezzo delle azioni ed è stato eseguito il training di un modello con una precisione del 95%, è probabile che vi sia una perdita di destinazione da qualche parte nelle feature.

La rimozione di feature può essere utile anche per l'over-fitting impedendo al modello di avere troppi campi da utilizzare per memorizzare modelli specifici, rendendo così più flessibile. Può essere difficile da misurare quantitativamente, ma se è possibile rimuovere le funzionalità e mantenere la stessa precisione, è probabile che il modello sia stato reso più flessibile e abbia ridotto il rischio di sovraadattamento.

### <a name="best-practices-automated-ml-implements"></a>Best practice implementate automatizzate ML

La regolarizzazione è il processo di minimizzazione di una funzione di costo per penalizzare i modelli complessi e imbottiti. Esistono diversi tipi di funzioni di regolarizzazione, ma in generale tutti penalizzano le dimensioni, la varianza e la complessità del coefficiente del modello. La ML automatizzata utilizza L1 (Lazo), L2 (Ridge) ed ElasticNet (L1 e L2 contemporaneamente) in diverse combinazioni con diverse impostazioni di iperparametri del modello che controllano l'over-fitting. In termini semplici, mm automatico varierà quanto un modello è regolato e sceglierà il miglior risultato.

L'mL automatizzato implementa anche limitazioni esplicite di complessità del modello per evitare l'over-fitting. Nella maggior parte dei casi questa implementazione è specifica per gli algoritmi dell'albero delle decisioni o della foresta, in cui la profondità massima dei singoli alberi è limitata e il numero totale di alberi utilizzati nelle tecniche di foresta o insieme è limitato.

La convalida incrociata (CV) è il processo di gestione di molti sottoinsiemi dei dati di training completi e il training di un modello in ogni sottoinsieme. L'idea è che un modello potrebbe ottenere "fortunato" e avere grande precisione con un sottoinsieme, ma utilizzando molti sottoinsiemi il modello non otterrà questa alta precisione ogni volta. Quando si esegue CV, si fornisce un set di dati di controllo di convalida, specificare le pieghe CV (numero di sottoinsiemi) e ML automatizzato verrà eseguito il training del modello e ottimizzare gli iperparametri per ridurre al minimo gli errori nel set di convalida. Una piega CV potrebbe essere iper-adatta, ma usando molti di loro riduce la probabilità che il modello finale sia over-fit. Il compromesso è che CV si traducono in tempi di formazione più lunghi e quindi maggiori costi, perché invece di eseguire il training di un modello una volta, si esegue il training una volta per ogni sottoinsiemi *n* CV. 

> [!NOTE]
> La convalida incrociata non è abilitata per impostazione predefinita; deve essere configurato nelle impostazioni ML automatizzate. Tuttavia, dopo la convalida incrociata è stata configurata e un set di dati di convalida è stato fornito, il processo viene automatizzato per l'utente. Vedere 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificare i modelli con dati sbilanciati

I dati sbilanciati si trovano comunemente nei dati per gli scenari di classificazione di Machine Learning e si riferiscono ai dati che contengono un rapporto sproporzionato di osservazioni in ogni classe. Questo squilibrio può portare a un effetto positivo falsamente percepito dell'accuratezza di un modello, perché i dati di input hanno una distorsione verso una classe, il che si traduce nel modello sottoposto a training per simulare tale distorsione. 

Poiché gli algoritmi di classificazione vengono comunemente valutati in base all'accuratezza, controllare il punteggio di precisione di un modello è un buon modo per identificare se è stato influenzato da dati sbilanciati. Aveva una precisione davvero elevata o una precisione davvero bassa per alcune classi?

Inoltre, le esecuzioni automatiche di Machine Learning generano automaticamente i grafici seguenti, che consentono di comprendere la correttezza delle classificazioni del modello e di identificare i modelli potenzialmente interessati da dati sbilanciati.

Grafico| Descrizione
---|---
[Matrice di confusione](how-to-understand-automated-ml.md#confusion-matrix)| Valuta le etichette classificate correttamente rispetto alle etichette effettive dei dati. 
[Richiamo di precisione](how-to-understand-automated-ml.md#precision-recall-chart)| Valuta il rapporto tra le etichette corrette e il rapporto tra le istanze di etichette trovate dei dati 
[Curve ROC](how-to-understand-automated-ml.md#roc)| Valuta il rapporto tra le etichette corrette e il rapporto delle etichette false positive.

## <a name="handle-imbalanced-data"></a>Gestire i dati sbilanciati 

Nell'ambito dell'obiettivo di semplificare il flusso di lavoro di apprendimento automatico, l'ML automatizzato ha integrato funzionalità per aiutare a gestire i dati sbilanciati, come 

- Una **colonna di peso**: ML automatizzato supporta una colonna ponderata come input, causando le righe nei dati da ponderare verso l'alto o verso il basso, che può rendere una classe più o meno "importante".

- Gli algoritmi utilizzati da ML automatizzato possono gestire correttamente lo squilibrio fino a 20:1, il che significa che la classe più comune può avere 20 volte più righe nei dati rispetto alla classe meno comune.

Le tecniche seguenti sono opzioni aggiuntive per gestire i dati sbilanciati al di fuori di ML automatizzato. 

- Ricampionamento anche allo squilibrio di classe, sovracampionando le classi più piccole o sovracampionando le classi più grandi. Questi metodi richiedono competenze per elaborare e analizzare.

- Utilizzare una metrica delle prestazioni che risieda meglio con i dati sbilanciati. Ad esempio, il punteggio F1 è una media ponderata di precisione e richiamo. La precisione misura l'esattezza di un classificatore: la bassa precisione indica un numero elevato di falsi positivi--, mentre il richiamo misura la completezza di un classificatore: il richiamo basso indica un numero elevato di falsi negativi. 

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli usando l'apprendimento automatico:See examples and learn how to build models using automated machine learning:

+ Seguire [l'esercitazione: eseguire automaticamente](tutorial-auto-train-models.md) il training di un modello di regressione con Azure Machine LearningFollow the Tutorial: Automatically train a regression model with Azure Machine Learning

+ Configurare le impostazioni per l'esperimento di allenamento automatico:
  + In Azure Machine Learning Studio [usare questi passaggi.](how-to-use-automated-ml-for-ml-models.md)
  + Con Python SDK, [utilizzare questi passaggi](how-to-configure-auto-train.md).


