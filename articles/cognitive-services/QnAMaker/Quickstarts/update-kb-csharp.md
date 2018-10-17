---
title: 'Guida introduttiva: Aggiornare una knowledge base in REST, C# - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Questa guida introduttiva illustra come aggiornare a livello di codice la knowledge base QnA Maker di esempio. La definizione JSON usata per aggiornare una knowledge base consente di aggiungere, modificare o eliminare coppie di domande e risposte.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: 9fa57a02f11461a3ea9a08b7b1b101b143924037
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801694"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-c"></a>Guida introduttiva: Aggiornare una knowledge base QnA Maker in C#

Questa guida introduttiva illustra come aggiornare a livello di codice una knowledge base QnA Maker esistente.  Con questo codice JSON è possibile aggiornare una knowledge base aggiungendo nuove origini dati oppure modificando o eliminando origini dati.

Questa API è equivalente alla modifica e al successivo utilizzo del pulsante **Save and train** (Salva ed esegui training) nel portale di QnA Maker.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) (Aggiornare): il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API. 
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Ottenere i dettagli dell'operazione)

## <a name="prerequisites"></a>Prerequisiti

* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard.
* ID della knowledge base (KB) QnA Maker trovato nell'URL nel parametro della stringa di query kbid come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Creare il progetto per la knowledge base

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Aggiungere le dipendenze obbligatorie

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Aggiungere le costanti obbligatorie

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-knowledge-base-id"></a>Aggiungere l'ID della knowledge base

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-the-kb-update-definition"></a>Aggiungere la definizione di aggiornamento della knowledge base

Dopo le costanti, aggiungere la definizione di aggiornamento della knowledge base seguente. La definizione di aggiornamento include tre sezioni:

* add
* update
* delete

È possibile usare ogni sezione nella stessa richiesta all'API. 

```csharp
static string new_kb = @"
{
    'add': {
        'qnaList': [
            {
            'id': 1,
            'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
            'source': 'Custom Editorial',
            'questions': [
                'How can I change the default message from QnA Maker?'
            ],
            'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'QnA Maker FAQ from quickstart - updated'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Aggiungere strutture e funzioni di supporto

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-patch-request-to-update-kb"></a>Aggiungere la richiesta PATCH per aggiornare la knowledge base

Nel codice seguente viene effettuata una richiesta HTTPS all'API QnA Maker per aggiornare i gruppi di domande e risposte in una knowledge base e viene ricevuta la risposta:

```csharp
async static Task<Response> PatchUpdateKB(string kb, string new_kb)
{
    string uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = new HttpMethod("PATCH");
        request.RequestUri = new Uri(uri);

        request.Content = new StringContent(new_kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Aggiungere la richiesta GET per determinare lo stato della creazione

L'aggiornamento di una knowledge base consente di aggiungere, aggiornare ed eliminare coppie di domande e risposte. 

```csharp
async static Task<Response> GetStatus(string operation)
{
    string uri = host + service + operation;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

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

## <a name="add-updatekb-method"></a>Aggiungere il metodo UpdateKB
Il metodo seguente consente di aggiornare la knowledge base e ripetere i controlli sullo stato. Dal momento che la creazione della knowledge base può richiedere tempo, è necessario ripetere le chiamate per controllare lo stato fino a quando questo indica un esito positivo o negativo dell'operazione.

```csharp
async static void UpdateKB(string kb, string new_kb)
{
    try
    {
        // Starts the QnA Maker operation to update the knowledge base.
        var response = await PatchUpdateKB(kb, new_kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostUpdateKB(string, string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation updating the
        // knowledge base. Once the operation state is something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);
            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still updating the knowledge base. The thread is
                // paused for a number of seconds equal to the Retry-After
                // header value, and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed updating the knowledge base.
                done = true;
            }
        }
    }
    catch(Exception ex)
    {
        // An error occurred while updating the knowledge base. Ensure that
        // you included your QnA Maker subscription key and knowledge base ID
        // where directed in the sample.
        Console.WriteLine("An error occurred while updating the knowledge base." + ex.InnerException);
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }
}
```

## <a name="add-the-updatekb-method-to-main"></a>Aggiungere il metodo UpdateKB a Main

Modificare il metodo Main in modo che chiami il metodo UpdateKB:

```csharp
static void Main(string[] args)
{
    // Invoke the UpdateKB() method to update a knowledge base, periodically
    // checking the status of the QnA Maker operation until the
    // knowledge base is updated.
    UpdateKB(kb, new_kb);

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```


## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma. La richiesta per aggiornare la knowledge base verrà inviata automaticamente all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

Dopo aver aggiornato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)