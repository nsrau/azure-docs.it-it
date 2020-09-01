---
title: 'Guida introduttiva: pubblicare la Knowledge base, REST, go-QnA Maker'
description: In questo avvio rapido basato su Go REST viene pubblicata la knowledge base e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: b94b09fcb3bfff2eeacabaaa49eb5e4c751ec79d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267752"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Guida introduttiva: Pubblicare una knowledge base in QnA Maker con Go

Questa guida introduttiva basata su REST illustra la procedura di pubblicazione della knowledge base (KB) a livello di codice. Con la pubblicazione viene eseguito il push dell'ultima versione della knowledge base in un indice dedicato di Ricerca cognitiva di Azure e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Pubblicazione): con questa API non sono richieste informazioni nel corpo della richiesta.

## <a name="prerequisites"></a>Prerequisiti

* [Go 1.10.1](https://golang.org/dl/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.

* ID della knowledge base (KB) di QnA Maker trovato nell'URL nel parametro della stringa di query `kbid` come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-csharp.md).

> [!NOTE]
> I file della soluzione completa sono disponibili nel [repository GitHub **Azure-Samples/cognitive-Services-qnamaker-go** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Creare un file Go

Aprire VSCode e creare un nuovo file denominato `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `publish-kb.go` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Creare la funzione main

Dopo le dipendenze necessarie aggiungere la classe seguente:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Aggiungere la richiesta POST per pubblicare la knowledge base

Aggiungere il codice seguente, che effettua una richiesta HTTPS all'API QnA Maker per pubblicare una Knowledge base e riceve la risposta:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

In caso di pubblicazione riuscita la chiamata API restituisce uno stato 204 senza alcun contenuto nel corpo della risposta. Il codice aggiunge il contenuto per le risposte con stato 204.

Per qualsiasi altra risposta, tale risposta viene restituita senza variazioni.

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Immettere il comando seguente per compilare il file. Il prompt dei comandi non restituisce informazioni relative all'avvenuta compilazione.

```bash
go build publish-kb.go
```

Per eseguire il programma, immettere il comando seguente a una riga di comando. Invierà la richiesta all'API di QnA Maker per pubblicare la knowledge base, quindi stamperà lo stato 204 con esito positivo o errori.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo la pubblicazione della knowledge base, è necessario l'[URL dell'endpoint per generare una risposta](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
