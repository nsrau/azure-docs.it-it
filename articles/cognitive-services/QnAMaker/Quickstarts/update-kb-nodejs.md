---
title: 'Guida introduttiva: Aggiornare una knowledge base in REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Questa guida introduttiva illustra come aggiornare a livello di codice una knowledge base QnA Maker esistente.  Con questo codice JSON è possibile aggiornare una knowledge base aggiungendo nuove origini dati oppure modificando o eliminando origini dati.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: 3bbc55b3bb064b2cf4b140a395e99209b71a5ce1
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816230"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-nodejs"></a>Guida introduttiva: Aggiornare una knowledge base QnA Maker in Node.js

Questa guida introduttiva illustra come aggiornare a livello di codice una knowledge base QnA Maker esistente.  Con questo codice JSON è possibile aggiornare una knowledge base aggiungendo nuove origini dati oppure modificando o eliminando origini dati.

Questa API è equivalente alla modifica e al successivo utilizzo del pulsante **Save and train** (Salva ed esegui training) nel portale di QnA Maker.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) (Aggiornare): il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API. 
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Ottenere i dettagli dell'operazione)

## <a name="prerequisites"></a>Prerequisiti

* [Node.js 6+](https://nodejs.org/en/download/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard. 
* ID della knowledge base (KB) QnA Maker trovato nell'URL nel parametro della stringa di query kbid come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Creare un file Node.js per la knowledge base

Creare un file denominato `update-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze obbligatorie

All'inizio di `update-knowledge-base.js` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Aggiungere le costanti obbligatorie
Dopo le dipendenze obbligatorie precedenti, aggiungere le costanti obbligatorie per accedere a QnA Maker. Sostituire il valore della variabile `subscriptionKey` con la chiave personale di QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Aggiungere l'ID della knowledge base

Dopo le costanti precedenti specificare l'ID della knowledge base e aggiungerlo al percorso:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>Aggiungere la definizione del modello di aggiornamento della knowledge base

Dopo le costanti, aggiungere la definizione di aggiornamento della knowledge base seguente. La definizione di aggiornamento include tre sezioni:

* add
* update
* delete

È possibile usare ogni sezione nella stessa richiesta all'API. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Aggiungere funzioni di supporto

A questo punto, aggiungere le funzioni di supporto seguenti.

1. Aggiungere la funzione seguente per stampare JSON in un formato leggibile:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Aggiungere le funzioni seguenti per gestire la risposta HTTP in modo da ottenere lo stato dell'operazione di creazione:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>Aggiungere la richiesta PATCH per aggiornare la knowledge base

Aggiungere le funzioni seguenti per effettuare una richiesta HTTP PATCH per aggiornare la knowledge base. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

Questa chiamata API restituisce una risposta JSON che include l'ID operazione. Se l'operazione non è stata completata, l'ID operazione è necessario per richiedere lo stato.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>Aggiungere la richiesta GET per determinare lo stato dell'operazione

Verificare lo stato dell'operazione.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

Questa chiamata API restituisce una risposta JSON che include lo stato dell'operazione: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Ripetere la chiamata fino a quando l'esito non è positivo o negativo: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Aggiungere il metodo update_kb

Il metodo seguente consente di aggiornare la knowledge base e ripetere i controlli sullo stato. Dal momento che la creazione della knowledge base può richiedere tempo, è necessario ripetere le chiamate per controllare lo stato fino a quando questo indica un esito positivo o negativo dell'operazione.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>Eseguire il programma

Per eseguire il programma, immettere il comando seguente a una riga di comando. La richiesta per aggiornare la knowledge base verrà inviata all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

```bash
node update-knowledge-base.js
```

Dopo aver aggiornato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)