---
title: Usare il metodo di valutazione offline-personalizzatore
titleSuffix: Azure Cognitive Services
description: Creare un ciclo di feedback in questa guida di avvio rapido in C# con il servizio Personalizza esperienze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f382c07dea75cb534632061c986dd8044c4aafa9
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515577"
---
# <a name="offline-evaluation"></a>Valutazione offline

La valutazione offline è un metodo che consente di testare e valutare l'efficacia del servizio Personalizza esperienze senza cambiare il codice o influire sull'esperienza utente. Vengono usati dati passati, inviati dall'applicazione all'API Classifica, per confrontare le prestazioni delle diverse classifiche.

La valutazione offline viene eseguita su un intervallo di date, che si può estendere fino alla data corrente. Il numero di giorni inclusi nell'intervallo non può essere maggiore di quello specificato per la [conservazione dei dati](how-to-settings.md).

La valutazione offline può essere utile per rispondere alle domande seguenti:

* Quanto sono efficaci le classifiche di Personalizza esperienze ai fini di una corretta personalizzazione?
    * Quali sono in media le ricompense ottenute con i criteri di Machine Learning online di Personalizza esperienze?
    * Quanto è efficace Personalizza esperienze rispetto ai risultati che si sarebbero ottenuti per impostazione predefinita con l'applicazione?
    * Quale sarebbe stata l'efficacia comparativa di una scelta casuale per la personalizzazione?
    * Quale sarebbe stata l'efficacia comparativa di diversi criteri di apprendimento specificati manualmente?
* Quali caratteristiche del contesto contribuiscono di più o di meno a una corretta personalizzazione?
* Quali caratteristiche delle azioni contribuiscono di più o di meno a una corretta personalizzazione?

Inoltre, la valutazione offline può essere usata per individuare i criteri di apprendimento ottimizzati che è possibile applicare per migliorare i risultati di Personalizza esperienze in futuro.

Le valutazioni offline non forniscono indicazioni per quanto riguarda la percentuale di eventi da usare per l'esplorazione.

## <a name="prerequisites-for-offline-evaluation"></a>Prerequisiti della valutazione offline

Di seguito sono riportati gli aspetti importanti da considerare per una valutazione offline rappresentativa:

* Una quantità sufficiente di dati. Il minimo consigliato è di almeno 50.000 eventi.
* Raccogliere i dati da periodi con valori rappresentativi riguardo al comportamento degli utenti e al traffico.

## <a name="discovering-the-optimized-learning-policy"></a>Individuazione dei criteri di apprendimento ottimizzati

È possibile usare il processo di valutazione offline per individuare automaticamente i criteri di apprendimento ottimali per Personalizza esperienze.

Dopo aver eseguito la valutazione offline, è possibile verificare l'efficacia comparativa di Personalizza esperienze con i nuovi criteri rispetto ai criteri online correnti. È quindi possibile applicare i criteri di apprendimento per renderli immediatamente effettivi in personalizzatore, scaricando e caricando questi criteri nel pannello modelli e criteri. È anche possibile scaricarlo per un'analisi o un uso futuro.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Identificazione della pertinenza dei risultati della valutazione offline

Quando si esegue una valutazione offline, è particolarmente importante analizzare i _limiti di confidenza_ dei risultati. Se sono ampi, significa che l'applicazione non ha ricevuto una quantità sufficiente di dati per ricavare una stima delle ricompense precisa o significativa. Quando vengono accumulati più dati nel sistema e se si eseguono valutazioni offline su periodi prolungati di tempo, gli intervalli di confidenza si restringono.

## <a name="how-offline-evaluations-are-done"></a>Come vengono eseguite le valutazioni offline

Le valutazioni offline vengono eseguite secondo il metodo di **valutazione controfattuale**. 

Personalizza esperienze si basa sul presupposto che i comportamenti degli utenti (e di conseguenza le ricompense) siano impossibili da prevedere retroattivamente (non si può sapere cosa sarebbe successo se i contenuti mostrati all'utente fossero stati diversi) e che per l'apprendimento si possano usare solo ricompense misurate. 

Il processo concettuale usato per le valutazioni è il seguente:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Per la valutazione offline viene usato solo il comportamento osservato degli utenti. I grandi volumi di dati vengono ignorati, soprattutto se l'applicazione effettua chiamate a Classifica con un numero elevato di azioni.


## <a name="evaluation-of-features"></a>Valutazione delle caratteristiche

Le valutazioni offline possono fornire informazioni sul livello di incisività delle specifiche caratteristiche per azioni o contesto ai fini di ricompense più alte. Le informazioni vengono calcolate eseguendo la valutazione rispetto al periodo di tempo e ai dati specificati e possono variare nel corso del tempo.

È consigliabile esaminare le valutazioni delle caratteristiche e trovare una risposta a queste domande:

* Quali altre caratteristiche aggiuntive potrebbero essere fornite dall'applicazione o dal sistema sulla falsariga di quelle più efficaci?
* Quali caratteristiche possono essere rimosse in quanto poco efficaci? Le caratteristiche poco efficaci aggiungono _rumore_ nel processo di Machine Learning.
* Alcune caratteristiche sono state incluse per errore? Si tratta ad esempio di informazioni personali, ID duplicati e così via.
* Sono state incluse caratteristiche indesiderate che non dovrebbero essere usate per la personalizzazione a causa di considerazioni sull'uso responsabile o per motivi legali? Alcune caratteristiche potrebbero rispecchiare o essere correlate a caratteristiche indesiderate?


## <a name="next-steps"></a>Passaggi successivi

[Configurare la personalizzazione](how-to-settings.md) 
[eseguire valutazioni offline](how-to-offline-evaluation.md) informazioni sul [funzionamento della personalizzazione](how-personalizer-works.md)
