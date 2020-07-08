---
title: Valutare e mitigare i problemi di equità nei modelli di Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni sull'equità nei modelli di Machine Learning e sul modo in cui il pacchetto Fairlearn Python può aiutare a creare modelli più equi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 06/30/2020
ms.openlocfilehash: c4e9b8386c8341d076a69e2e81b5e92f296153ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611782"
---
# <a name="build-fairer-machine-learning-models"></a>Creazione di modelli di Machine Learning più giusti

Informazioni sull'equità nell'apprendimento automatico e sul modo in cui il pacchetto python open source di [Fairlearn](https://fairlearn.github.io/) consente di creare modelli più corretti. Se non si sta tentando di comprendere i problemi di equità e di valutare l'equità quando si creano modelli di apprendimento automatico, è possibile creare modelli che producono risultati non corretti. 

Il seguente riepilogo del [manuale dell'utente](https://fairlearn.github.io/user_guide/index.html) per il pacchetto open source Fairlearn descrive come usarlo per valutare l'equità dei sistemi di intelligenza artificiale che si stanno compilando.  Il pacchetto open source di Fairlearn può anche offrire opzioni che consentono di attenuare o ridurre i problemi di equità osservati.  Vedere le [procedure](how-to-machine-learning-fairness-aml.md) e i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) per abilitare la valutazione dell'equità dei sistemi di intelligenza artificiale durante il training in Azure Machine Learning.


## <a name="what-is-fairness-in-machine-learning-systems"></a>Cos'è l'equità nei sistemi di Machine Learning?

>[!NOTE]
> L'equità è una sfida socio-tecnica. Molti aspetti dell'equità, come la giustizia e il processo equo, non vengono monitorati nelle metriche di equità quantitativa. Inoltre, molte metriche di equità quantitativa non possono essere soddisfatte contemporaneamente. L'obiettivo del pacchetto open source Fairlearn è consentire agli utenti di valutare le diverse strategie di riduzione e l'effetto. Infine, spetta agli utenti umani che compilano modelli di intelligenza artificiale e machine learning per creare compromessi appropriati per il proprio scenario.

I sistemi di intelligenza artificiale e Machine Learning possono mostrare un comportamento iniquo. Un metodo per definire un comportamento iniquo è considerarne il danno o l'effetto sulle persone. Ci sono molti tipi di problemi che i sistemi di IA possono far sorgere. Per altre informazioni, vedere il [Keynote NeurIPS 2017 di Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) .

Di seguito sono riportati due tipi di problemi comuni causati dall'IA:

- Danno dell'allocazione: un sistema di intelligenza artificiale estende o trattiene opportunità, risorse o informazioni per determinati gruppi. Gli esempi includono assunzioni, ammissioni scolastiche e concessioni di prestiti in cui un modello potrebbe essere decisamente migliore nella scelta dei candidati validi in un gruppo specifico di persone rispetto a quanto lo sia in altri gruppi.

- Problema di qualità del servizio: un sistema di intelligenza artificiale non funziona ugualmente bene per due gruppi di persone. Come esempio, un sistema di riconoscimento vocale potrebbe non funzionare ugualmente bene per le donne e per gli uomini.

Per ridurre i comportamenti iniqui nei sistemi di intelligenza artificiale, è necessario valutare e mitigare questi problemi.


## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Valutazione e mitigazione dell'equità con Fairlearn

Fairlearn è un pacchetto open source di Python che consente agli sviluppatori di sistemi di Machine Learning di valutare l'equità dei propri sistemi e di mitigare i problemi evidenziati.

Il pacchetto open source di Fairlearn include due componenti:

- Dashboard Valutazione: un widget del notebook di Jupyter per valutare come le stime di un modello influenzano diversi gruppi. Consente inoltre di confrontare più modelli usando metriche di equità e prestazioni.
- Algoritmi di mitigazione: un set di algoritmi per attenuare l'iniquità nella classificazione binaria e nella regressione.

Insieme, questi componenti consentono a data scientist e leader aziendali di scendere a compromessi tra equità e prestazioni e di scegliere la strategia di mitigazione più adatta alle proprie esigenze.

## <a name="fairness-assessment"></a>Valutazione dell'equità
Nel pacchetto open source di Fairlearn, l'equità viene concettuale anche se si tratta di un approccio noto come **equità del gruppo**, che chiede: quali gruppi di persone rischiano di subire danni? I gruppi rilevanti, anche noti come sottopopolazioni, vengono definiti tramite **caratteristiche sensibili** o attributi sensibili. Le funzionalità sensibili vengono passate a uno strumento di stima nel pacchetto open source Fairlearn come vettore o matrice denominata `sensitive_features` . Il termine suggerisce che la finestra di progettazione del sistema debba essere sensibile a queste caratteristiche durante la valutazione dell'equità di gruppo. 

Un aspetto da tenere presente è se queste funzionalità contengono implicazioni sulla privacy dovute a dati privati. Tuttavia, la parola "sensibile" non implica che queste caratteristiche non debbano essere usate per realizzare stime.

>[!NOTE]
> Una valutazione dell'equità non è un esercizio puramente tecnico.  Il pacchetto open source Fairlearn consente di valutare l'equità di un modello, ma non esegue la valutazione per l'utente.  Il pacchetto open source Fairlearn consente di identificare le metriche quantitative per valutare l'equità, ma anche gli sviluppatori devono eseguire un'analisi qualitativa per valutare l'equità dei propri modelli.  Le funzionalità riservate indicate sopra sono un esempio di questo tipo di analisi qualitativa.     

Durante la fase di valutazione, l'equità viene quantificata attraverso le metriche di disparità. **Le metriche di disparità** consentono di valutare e confrontare il comportamento del modello tra gruppi diversi come proporzioni o come differenze. Il pacchetto open source Fairlearn supporta due classi di metriche di disparità:


- Disparità nelle prestazioni del modello: questi set di metriche calcolano la disparità (differenza) nei valori della metrica di prestazioni selezionata tra diversi sottogruppi. Di seguito sono riportati alcuni esempi:

  - disparità nel tasso di accuratezza
  - disparità nel tasso di errore
  - disparità nella precisione
  - disparità nel richiamo
  - disparità nel MAE (errore assoluto medio)
  - e molti altri

- Disparità nel tasso di selezione: questa metrica mostra la differenza nel tasso di selezione tra diversi sottogruppi. Un esempio è rappresentato dalla disparità nel tasso di approvazione dei prestiti. Il tasso di selezione indica la frazione di punti dati in ogni classe classificata come 1 (nella classificazione binaria) o la distribuzione dei valori di stima (nella regressione).

## <a name="unfairness-mitigation"></a>Mitigazione dell'iniquità

### <a name="parity-constraints"></a>Vincoli di parità

Il pacchetto open source di Fairlearn include un'ampia gamma di algoritmi di mitigazione della correttezza. Tali algoritmi supportano un set di vincoli per il comportamento del predittore denominati **vincoli di parità** o criteri. I vincoli di parità richiedono che alcuni aspetti del comportamento del predittore siano confrontabili tra i gruppi che le caratteristiche sensibili definiscono (ad esempio diverse razze). Gli algoritmi di mitigazione nel pacchetto open source Fairlearn utilizzano tali vincoli di parità per attenuare i problemi di equità osservati.

>[!NOTE]
> La riduzione della correttezza di un modello implica la riduzione della non correttezza, ma questa mitigazione tecnica non può eliminare completamente questa non correttezza.  Gli algoritmi di mitigazione della correttezza nel pacchetto open source Fairlearn possono fornire strategie di mitigazione suggerite che consentono di ridurre la correttezza in un modello di apprendimento automatico, ma non sono soluzioni per eliminare completamente la correttezza.  Potrebbero essere presenti altri vincoli o criteri di parità da considerare per ogni modello di apprendimento automatico di uno sviluppatore specifico. Gli sviluppatori che usano Azure Machine Learning devono determinare autonomamente se la mitigazione Elimina sufficientemente la correttezza nell'uso previsto e nella distribuzione dei modelli di machine learning.  

Il pacchetto open source Fairlearn supporta i tipi di vincoli di parità seguenti: 

|Vincolo di parità  | Scopo  |Attività di Machine Learning  |
|---------|---------|---------|
|Parità demografica     |  Mitigare i problemi di allocazione | Classificazione binaria, regressione |
|Probabilità equalizzate  | Diagnosticare i problemi di allocazione e di qualità del servizio | Classificazione binaria        |
|Uguale opportunità | Diagnosticare i problemi di allocazione e di qualità del servizio | Classificazione binaria        |
|Perdita del gruppo associato     |  Mitigare i problemi di qualità del servizio | Regressione |



### <a name="mitigation-algorithms"></a>Algoritmi di mitigazione

Il pacchetto open source Fairlearn fornisce algoritmi di mitigazione della rielaborazione e riduzione della correttezza:

- Riduzione: questi algoritmi accettano uno strumento di stima di Machine Learning standard, ad esempio un modello LightGBM, e generano un set di modelli di cui è stato ripetuto il training usando una sequenza di set di impostazioni di training riponderati. Ad esempio, i richiedenti appartenenti a un determinato genere potrebbero presentare un valore ponderato più alto o più basso per eseguire nuovamente il training dei modelli e ridurre le disparità tra gruppi di diverso genere. Gli utenti possono quindi scegliere un modello che offra il miglior compromesso tra accuratezza (o altra metrica di prestazioni) e disparità, che in genere deve essere basata sulle regole business e sui calcoli dei costi.  
- Post-elaborazione: questi algoritmi prendono un classificatore esistente e la caratteristica sensibile come input. Quindi, derivano una trasformazione della stima del classificatore per l'applicazione dei vincoli di equità specificati. Il maggiore vantaggio dell'ottimizzazione della soglia è la sua semplicità e flessibilità, in quanto non richiede di ripetere il training del modello. 

| Algoritmo | Descrizione | Attività di Machine Learning | Caratteristiche sensibili | Vincoli di parità supportati | Tipo di algoritmo |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Approccio black box alla classificazione equa descritto in [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453) (Un approccio di riduzione alla classificazione equa) | Classificazione binaria | Categorical | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Riduzione |
| `GridSearch` | Approccio black box descritto in [A Reductions Approach to Fair Classification ](https://arxiv.org/abs/1803.02453)(Un approccio di riduzione alla classificazione equa)| Classificazione binaria | Binary | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Riduzione |
| `GridSearch` | Approccio black box che implementa una variante di ricerca a griglia di regressione equa con l'algoritmo per la perdita del gruppo associato descritta in [Fair Regression: Quantitative Definitions and Reduction-based Algorithms](https://arxiv.org/abs/1905.12843) (Regressione equa:definizioni quantitative e algoritmi basati sulla riduzione) | Regressione | Binary | [Perdita del gruppo associato](#parity-constraints) | Riduzione |
| `ThresholdOptimizer` | Algoritmo di post-elaborazione basato sul documento [Equality of Opportunity in Supervised Learning](https://arxiv.org/abs/1610.02413) (Eguaglianza di opportunità nell'apprendimento supervisionato). Questa tecnica accetta come input un classificatore esistente e la caratteristica sensibile e deriva una trasformazione monotona della stima del classificatore per applicare i vincoli di parità specificati. | Classificazione binaria | Categorical | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Post-elaborazione |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare i diversi componenti, vedere la pagina relativa a [GitHub](https://github.com/fairlearn/fairlearn/), [Guida dell'utente](https://fairlearn.github.io/user_guide/index.html), [esempi](https://fairlearn.github.io/auto_examples/notebooks/index.html)e [notebook di esempio](https://github.com/fairlearn/fairlearn/tree/master/notebooks)di Fairlearn.
- Informazioni [su come abilitare la](how-to-machine-learning-fairness-aml.md) valutazione dell'equità dei modelli di machine learning in Azure Machine Learning.
- Vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) per altri scenari di valutazione dell'equità in Azure Machine Learning. 
