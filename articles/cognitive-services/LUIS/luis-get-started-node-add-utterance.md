---
title: "Guida introduttiva: Modificare il modello ed eseguire il training dell'app LUIS con Node.js - Servizi cognitivi di Azure | Microsoft Docs"
description: In questa guida introduttiva per Node.js si aggiungono espressioni di esempio in un'app Home Automation e si esegue il training dell'app. Le espressioni di esempio sono costituite da testo utente discorsivo di cui è stato eseguito il mapping a una finalità. Fornendo espressioni di esempio per le finalità, si insegna all'app LUIS quale tipo di testo specificato dall'utente appartiene a un determinato tipo di finalità.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: fbd8b467fa3894d9cf58e1c8cb78ee00ebd0965e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44156854"
---
# <a name="quickstart-change-model-using-nodejs"></a>Guida introduttiva: Modificare il modello con Node.js

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* La versione più recente di [**Node.js**](https://nodejs.org/en/download/) con NPM.
* Dipendenze NPM per questo articolo: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Creare il codice della guida introduttiva 

Aggiungere le dipendenze NPM al file denominato `add-utterances.js`.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Aggiungere le costanti LUIS al file. Copiare il codice seguente e immettere la propria chiave di creazione, insieme all'ID applicazione e all'ID versione.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Aggiungere il nome e il percorso del file di caricamento che contiene le espressioni. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Aggiungere metodo e oggetto per la funzione `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Aggiungere metodo e oggetto per la funzione `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Aggiungere la funzione `sendUtteranceToApi` per inviare e ricevere chiamate HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Aggiungere il codice **main** che sceglie l'azione appropriata.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Installare le dipendenze

Creare il file `package.json` con il testo seguente:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

Alla riga di comando, nella directory che contiene package.json, installare le dipendenze con NPM: `npm install`.

## <a name="run-code"></a>Eseguire il codice

Eseguire l'applicazione da una riga di comando con Node.js.

Con la chiamata a `npm start` vengono aggiunte le espressioni, viene eseguito il training e viene ottenuto lo stato del training.

```CMD
> npm start 
```

Questa riga di comando visualizza i risultati della chiamata all'API di aggiunta delle espressioni. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la procedura illustrata, rimuovere tutti i file creati in questa guida introduttiva. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Compilare un'app di Language Understanding Intelligent Service (LUIS) a livello di codice](luis-tutorial-node-import-utterances-csv.md)