---
title: Definizione dello schema dell'app
description: L'app LUIS è rappresentata in `.json` o `.lu` e include tutti gli Intent, le entità, le espressioni di esempio, le funzionalità e le impostazioni.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 21b58f79ffd2baf553c6f8b07daa84473e620f77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599370"
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

* Se si passa alla versione 7. x, le entità vengono rappresentate come entità nidificate dal computer.
* Supporto per la creazione di entità annidate Machine-learned con `enableNestedChildren` proprietà nelle API di creazione seguenti:
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
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>Versione 6. x

* Al passaggio alla versione 6. x, usare la nuova [entità Machine-learned](reference-entity-machine-learned-entity.md) per rappresentare le entità.

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