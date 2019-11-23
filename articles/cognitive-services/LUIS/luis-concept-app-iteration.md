---
title: Iterative app design - LUIS
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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422598"
---
# <a name="iterative-app-design-for-luis"></a>Iterative app design for LUIS

A Language Understanding (LUIS) app learns and performs most efficiently with iteration. Here's a typical iteration cycle:

* Create new version
* Edit the LUIS app schema. Sono inclusi:
    * Intents with example utterances
    * Entità
    * database elastico
* Train, test, and publish
    * Test at the prediction endpoint for active learning
* Gather data from endpoint queries

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Building a LUIS schema

An app's schema defines what the user is asking for (the _intention_ or _intent_ ) and what parts of the intent provide details (called _entities_) that are used to help determine the answer. 

The app schema must be specific to the app domains to determine words and phrases that are relevant, as well as to determine typical word ordering. 

Example utterances represent user inputs, such as recognized speech or text, that the app expects at runtime. 

The schema requires intents, and _should have_ entities. 

### <a name="example-schema-of-intents"></a>Example schema of intents

The most common schema is an intent schema organized with intents. This type of schema uses LUIS to determine a user's intention. 

The intent schema type may have entities if it helps LUIS determine the user's intention. For example, a shipping entity (as a descriptor to an intent) helps LUIS determine a shipping intention. 

### <a name="example-schema-of-entities"></a>Example schema of entities

An entity schema focuses on entities, which is the data that is extracted from user utterances. For example, if a user was to say, "I'd like to order three pizzas." There are two entities that would be extracted: _three_ and _pizzas_. These are used to help fulfill the intention, which was to make an order. 

For an entity schema, the intention of the utterance is less important to the client application. 

A common method of organizing an entity schema is to add all example utterances to the **None** intent. 

### <a name="example-of-a-mixed-schema"></a>Example of a mixed schema

The most powerful and mature schema is an intent schema with a full range of entities and features. This schema can begin as either an intent or entity schema and grow to include concepts of both, as the client application needs those pieces of information. 

## <a name="add-example-utterances-to-intents"></a>Add example utterances to intents

LUIS needs a few example utterances in each **intent**. The example utterances need enough variation of word choice and word order to be able to determine which intent the utterance is meant for. 

> [!CAUTION]
> Do not add example utterances in bulk. Start with 15 to 30 specific and varying examples. 

Each example utterance needs to have any **required data to extract** designed and labeled with **entities**. 

|Key element|Finalità|
|--|--|
|Finalità|**Classify** user utterances into a single intention, or action. Gli esempi includono `BookFlight` e `GetWeather`.|
|Persona giuridica|**Extract** data from utterance required to complete intention. Examples include date and time of travel, and location.|

A LUIS app can be designed to ignore utterances that aren't relevant to an app's domain by assigning the utterance to the **None** intent.

## <a name="test-and-train-your-app"></a>Test and train your app

After you have 15 to 30 different example utterances in each intent, with the required entities labeled, you need to test and [train](luis-how-to-train.md) your LUIS app. 

## <a name="publish-to-a-prediction-endpoint"></a>Publish to a prediction endpoint

The LUIS app must be published so that it's available to you in the list [prediction endpoint regions](luis-reference-regions.md).

## <a name="test-your-published-app"></a>Testare l'app pubblicata

You can test your published LUIS app from the HTTPS prediction endpoint. Testing from the prediction endpoint allows LUIS to choose any utterances with low-confidence for [review](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Create a new version for each cycle

Each version is a snapshot in time of the LUIS app. Prima di apportare modifiche all'app, creare una nuova versione. It is easier to go back to an older version than to try to remove intents and utterances to a previous state.

L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

La versione iniziale (0.1) è la versione attiva predefinita. 

### <a name="begin-by-cloning-an-existing-version"></a>Begin by cloning an existing version

Clone an existing version to use as a starting point for each new version. After you clone a version, the new version becomes the **active** version. 

### <a name="publishing-slots"></a>Publishing slots

You can publish to either the stage and/or production slots. Ogni slot può presentare una versione diversa o la stessa versione. This is useful for verifying changes before publishing to production, which is available to bots or other LUIS calling apps. 

Trained versions aren't automatically available at your LUIS app's [endpoint](luis-glossary.md#endpoint). You must [publish](luis-how-to-publish-app.md) or republish a version in order for it to be available at your LUIS app endpoint. You can publish to **Staging** and **Production**, giving you two versions of the app available at the endpoint. If more versions of the app need to be available at an endpoint, you should export the version and reimport it to a new app. La nuova app presenta un ID app diverso.

### <a name="import-and-export-a-version"></a>Importare ed esportare una versione

A version can be imported at the app level. That version becomes the active version and uses the version ID in the `versionId` property of the app file. You can also import into an existing app, at the version level. La nuova versione diventa quella attiva. 

A version can be exported at the app or version level as well. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

The exported file **doesn't** contain:

* Machine-learned information, because the app is retrained after it's imported
* Contributor information

In order to back up your LUIS app schema, export a version from the [LUIS portal](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Manage contributor changes with versions and contributors

LUIS uses the concept of contributors to an app, by providing Azure resource-level permissions. Combine this concept with versioning to provide targeted collaboration. 

Use the following techniques to manage contributor changes to your app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app

Begin by [cloning](luis-how-to-manage-versions.md#clone-a-version) from a base version for each author. 

Each author makes changes to their own version of the app. When the author is satisfied with the model, export the new versions to JSON files.  

Exported apps, .json or .lu files, can be compared for changes. Combine the files to create a single file of the new version. Change the `versionId` property to signify the new merged version. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. You can compare the results of the active version with a published version (stage or production) in the [interactive testing pane](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app

[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

Learn more about authoring contributions from [collaborators](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Review endpoint utterances to begin the new iterative cycle

When you are done with an iteration cycle, you can repeat the process. Start with [reviewing prediction endpoint utterances](luis-how-to-review-endpoint-utterances.md) LUIS marked with low-confidence. Check these utterances for both correct predicted intent and correct and complete entity extracted. After you review and accept changes, the review list should be empty.  

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-keys.md).
