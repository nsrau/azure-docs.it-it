---
title: 'Avvio rapido: Creare una knowledge base in REST, Python - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Questa guida introduttiva per Python basata su REST illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 43ae6e7100efd923c81a0f9b94867a9101549d9b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559864"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Avvio rapido: Creare una knowledge base in QnA Maker con Python

Questa guida introduttiva illustra come creare e pubblicare a livello di codice una knowledge base QnA Maker di esempio. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../Concepts/data-sources-supported.md). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API. 

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Create Knowledgebase](https://go.microsoft.com/fwlink/?linkid=2092179) (Creare la knowledge base)
* [Get Operation Details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) (Ottenere i dettagli dell'operazione)

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.7](https://www.python.org/downloads/)
* È necessario disporre di un servizio QnA Maker. Per recuperare la chiave, selezionare Chiavi in Gestione risorse nel dashboard.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Creare un file Python per la knowledge base

Creare un file denominato `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `create-new-knowledge-base-3x.py` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie
Dopo le dipendenze obbligatorie precedenti, aggiungere le costanti obbligatorie per accedere a QnA Maker. Sostituire il valore della variabile `subscriptionKey` con la chiave personale di QnA Maker.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Aggiungere la definizione del modello di knowledge base

Dopo le costanti, aggiungere la definizione di modello di knowledge base seguente. Il modello viene convertito in una stringa dopo la definizione.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Aggiungere la funzione di supporto

Aggiungere la funzione seguente per stampare JSON in un formato leggibile:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Aggiungere le funzioni per creare la knowledge base

Aggiungere la funzione seguente per inviare una richiesta HTTP POST per creare la knowledge base. Questa chiamata API restituisce una risposta JSON che include l'ID operazione nel campo di intestazione **Location**. Usare l'ID operazione per determinare se la knowledge base è stata creata. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>Aggiungere la funzione per controllare lo stato della creazione

La funzione seguente controlla lo stato della creazione inviando l'ID operazione alla fine della route dell'URL. La chiamata a `check_status` è all'interno del ciclo _while_ principale.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

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

## <a name="add-main-code-block"></a>Aggiungere il blocco di codice principale
Il ciclo seguente esegue periodicamente il polling dell'operazione di creazione fino al completamento dell'operazione. 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Per eseguire il programma, immettere il comando seguente a una riga di comando. La richiesta per creare la knowledge base verrà inviata all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

```bash
python create-new-knowledge-base-3x.py
```

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. Selezionare il nome della knowledge base, ad esempio QnA Maker FAQ, per visualizzare le informazioni.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
