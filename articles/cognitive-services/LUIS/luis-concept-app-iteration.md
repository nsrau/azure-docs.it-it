---
title: Progettazione di app iterativa
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813782"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo di creazione per l'app LUIS
LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint. 

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Creazione di un modello LUIS
Lo scopo del modello è capire ciò che l'utente sta chiedendo (finalità) e quali parti della domanda forniscono i dettagli (entità) necessari a determinare la risposta. 

Il modello deve essere specifico del dominio dell'app per determinare le parole e le frasi rilevanti, oltre all'ordine tipico delle parole. 

Il modello include finalità ed entità. 

## <a name="add-training-examples"></a>Aggiungere esempi di training
LUIS necessita di espressioni di esempio nelle finalità. Gli esempi richiedono un numero sufficiente di variazioni nella scelta e nell'ordine delle parole per poter determinare la finalità dell'espressione. Per ogni espressione di esempio tutti i dati necessari devono essere etichettati come entità. 

Indicare a LUIS di ignorare le espressioni non rilevanti per il dominio dell'app assegnando l'espressione alla finalità **None** (Nessuna). Eventuali parole o frasi che non è necessario estrarre da un'espressione non devono essere etichettate. Non è prevista alcuna etichetta per le parole o le frasi da ignorare. 

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla
Quando si dispone di 10 - 15 diverse espressioni per ciascuna finalità, e le entità necessarie sono etichettate, eseguire il training, quindi pubblicare. Dalla notifica di esito positivo della pubblicazione, usare il collegamento per ottenere gli endpoint. Assicurarsi di creare l'app e di pubblicarla affinché sia disponibile nelle [regioni endpoint](luis-reference-regions.md) necessarie. 

## <a name="https-endpoint-testing"></a>Test dall'endpoint HTTPS
È possibile eseguire il test dell'app LUIS dall'endpoint HTTPS. Il test dall'endpoint consente a LUIS di scegliere qualsiasi espressione con bassa probabilità di revisione.  

## <a name="recycle"></a>Ripetere il ciclo
Dopo aver completato un ciclo di creazione, è possibile ricominciare. Rivedere innanzitutto le espressioni endpoint che LUIS ha contrassegnato con probabilità bassa. Controllare le espressioni a livello di finalità e di entità. Dopo aver rivisto le espressioni, l'elenco di revisione dovrebbe risultare vuoto.  

## <a name="batch-testing"></a>Test in batch
Il test in batch è un modo per stabilire a quante espressioni di esempio LUIS assegna un punteggio. Gli esempi devono essere nuovi per LUIS e devono essere etichettati correttamente con la finalità e le entità che LUIS deve trovare. I risultati del test indicano le prestazioni di LUIS relativamente a quel set di espressioni. 

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-collaborator.md).
