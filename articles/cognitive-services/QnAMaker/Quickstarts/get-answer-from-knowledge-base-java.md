---
title: 'Guida introduttiva: Ottenere una risposta dalla knowledge base in REST, Java - QnA Maker'
description: Questa Guida introduttiva Java basata su REST assiste nell'ottenimento di una risposta da una knowledge base a livello di codice.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 4d42bcf3a30b95f82ec34094afc4b6cb0842906f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267225"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Guida introduttiva: ottenere le risposte a una domanda da una Knowledge base con Java

Questa guida introduttiva illustra come ottenere, a livello di codice, una risposta da una knowledge base QnA Maker pubblicata. La knowledge base include domande e risposte da [origini dati](../Concepts/knowledge-base.md), ad esempio domande frequenti. La [domanda](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) viene inviata al servizio QnA Maker. La [risposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) include la risposta stimata più attendibile.

[Documentazione di riferimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Esempio](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Prerequisiti

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In questo esempio si usa il [client HTTP](https://hc.apache.org/httpcomponents-client-ga/) Apache di HTTP Components. È necessario aggiungere le librerie client HTTP Apache seguenti al progetto:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **Gestione risorse** nel dashboard di Azure per la risorsa di QnA Maker.
* Impostazioni pagina di **Pubblicazione**. Se non si dispone di una knowledge base pubblicata, creare una knowledge base vuota, importare una knowledge base nella pagina **Impostazioni** e quindi pubblicarla. È possibile scaricare e usare [questa knowledge base di base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    Le impostazioni della pagina di pubblicazione includono il valore di route POST, il valore Host e il valore EndpointKey.

    ![Impostazioni di pubblicazione](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Creare un file Java

Aprire VSCode, creare un nuovo file denominato `GetAnswer.java` e aggiungere la classe seguente:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

Questa Guida introduttiva usa le classi di Apache per le richieste HTTP. Sopra la classe GetAnswer, all'inizio del file `GetAnswer.java`, aggiungere le dipendenze necessarie al progetto:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie

Nella parte superiore della classe `GetAnswer.java` aggiungere le costanti seguenti per accedere a QnA Maker. Questi dati sono contenuti nella pagina **Pubblica** dopo la pubblicazione della knowledge base.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="constants":::

## <a name="add-a-post-request-to-send-question"></a>Aggiungere una richiesta POST per inviare una domanda

Nel codice seguente viene effettuata una richiesta HTTPS all'API di QnA Maker per inviare la domanda alla knowledge base e ricevere la risposta:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="post":::

Il valore dell'intestazione di `Authorization` include la stringa `EndpointKey`.

Altre informazioni su [richiesta](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [risposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma dalla riga di comando. Verrà inviata automaticamente la richiesta all'API di QnA Maker, quindi verrà visualizzato l'output nella finestra della console.

1. Compilare il file:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Eseguire il file:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
