---
title: Valutare e mitigare l'equità nei modello di Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni sull'equità nei modelli di Machine Learning e sul modo in cui il pacchetto Fairlearn Python può aiutare a creare modelli più equi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596360"
---
# <a name="fairness-in-machine-learning-models"></a>Equità nei modelli di Machine Learning

Informazioni sull'equità nel Machine Learning e sul modo in cui il pacchetto open source Fairlearn Python può aiutare a creare modelli più equi.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Cos'è l'equità nei sistemi di Machine Learning?

I sistemi di intelligenza artificiale e Machine Learning possono mostrare un comportamento iniquo. Un metodo per definire un comportamento iniquo è considerarne il danno o l'effetto sulle persone. Ci sono molti tipi di problemi che i sistemi di IA possono far sorgere. Di seguito sono riportati due tipi di problemi comuni causati dall'IA:

- Problema di allocazione: un sistema di intelligenza artificiale estende o trattiene opportunità, risorse o informazioni. Gli esempi includono assunzioni, ammissioni scolastiche e concessioni di prestiti in cui un modello potrebbe essere decisamente migliore nella scelta dei candidati validi in un gruppo specifico di persone rispetto a quanto lo sia in altri gruppi.

- Problema di qualità del servizio: un sistema di intelligenza artificiale non funziona ugualmente bene per due gruppi di persone. Come esempio, un sistema di riconoscimento vocale potrebbe non funzionare ugualmente bene per le donne e per gli uomini.

Per ridurre i comportamenti iniqui nei sistemi di intelligenza artificiale, è necessario valutare e mitigare questi problemi.

>[!NOTE]
> L'equità è una sfida socio-tecnica. Molti aspetti dell'equità, come la giustizia e il processo equo, non vengono monitorati nelle metriche di equità quantitativa. Inoltre, molte metriche di equità quantitativa non possono essere soddisfatte contemporaneamente. L'obiettivo è consentire agli utenti di valutare diverse strategie di mitigazione e quindi di fare compromessi appropriati allo scenario.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Valutazione e mitigazione dell'equità con Fairlearn

Fairlearn è un pacchetto open source di Python che consente agli sviluppatori di sistemi di Machine Learning di valutare l'equità dei propri sistemi e di mitigare i problemi evidenziati.

Fairlearn presenta due componenti:

- Dashboard Valutazione: un widget del notebook di Jupyter per valutare come le stime di un modello influenzano diversi gruppi. Consente inoltre di confrontare più modelli usando metriche di equità e prestazioni.
- Algoritmi di mitigazione: un set di algoritmi per attenuare l'iniquità nella classificazione binaria e nella regressione.

Insieme, questi componenti consentono a data scientist e leader aziendali di scendere a compromessi tra equità e prestazioni e di scegliere la strategia di mitigazione più adatta alle proprie esigenze.

## <a name="fairness-assessment"></a>Valutazione dell'equità

In Fairlearn l'equità viene concettualizzata tramite un approccio noto come **equità di gruppo**, che si chiede: Quali gruppi di persone rischiano di incorrere in problemi?

I gruppi rilevanti, anche noti come sottopopolazioni, vengono definiti tramite **caratteristiche sensibili** o attributi sensibili. Le caratteristiche sensibili vengono passate a un oggetto estimator di Fairlearn come vettore o matrice denominato `sensitive_features`. Il termine suggerisce che la finestra di progettazione del sistema debba essere sensibile a queste caratteristiche durante la valutazione dell'equità di gruppo. Un aspetto da tenere presente è se tali caratteristiche comportano implicazioni sulla privacy dovute a informazioni personali. Tuttavia, la parola "sensibile" non implica che queste caratteristiche non debbano essere usate per realizzare stime.

Durante la fase di valutazione, l'equità viene quantificata attraverso le metriche di disparità. **Le metriche di disparità** consentono di valutare e confrontare il comportamento del modello tra gruppi diversi come proporzioni o come differenze. Fairlearn supporta due classi di metriche di disparità:


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

