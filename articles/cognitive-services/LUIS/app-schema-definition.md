---
title: Definizione dello schema dell'app
description: L'app LUIS è rappresentata in `.json` o `.lu` e include tutti gli Intent, le entità, le espressioni di esempio, le funzionalità e le impostazioni.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327324"
---
# <a name="app-schema-definition"></a>Definizione dello schema dell'app

L'app LUIS è rappresentata in `.json` o `.lu` e include tutti gli Intent, le entità, le espressioni di esempio, le funzionalità e le impostazioni.

## <a name="format"></a>Formato

Quando si importa ed Esporta l'app, scegliere `.json` o `.lu` .

|Formato|Informazioni|
|--|--|
|`.json`| Formato di programmazione standard|
|`.lu`|Supportato dagli [strumenti Bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)di bot Framework.|

## <a name="version-7x"></a>Versione 7. x

* Se si passa alla versione 7. x, le entità vengono rappresentate come entità nidificate di machine learning.
* Supporto per la creazione di entità nidificate di Machine Learning con `enableNestedChildren` proprietà nelle API di creazione seguenti:
    * [Aggiungere etichetta](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Aggiungi etichetta batch](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Verifica etichette](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Suggerisci query endpoint per le entità](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Suggerisci query endpoint per gli Intent](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| element                  | Comment                              |
|--------------------------|--------------------------------------|
| "gerarchie": [],     | Deprecato, usare le [entità di Machine Learning](luis-concept-entity-types.md).   |
| "compositi": [],        | Deprecato, usare le [entità di Machine Learning](luis-concept-entity-types.md). Riferimento all' [entità composita](reference-entity-composite.md) . |
| "closedLists": [],       | [Elencare le entità](reference-entity-list.md) di riferimento, utilizzate principalmente come funzionalità per le entità.    |
| "VersionId": "0,1",      | Versione di un'app LUIS.|
| "nome": "example-app",   | Nome dell'app LUIS. |
| "DESC": "",              | Descrizione facoltativa dell'app LUIS.  |
| "cultura": "en-US",      | [Lingua](luis-language-support.md) dell'app, influisca sulle funzionalità sottostanti, ad esempio le entità predefinite, Machine Learning e Tokenizer.  |
| "tokenizerVersion": "1.0.0", | [Tokenizer](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Entità pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Entità di espressione regolare](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Elenchi di frasi (funzionalità)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Deprecato, usare le [entità di Machine Learning](luis-concept-entity-types.md). |
| "Patterns": [],          |  [Modelli per migliorare l'accuratezza della stima](luis-concept-patterns.md) con la [sintassi](reference-pattern-syntax.md) dei modelli   |
| "Impostazioni": []           | [Impostazioni app](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Versione 6. x

* Passaggio alla versione 6. x, usare la nuova [entità Machine Learning](reference-entity-machine-learned-entity.md) per rappresentare le entità.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Versione 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Passaggi successivi

* Eseguire la migrazione alle [API di creazione di V3](luis-migration-authoring-entities.md)