---
title: 'Avvio rapido: Libreria client di Riconoscimento modulo per .NET'
description: Questo argomento di avvio rapido illustra un'introduzione alla libreria client di Riconoscimento modulo per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: c4a8950e5aaa56f739fb3f6f780fbcfef80e2ec6
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035791"
---
[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di un set di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451).
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-form-recognizer-azure-resource"></a>Creare una risorsa di Azure per Riconoscimento modulo

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Creare variabili di ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `formrecognizer-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Spostarsi nella cartella dell'app appena creata. Quindi compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le direttive `using` seguenti:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Aggiungere quindi il codice seguente nel metodo **Main** dell'applicazione. Questa attività asincrona verrà definita in un secondo momento. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di Riconoscimento modulo per .NET con il comando seguente:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Riconoscimento modulo per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Riconoscere il contenuto di un modulo](#recognize-form-content)
* [Riconoscere le ricevute](#recognize-receipts)
* [Eseguire il training di un modello personalizzato](#train-a-custom-model)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Gestire i modelli personalizzati](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticare il client

Sotto il metodo `Main` definire l'attività a cui si fa riferimento in `Main`. In questo caso si eseguirà l'autenticazione dei due oggetti client con le variabili di sottoscrizione definite in precedenza. Si userà un oggetto **AzureKeyCredential** in modo che, se necessario, sia possibile aggiornare la chiave API senza creare nuovi oggetti client.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Chiamare metodi specifici del client

Il blocco di codice successivo usa gli oggetti client per chiamare i metodi per ogni attività principale nell'SDK di Riconoscimento modulo. Questi metodi verranno definiti in un secondo momento.

Sarà inoltre necessario aggiungere riferimenti agli URL per i dati di training e di test. 
* Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Per ottenere un URL di un modulo da testare è possibile usare i passaggi precedenti per ottenere l'URL di firma di accesso condiviso di un singolo documento nell'archivio BLOB. In alternativa, prendere l'URL di un documento situato altrove.
* Usare il metodo precedente per ottenere l'URL di un'immagine di una ricevuta, oppure usare l'URL dell'immagine di esempio specificata.

> [!NOTE]
> I frammenti di codice di questa guida usano i moduli remoti a cui si accede tramite URL. Se invece si vogliono elaborare i documenti del modulo locale, vedere i metodi correlati nella [documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Riconoscere il contenuto di un modulo

È possibile usare Riconoscimento modulo per riconoscere tabelle, righe e parole nei documenti, senza dover eseguire il training di un modello.

Per riconoscere il contenuto di un file a un URL specifico, usare il metodo **StartRecognizeContentFromUri**.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<IReadOnlyList<FormPage>> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

Il valore restituito è una raccolta di oggetti **FormPage**: uno per ogni pagina nel documento inviato. Il codice seguente esegue l'iterazione di questi oggetti e stampa le coppie chiave-valore estratte con i dati della tabella.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Riconoscere le ricevute

Questa sezione mostra come riconoscere ed estrarre i campi comuni dalle ricevute degli Stati Uniti, usando un modello di ricevuta con training preliminare.

Per riconoscere le ricevute da un URI, usare il metodo **StartRecognizeReceiptsFromUri**. Il valore restituito è una raccolta di oggetti **RecognizedReceipt**, uno per ogni pagina del documento inviato. Il codice seguente elabora una ricevuta in corrispondenza dell'URI specificato e stampa i campi e i valori principali nella console.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

Il blocco di codice successivo esegue l'iterazione dei singoli elementi rilevati nella ricevuta e stampa i relativi dettagli nella console.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Infine, l'ultimo blocco di codice stampa il valore totale della ricevuta.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Questa sezione illustra come eseguire il training di un modello con i dati personali. Un modello sottoposto a training restituisce dati strutturati che includono le relazioni chiave-valore del file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

> [!NOTE]
> È anche possibile eseguire il training dei modelli con un'interfaccia utente grafica, ad esempio con lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Eseguire il training di un modello senza etichette

Eseguire il training di modelli personalizzati per riconoscere tutti i campi e i valori trovati nei moduli personalizzati, senza etichettare manualmente i documenti di training.

Il metodo seguente esegue il training di un modello su un set di documenti specificato e ne stampa lo stato nella console. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

L'oggetto **CustomFormModel** restituito, contiene informazioni sui tipi di modulo che il modello è in grado di riconoscere e i campi che può estrarre da ogni tipo di modulo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Infine, questo metodo restituisce l'ID univoco del modello.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Eseguire il training di un modello con etichette

È inoltre possibile eseguire il training di modelli personalizzati etichettando manualmente i documenti di training. Il training con etichette consente prestazioni migliori in alcuni scenari. Per eseguire il training con etichette, è necessario avere file speciali di informazioni sulle etichette ( *\<filename\>.pdf. labels.json*) nel contenitore di archiviazione BLOB insieme ai documenti di training. Lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md) fornisce un'interfaccia utente che consente di creare questi file di etichette. Quando sono disponibili è possibile chiamare il metodo **StartTrainingAsync** con il parametro *uselabels* impostato su `true`.

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

L'oggetto **CustomFormModel** restituito indica i campi che il modello può estrarre, insieme alla relativa precisione stimata in ogni campo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Questa sezione illustra come estrarre informazioni chiave-valore e altro contenuto dai tipi di modulo personalizzati, usando i modelli a cui è stato eseguito il training con moduli personalizzati.

> [!IMPORTANT]
> Per implementare questo scenario è necessario avere già eseguito il training di un modello in modo da poter passare il relativo ID al metodo seguente. Vedere la sezione [Eseguire il training di un modello](#train-a-model-without-labels).

Si userà il metodo **StartRecognizeCustomFormsFromUri**. Il valore restituito è una raccolta di oggetti **RecognizedForm**, uno per ogni pagina del documento inviato.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

Il codice seguente stampa i risultati dell'analisi nella console. Stampa ogni campo riconosciuto e il valore corrispondente, insieme a un punteggio di attendibilità.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Gestire i modelli personalizzati

Questa sezione illustra come gestire modelli personalizzati archiviati nell'account. Il codice seguente esegue tutte le attività di gestione dei modelli in un singolo metodo, come esempio. Per iniziare, copiare la firma del metodo seguente:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificare il numero dei modelli all'interno dell'account della risorsa FormRecognizer

Il blocco di codice seguente consente di controllare il numero di modelli salvati nell'account di Riconoscimento modulo e di confrontarli con il limite dell'account.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Elencare i modelli archiviati attualmente nell'account della risorsa

Il blocco di codice seguente elenca i modelli attuali presenti nell'account e stampa i relativi dettagli nella console.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Ottenere un modello specifico con l'ID del modello

Il blocco di codice seguente esegue il training di un nuovo modello, come descritto nella sezione [Eseguire il training di un modello](#train-a-model-without-labels), e quindi ne recupera un secondo riferimento usando il relativo ID.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminare un modello dall'account della risorsa

È inoltre possibile eliminare un modello dall'account facendo riferimento al relativo ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si interagisce con la libreria client di Riconoscimento modulo di Servizi cognitivi con .NET SDK, gli errori restituiti dal servizio genereranno un'eccezione `RequestFailedException`. Verrà incluso lo stesso codice di stato HTTP che verrebbe restituito da una richiesta API REST.

Se, ad esempio, si invia un'immagine di ricevuta con un URI non valido, viene restituito un errore `400`, che indica che la richiesta non è valida.

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Si noterà che vengono registrate informazioni aggiuntive, ad esempio l'ID richiesta client dell'operazione.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata la libreria client di Riconoscimento modulo per .NET per eseguire il training di modelli e analizzare i moduli in modi diversi. In seguito, è possibile ottenere suggerimenti per creare un set di dati di training migliore e produrre modelli più accurati.

> [!div class="nextstepaction"]
> [Creare un set di dati di training](../../build-training-data-set.md)

* [Informazioni su Riconoscimento modulo](../../overview.md)
* Il codice di esempio di questa guida (e altro ancora) è reperibile su [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
