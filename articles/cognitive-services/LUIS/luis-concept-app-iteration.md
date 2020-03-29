---
title: Progettazione di app iterative - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422598"
---
# <a name="iterative-app-design-for-luis"></a>Progettazione di app iterative per LUIS

Un'app LuiS (Language Understanding) app appsa e funziona in modo più efficiente con l'iterazione. Ecco un ciclo di iterazione tipico:Here's a typical iteration cycle:

* Crea nuova versione
* Modificare lo schema dell'app LUIS. ad esempio:
    * Intenti con espressioni di esempioIntents with example utterances
    * Entità
    * Funzionalità
* Addestrare, testare e pubblicare
    * Test nell'endpoint di stima per l'apprendimento attivoTest at the prediction endpoint for active learning
* Raccogliere dati dalle query endpoint

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Creazione di uno schema LUISBuilding a LUIS schema

Lo schema di un'app definisce ciò che l'utente richiede _(l'intenzione_ o _l'intenzione)_ e quali parti della finalità forniscono dettagli (denominate _entità)_ utilizzate per determinare la risposta. 

Lo schema dell'app deve essere specifico per i domini dell'app per determinare le parole e le frasi che sono rilevanti, nonché per determinare l'ordine tipico delle parole. 

Espressioni di esempio rappresentano gli input dell'utente, ad esempio il riconoscimento vocale o il testo, che l'app si aspetta in fase di esecuzione. 

Lo schema richiede finalità e _deve avere_ entità. 

### <a name="example-schema-of-intents"></a>Schema di esempio delle finalitàExample schema of intents

Lo schema più comune è uno schema di finalità organizzato con finalità. Questo tipo di schema utilizza LUIS per determinare l'intenzione di un utente. 

Il tipo di schema intento può avere entità se aiuta LUIS a determinare l'intenzione dell'utente. Ad esempio, un'entità di spedizione (come descrittore di una finalità) consente a LUIS di determinare un'intenzione di spedizione. 

### <a name="example-schema-of-entities"></a>Schema di esempio delle entitàExample schema of entities

Uno schema di entità si concentra sulle entità, ovvero i dati estratti dalle espressioni utente. Ad esempio, se un utente deve dire: "Vorrei ordinare tre pizze". Ci sono due entità che verrebbero estratte: _tre_ e _pizze_. Questi sono utilizzati per aiutare a soddisfare l'intenzione, che era quello di fare un ordine. 

Per uno schema di entità, l'intenzione dell'espressione è meno importante per l'applicazione client. 

Un metodo comune per organizzare uno schema di entità consiste nell'aggiungere tutte le espressioni di esempio alla finalità **None.A** common method of organizing an entity schema is to add all example utterances to the None intent. 

### <a name="example-of-a-mixed-schema"></a>Esempio di schema misto

Lo schema più potente e maturo è uno schema di finalità con una gamma completa di entità e funzionalità. Questo schema può iniziare come finalità o schema di entità e crescere fino a includere concetti di entrambi, in quanto l'applicazione client richiede tali informazioni. 

## <a name="add-example-utterances-to-intents"></a>Aggiungere espressioni di esempio alle finalitàAdd example utterances to intents

LUIS richiede alcune espressioni di esempio in ogni **finalità**. Le espressioni di esempio richiedono una variazione sufficiente della scelta delle parole e dell'ordine delle parole per poter determinare a quale intento è destinata l'espressione. 

> [!CAUTION]
> Non aggiungere espressioni di esempio in blocco. Iniziare con 15-30 esempi specifici e diversi. 

Ogni espressione di esempio deve disporre di tutti i **dati necessari da estrarre** progettati ed etichettati con **entità**. 

|Key - elemento|Scopo|
|--|--|
|Finalità|**Classificare le** espressioni utente in un'unica intenzione o azione. Gli esempi includono `BookFlight` e `GetWeather`.|
|Entità|**Estrarre** i dati dall'espressione necessaria per completare l'intenzione. Gli esempi includono la data e l'ora del viaggio e il luogo.|

Un'app LUIS può essere progettata per ignorare le espressioni che non sono rilevanti per il dominio di un'app assegnando l'espressione allo scopo **None.**

## <a name="test-and-train-your-app"></a>Testare e addestrare l'app

Dopo aver conto da 15 a 30 espressioni di esempio diverse in ogni finalità, con le entità necessarie etichettate, è necessario testare ed eseguire il [training](luis-how-to-train.md) dell'app LUIS. 

