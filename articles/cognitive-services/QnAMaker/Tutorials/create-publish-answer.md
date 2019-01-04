---
title: Creare, pubblicare, rispondere
titleSuffix: QnA Maker - Azure Cognitive Services
description: Questa esercitazione basata su REST illustra la procedura di creazione e pubblicazione di una knowledge base a livello di codice e quindi descrive come rispondere a una domanda dalla knowledge base stessa.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: f178256180cfc75467a3dea2c7ac8d53fe9dd26a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995168"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Esercitazione: Creare una knowledge base e quindi rispondere a una domanda con C#

Questa esercitazione illustra la procedura di creazione e pubblicazione di una knowledge base a livello di codice e quindi descrive come rispondere a una domanda del cliente con la knowledge base stessa. 

> [!div class="checklist"]
* Creare una knowledge base 
* Controllare lo stato della creazione
* Eseguire il training della knowledge base e pubblicarla
* Ottenere le informazioni sugli endpoint
* Usare Curl per eseguire una query sulla knowledge base


In questa guida introduttiva viene chiamata l'API QnA Maker seguente:

* [Create Knowledge base (kb)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Creare la knowledge base)
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Ottenere i dettagli dell'operazione)
* [Get Knowledge base details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) (Ottenere i dettagli della knowledge base) 
* [Get Knowledge base endpoints](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys) (Ottenere gli endpoint della knowledge base)
* [Pubblica](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Prerequisiti

* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard. 

> [!NOTE] 
> I file di soluzione completi sono disponibili nel [**repository GitHub** Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Creare un progetto per la knowledge base

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di Program.cs sostituire la singola istruzione _using_ con le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Aggiungere una classe KBDetails
Aggiungere questa classe KBDetails all'interno delle parentesi quadre degli spazi dei nomi. Questa classe consente alla libreria NewtonSoft di deserializzare la risposta JSON in una classe C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie

Nella parte superiore della classe Program, aggiungere le costanti seguenti per accedere a QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Aggiungere la definizione di knowledge base

Dopo le costanti aggiungere la definizione del modello di knowledge base seguente:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Aggiungere strutture e funzioni di supporto
Aggiungere il blocco di codice seguente all'interno della classe Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Aggiungere una richiesta POST per creare la knowledge base

Nel codice seguente viene effettuata una richiesta HTTPS all'API QnA Maker per creare una knowledge base e viene ricevuta la risposta:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Questa chiamata API restituisce una risposta JSON che include l'ID operazione. Successivamente, il programma usa l'ID operazione per determinare se la knowledge base è stata creata correttamente. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Aggiungere la richiesta GET per determinare lo stato della creazione

Controllare lo stato dell'operazione di creazione.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Questa chiamata API restituisce una risposta JSON che include lo stato dell'operazione: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Aggiungere il metodo CreateKB

Il metodo seguente incapsula le chiamate per creare la knowledge base e controllare lo stato.  Viene restituito l'_ID operazione_ **create** nel campo di intestazione della risposta POST **Location**, quindi usato come parte della route nella richiesta GET. Dal momento che la creazione della knowledge base può richiedere tempo, è necessario ripetere le chiamate per controllare lo stato fino a quando questo indica un esito positivo o negativo dell'operazione. Quando l'operazione riesce, viene restituito l'ID della knowledge base in **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Aggiungere un metodo di pubblicazione

Dopo aver creato correttamente la knowledge base, è possibile pubblicarla. Ci si può aspettare una chiamata ad un'API Training, ma non è necessaria con questa versione. 

Nel codice seguente viene effettuata una richiesta HTTPS all'API QnA Maker per pubblicare una knowledge base e viene ricevuta la risposta:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

In caso di pubblicazione riuscita la chiamata API restituisce uno stato 204 senza alcun contenuto nel corpo della risposta. Il codice di avvio rapido aggiunge il testo per 204 risposte in modo da potere visualizzare il risultato.

Per qualsiasi altra risposta, tale risposta viene restituita senza variazioni.

## <a name="generating-an-answer"></a>Generazione di una risposta
Per poter accedere alla knowledge base per inviare una domanda e ricevere la risposta migliore, al programma serve l'_host dell'endpoint_ dall'API dei dettagli della knowledge base e la _chiave dell'endpoint primario_ dall'API degli endpoint. Tali metodi sono inclusi nelle sezioni seguenti insieme al metodo per generare una risposta. 

La tabella seguente illustra come vengono usati i dati per costruire l'URI:

|Generare il modello URI della risposta|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

L'_endpoint primario_ viene passato come intestazione per autenticare la richiesta per generare una risposta:

|Nome intestazione|Valore intestazione|
|--|--|
|Authorization|`Endpoint ` + **endpoint primario**<br>Esempio: `Endpoint xxxxxxx`<br>Si noti lo spazio tra il testo di `Endpoint` e il valore dell'endpoint primario. 

Il corpo della richiesta deve passare l'oggetto JSON appropriato:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Ottenere i dettagli della knowledge base
Aggiungere il metodo seguente per ottenere i dettagli della knowledge base. Questi dettagli contengono il nome host della knowledge base. Il nome host è il nome del servizio Web di Azure QnA Maker che è stato immesso durante la creazione della risorsa QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Questa chiamata API restituisce una risposta JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Ottenere gli endpoint della knowledge base
Aggiungere il metodo seguente per ottenere gli endpoint primari di QnA Maker. Questi endpoint non sono associati alla knowledge base, ma sono validi per tutte le knowledge base associate alle chiavi della risorsa QnA Maker dal portale di Azure.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Questa chiamata API restituisce una risposta JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Ottenere una risposta
Aggiungere il metodo seguente per ottenere una risposta alla domanda dell'utente. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Questa chiamata API restituisce una risposta JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Metodo Main
Il metodo Main mostra le chiamate sincrone per creare, pubblicare e generare la risposta. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma. 

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. Dopo aver appreso come usare l'API di generazione delle risposte, è possibile usare l'API con qualsiasi linguaggio o framework di richiesta HTTP. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
