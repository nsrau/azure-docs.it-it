---
title: 'Guida introduttiva: Creare una knowledge base in REST, Go - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva per Go basata su REST illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 4831fca8a72f62d64c1977b510f039065f6cd3c5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788573"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Guida introduttiva: Creare una knowledge base in QnA Maker con Go

Questa guida introduttiva illustra come creare a livello di codice una knowledge base QnA Maker di esempio. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../Concepts/data-sources-supported.md). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API. 

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Create Knowledgebase](https://go.microsoft.com/fwlink/?linkid=2092179) (Creare la knowledge base)
* [Get Operation Details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) (Ottenere i dettagli dell'operazione)

## <a name="prerequisites"></a>Prerequisiti

* [Go 1.10.1](https://golang.org/dl/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard. 

## <a name="create-a-knowledge-base-go-file"></a>Creare un file Go per la knowledge base

Creare un file denominato `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `create-new-knowledge-base.go` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie
Dopo le dipendenze obbligatorie precedenti, aggiungere le costanti obbligatorie per accedere a QnA Maker. Sostituire il valore della variabile `subscriptionKey` con la chiave personale di QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Aggiungere la definizione del modello di knowledge base
Dopo le costanti, aggiungere la definizione di modello di knowledge base seguente. Il modello viene convertito in una stringa dopo la definizione.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Aggiungere strutture e funzioni di supporto

A questo punto, aggiungere le funzioni di supporto seguenti.

1. Aggiungere la struttura per una richiesta HTTP:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Aggiungere il metodo seguente per gestire una richiesta POST alle API QnA Maker. Per questa guida introduttiva la richiesta POST viene usata per inviare la definizione della knowledge base a QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Aggiungere il metodo seguente per gestire una richiesta GET alle API QnA Maker. Per questa guida introduttiva la richiesta GET viene usata per controllare lo stato dell'operazione di creazione. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Aggiungere le funzioni per creare la knowledge base

Aggiungere le funzioni seguenti per effettuare una richiesta HTTP POST per creare la knowledge base. Viene restituito l'_ID operazione_ **create** nel campo di intestazione della risposta POST **Location**, quindi usato come parte della route nella richiesta GET. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

Questa chiamata API restituisce una risposta JSON che include l'ID operazione. Usare l'ID operazione per determinare se la knowledge base è stata creata. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Aggiungere la funzione per ottenere lo stato

Aggiungere la funzione seguente per effettuare una richiesta HTTP GET per controllare lo stato dell'operazione. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Aggiungere la funzione principale

La funzione seguente è quella principale e consente di creare la knowledge base, nonché ripetere i controlli sullo stato. Dal momento che la creazione della knowledge base può richiedere tempo, è necessario ripetere le chiamate per controllare lo stato fino a quando questo indica un esito positivo o negativo dell'operazione.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Compilare il programma
Immettere il comando seguente per compilare il file. Il prompt dei comandi non restituisce informazioni relative all'avvenuta compilazione.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Eseguire il programma

Per eseguire il programma, immettere il comando seguente a una riga di comando. La richiesta per creare la knowledge base verrà inviata all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

```bash
go run create-new-knowledge-base
```

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