Fairlearn include un'ampia gamma di algoritmi di mitigazione dell'iniquità. Tali algoritmi supportano un set di vincoli per il comportamento del predittore denominati **vincoli di parità** o criteri. I vincoli di parità richiedono che alcuni aspetti del comportamento del predittore siano confrontabili tra i gruppi che le caratteristiche sensibili definiscono (ad esempio diverse razze). Gli algoritmi di mitigazione di Fairlearn usano suddetti vincoli di parità per mitigare i problemi di equità osservati.

Fairlearn supporta i seguenti tipi di vincoli di parità:

|Vincolo di parità  | Scopo  |Attività di Machine Learning  |
|---------|---------|---------|
|Parità demografica     |  Mitigare i problemi di allocazione | Classificazione binaria, regressione |
|Probabilità equalizzate  | Diagnosticare i problemi di allocazione e di qualità del servizio | Classificazione binaria        |
|Perdita del gruppo associato     |  Mitigare i problemi di qualità del servizio | Regressione |

### <a name="mitigation-algorithms"></a>Algoritmi di mitigazione

Fairlearn offre algoritmi di mitigazione dell'iniquità per riduzione e post-elaborazione:

- Riduzione: questi algoritmi prendono un oggetto estimator ML black box standard (ad esempio un modello LightGBM) e generano un set di modelli sottoposti nuovamente a training usando una sequenza di set di dati di training riponderata. Ad esempio, i richiedenti appartenenti a un determinato genere potrebbero presentare un valore ponderato più alto o più basso per eseguire nuovamente il training dei modelli e ridurre le disparità tra gruppi di diverso genere. Gli utenti possono quindi scegliere un modello che offra il miglior compromesso tra accuratezza (o altra metrica di prestazioni) e disparità, che in genere deve essere basata sulle regole business e sui calcoli dei costi.  
- Post-elaborazione: questi algoritmi prendono un classificatore esistente e la caratteristica sensibile come input. Quindi, derivano una trasformazione della stima del classificatore per l'applicazione dei vincoli di equità specificati. Il maggiore vantaggio dell'ottimizzazione della soglia è la sua semplicità e flessibilità, in quanto non richiede di ripetere il training del modello. 

| Algoritmo | Descrizione | Attività di Machine Learning | Caratteristiche sensibili | Vincoli di parità supportati | Tipo di algoritmo |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Approccio black box alla classificazione equa descritto in [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453) (Un approccio di riduzione alla classificazione equa) | Classificazione binaria | Categorical | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Riduzione |
| `GridSearch` | Approccio black box descritto in [A Reductions Approach to Fair Classification ](https://arxiv.org/abs/1803.02453)(Un approccio di riduzione alla classificazione equa)| Classificazione binaria | Binary | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Riduzione |
| `GridSearch` | Approccio black box che implementa una variante di ricerca a griglia di regressione equa con l'algoritmo per la perdita del gruppo associato descritta in [Fair Regression: Quantitative Definitions and Reduction-based Algorithms](https://arxiv.org/abs/1905.12843) (Regressione equa:definizioni quantitative e algoritmi basati sulla riduzione) | Regressione | Binary | [Perdita del gruppo associato](#parity-constraints) | Riduzione |
| `ThresholdOptimizer` | Algoritmo di post-elaborazione basato sul documento [Equality of Opportunity in Supervised Learning](https://arxiv.org/abs/1610.02413) (Eguaglianza di opportunità nell'apprendimento supervisionato). Questa tecnica accetta come input un classificatore esistente e la caratteristica sensibile e deriva una trasformazione monotona della stima del classificatore per applicare i vincoli di parità specificati. | Classificazione binaria | Categorical | [Parità demografica](#parity-constraints), [probabilità equalizzate](#parity-constraints) | Post-elaborazione |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare i diversi componenti, vedere il [repository GitHub Fairlearn](https://github.com/fairlearn/fairlearn/) e i [notebook di esempio](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Informazioni su come preservare la privacy dei dati usando la [privacy differenziale e il pacchetto WhisperNoise](concept-differential-privacy.md).