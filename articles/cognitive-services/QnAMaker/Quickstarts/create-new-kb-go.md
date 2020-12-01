---
title: 'Guida introduttiva: Creare una knowledge base in REST, Go - QnA Maker'
description: Questa guida introduttiva per Go basata su REST illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: cff2d141e8108d9a3e2e12764174a0bf4978182f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352321"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Guida introduttiva: Creare una knowledge base in QnA Maker con Go

Questa guida introduttiva illustra come creare a livello di codice una knowledge base QnA Maker di esempio. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../index.yml). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Creare la knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Ottenere i dettagli dell'operazione](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentazione](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  di riferimento [Esempio di go](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Go 1.10.1](https://golang.org/dl/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.

## <a name="create-a-knowledge-base-go-file"></a>Creare un file Go per la knowledge base

Creare un file denominato `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `create-new-knowledge-base.go` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Aggiungere la definizione del modello di knowledge base
Dopo le costanti, aggiungere la definizione di modello di knowledge base seguente. Il modello viene convertito in una stringa dopo la definizione.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Aggiungere strutture e funzioni di supporto

A questo punto, aggiungere le funzioni di supporto seguenti.

1. Aggiungere la struttura per una risposta HTTP:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Aggiungere il metodo seguente per gestire una richiesta POST alle API QnA Maker. Per questa guida introduttiva la richiesta POST viene usata per inviare la definizione della knowledge base a QnA Maker.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Aggiungere il metodo seguente per gestire una richiesta GET alle API QnA Maker. Per questa guida introduttiva la richiesta GET viene usata per controllare lo stato dell'operazione di creazione.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Aggiungere le funzioni per creare la knowledge base

Aggiungere le funzioni seguenti per effettuare una richiesta HTTP POST per creare la knowledge base. L' _create_ **ID dell'operazione** di creazione viene restituito nella **posizione** del campo dell'intestazione Post-risposta, quindi viene usato come parte della route nella richiesta GET. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


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
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)