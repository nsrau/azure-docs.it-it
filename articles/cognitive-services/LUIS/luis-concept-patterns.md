---
title: Patterns Help Prediction-LUIS
titleSuffix: Azure Cognitive Services
description: Un modello consente di ottenere maggiore accuratezza in relazione a una finalità senza fornire molte altre espressioni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 32b9b42ee0962353405616e501de95b19e40ea1a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280848"
---
# <a name="patterns-improve-prediction-accuracy"></a>Migliorare l'accuratezza della stima con i criteri
I criteri sono progettati per migliorare l'accuratezza quando vi sono più espressioni molto simili.  Un modello consente di ottenere maggiore accuratezza in relazione a una finalità senza fornire molte altre espressioni. 

## <a name="patterns-solve-low-intent-confidence"></a>I criteri risolvono il problema dell'attendibilità ridotta della finalità
Si consideri un'app di risorse umane che genera report nel grafico aziendale su un dipendente. Dato un nome e una relazione dipendente, LUIS restituisce i dipendenti coinvolti. Si consideri un dipendente, Tom, con un manager di nome Alice, e un team di sottoposti che si chiamano Michael, Rebecca e Carl.

![Immagine del grafico aziendale](./media/luis-concept-patterns/org-chart.png)

|Espressioni|Finalità stimata|Punteggio finalità|
|--|--|--|
|Chi è il sottoposto di Tom?|GetOrgChart|.30|
|Come si chiama il sottoposto di Tom?|GetOrgChart|.30|

Se un'app ha tra le 10 e le 20 espressioni con diverse lunghezze di frase, diverso ordine di parole e persino diverse parole (sinonimi di "sottoposto", "gestire", "report"), LUIS può restituire un punteggio corrispondente a un'attendibilità bassa. Creare un modello per aiutare LUIS a comprendere l'importanza dell'ordine delle parole. 

I criteri risolvono le situazioni seguenti: 

* Il Punteggio preventivo è basso
* Lo scopo corretto non è il punteggio superiore ma troppo vicino al punteggio superiore. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>I criteri non sono una garanzia della finalità
I criteri usano una combinazione di tecnologie di stima. L'impostazione di una finalità per l'espressione di un modello in un criterio non è una garanzia per la stima della finalità ma è un segnale. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>I modelli non migliorano il rilevamento delle entità acquisite dal computer

Un modello è concepito principalmente per aiutare la stima di Intent e ruoli. _Modello. qualsiasi_ entità viene utilizzata per estrarre le entità in formato libero. Sebbene i modelli usino le entità, un modello non consente di rilevare un'entità appresa dal computer.  

Non aspettarsi un miglioramento della stima delle entità se si comprimono più espressioni in un singolo criterio. Per l'attivazione di entità semplici, è necessario aggiungere espressioni o usare le entità dell'elenco altrimenti il modello non viene attivato.

## <a name="patterns-use-entity-roles"></a>I criteri usano i ruoli delle entità
Se due o più entità in un criterio hanno una relazione di tipo contestuale, i criteri usano i [ruoli](luis-concept-roles.md) delle entità per estrarre informazioni contestuali sull'entità.  

## <a name="prediction-scores-with-and-without-patterns"></a>Punteggi di stima con e senza criteri
Con un numero sufficiente di espressioni di esempio, LUIS dovrebbe poter aumentare l'attendibilità delle stime anche senza criteri. I criteri aumentano il punteggio di attendibilità senza dovere specificare un numero così elevato di espressioni.  

## <a name="pattern-matching"></a>Corrispondenza dei criteri
Per cercare una corrispondenza tra criteri, prima vengono rilevate le entità all'interno del criterio, poi vengono convalidate le parole restanti e l'ordine delle parole del criterio. Le entità nel criterio sono necessarie perché venga individuata una corrispondenza del criterio. Il criterio viene applicato a livello di token, non a livello di carattere. 

## <a name="pattern-only-apps"></a>App solo modello
È possibile compilare un'app con Intent senza espressioni di esempio, purché esista un modello per ogni finalità. Per un'app solo modello, il modello non deve contenere entità apprese dal computer perché queste richiedono espressioni di esempio. 

## <a name="best-practices"></a>Procedure consigliate
Informazioni sulle [procedure consigliate](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Sintassi dei criteri

Informazioni sulla sintassi dei modelli dalla Guida di [riferimento alla sintassi](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui modelli:

* [Come aggiungere modelli](luis-how-to-model-intent-pattern.md)
* [Come aggiungere pattern. any Entity](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Sintassi degli schemi](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Esegui l'esercitazione per implementare i criteri](luis-tutorial-pattern.md)