## <a name="publish-to-a-prediction-endpoint"></a>Pubblicare in un endpoint di stimaPublish to a prediction endpoint

L'app LUIS deve essere pubblicata in modo che sia disponibile nelle [aree endpoint di stima](luis-reference-regions.md)dell'elenco.

## <a name="test-your-published-app"></a>Testare l'app pubblicata

È possibile testare l'app LUIS pubblicata dall'endpoint di stima HTTPS. Il test dall'endpoint di stima consente a LUIS di scegliere eventuali espressioni con bassa attendibilità per la [revisione.](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>Creare una nuova versione per ogni cicloCreate a new version for each cycle

Ogni versione è uno snapshot nel tempo dell'app LUIS. Prima di apportare modifiche all'app, creare una nuova versione. È più facile tornare a una versione precedente che tentare di rimuovere finalità ed espressioni a uno stato precedente.

L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

La versione iniziale (0.1) è la versione attiva predefinita. 

### <a name="begin-by-cloning-an-existing-version"></a>Iniziare clonando una versione esistente

Clonare una versione esistente da utilizzare come punto di partenza per ogni nuova versione. Dopo aver clonato una versione, la nuova versione diventa la versione **attiva.** 

### <a name="publishing-slots"></a>Slot di pubblicazione

È possibile pubblicare negli stabilimenti di fase e/o di produzione. Ogni slot può presentare una versione diversa o la stessa versione. Ciò è utile per verificare le modifiche prima della pubblicazione nell'ambiente di produzione, disponibile per i bot o altre app chiamante LUIS. 

Le versioni di gestione non sono disponibili automaticamente [nell'endpoint](luis-glossary.md#endpoint)dell'app LUIS. È necessario [pubblicare](luis-how-to-publish-app.md) o ripubblicare una versione per renderlo disponibile nell'endpoint dell'app LUIS. È possibile pubblicare in **Gestione temporanea** e **produzione**, fornendo due versioni dell'app disponibili nell'endpoint. Se più versioni dell'app devono essere disponibili in un endpoint, è necessario esportare la versione e reimportarla in una nuova app. La nuova app presenta un ID app diverso.

### <a name="import-and-export-a-version"></a>Importare ed esportare una versione

Una versione può essere importata a livello di app. Tale versione diventa la versione attiva e `versionId` usa l'ID di versione nella proprietà del file dell'app. Puoi anche importare in un'app esistente, a livello di versione. La nuova versione diventa quella attiva. 

Una versione può essere esportata anche a livello di app o di versione. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

Il file esportato **non** contiene:

* Informazioni apprese automaticamente, perché l'app viene riaddestrata dopo l'importazione
* Informazioni per i collaboratori

Per eseguire il backup dello schema dell'app LUIS, esportare una versione dal [portale LUIS.](https://www.luis.ai/applications)

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gestire le modifiche dei collaboratori con versioni e collaboratori

LUIS usa il concetto di collaboratori a un'app, fornendo autorizzazioni a livello di risorsa di Azure.LUIS uses the concept of contributors to an app, by providing Azure resource-level permissions. Combina questo concetto con il controllo delle versioni per fornire una collaborazione mirata. 

Usa le tecniche seguenti per gestire le modifiche apportate ai collaboratori della tua app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app

Iniziare [clonando](luis-how-to-manage-versions.md#clone-a-version) da una versione di base per ogni autore. 

Ogni autore apporta modifiche alla propria versione dell'app. Quando l'autore è soddisfatto del modello, esportare le nuove versioni in file JSON.  

Le app esportate, file .json o .lu, possono essere confrontate per le modifiche. Combinare i file per creare un singolo file della nuova versione. Modificare `versionId` la proprietà per indicare la nuova versione unita. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. È possibile confrontare i risultati della versione attiva con una versione pubblicata (fase o produzione) nel [riquadro di test interattivo.](luis-interactive-test.md)

### <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app

[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

Ulteriori informazioni sulla creazione di contributi da [parte di collaboratori](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Esaminare le espressioni endpoint per iniziare il nuovo ciclo iterativoReview endpoint utterances to begin the new iterative cycle

Al termine di un ciclo di iterazione, è possibile ripetere il processo. Iniziare con la revisione delle [espressioni dell'endpoint](luis-how-to-review-endpoint-utterances.md) di stima LUIS contrassegnate con scarsa attendibilità. Controllare queste espressioni sia per la finalità prevista corretta che per l'entità corretta e completa estratta. Dopo aver esaminato e accettato le modifiche, l'elenco di revisione dovrebbe essere vuoto.  

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-keys.md).
