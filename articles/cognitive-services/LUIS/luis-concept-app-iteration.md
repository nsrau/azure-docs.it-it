---
title: Progettazione di app iterative-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256923"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo di creazione per l'app LUIS
LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint. 

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Creazione di un modello LUIS
Lo scopo del modello è capire ciò che l'utente sta chiedendo (finalità) e quali parti della domanda forniscono i dettagli (entità) necessari a determinare la risposta. 

Il modello deve essere specifico del dominio dell'app per determinare le parole e le frasi rilevanti, oltre all'ordine tipico delle parole. 

Il modello richiede Intent e _deve avere_ entità. 

## <a name="add-training-examples"></a>Aggiungere esempi di training
LUIS necessita di espressioni di esempio nelle finalità. Gli esempi richiedono un numero sufficiente di variazioni nella scelta e nell'ordine delle parole per poter determinare la finalità dell'espressione. Per ogni espressione di esempio tutti i dati necessari devono essere etichettati come entità. 

Indicare a LUIS di ignorare le espressioni non rilevanti per il dominio dell'app assegnando l'espressione alla finalità **None** (Nessuna). Eventuali parole o frasi che non è necessario estrarre da un'espressione non devono essere etichettate. Non è prevista alcuna etichetta per le parole o le frasi da ignorare. 

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla
Quando si hanno da 15 a 30 espressioni diverse per ogni finalità, con le entità obbligatorie etichettate, è necessario eseguire il [Training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md). Dalla notifica di esito positivo della pubblicazione, usare il collegamento per ottenere gli endpoint. Assicurarsi di creare e pubblicare l'app in modo che sia disponibile nelle aree dell' [endpoint](luis-reference-regions.md) necessarie. 

## <a name="https-prediction-endpoint-testing"></a>Test dell'endpoint di stima HTTPS
È possibile testare l'app LUIS dall'endpoint di stima HTTPS. Il test dall'endpoint di stima consente a LUIS di scegliere eventuali espressioni con una bassa confidenza per la [Revisione](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Ricicla

Dopo aver completato un ciclo di creazione, è possibile ricominciare. Iniziare a esaminare [gli enunciati dell'endpoint di stima](luis-how-to-review-endpoint-utterances.md) Luis contrassegnati con una bassa confidenza. Controllare le espressioni a livello di finalità e di entità. Dopo aver rivisto le espressioni, l'elenco di revisione dovrebbe risultare vuoto.  

Provare a [clonare](luis-concept-version.md#clone-a-version) la versione corrente in una nuova versione, quindi iniziare le modifiche di modifica nella nuova versione. 

## <a name="batch-testing"></a>Test in batch

Il [test batch](luis-concept-batch-test.md) è un modo per vedere il numero di espressioni di esempio con punteggio di Luis. Gli esempi devono essere nuovi per LUIS e devono essere etichettati correttamente con la finalità e le entità che LUIS deve trovare. I risultati del test indicano le prestazioni di LUIS relativamente a quel set di espressioni. 

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-keys.md).
