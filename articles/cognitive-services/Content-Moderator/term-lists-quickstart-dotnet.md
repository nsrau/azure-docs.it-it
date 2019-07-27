---
title: Controllare testo in base a un elenco personalizzato di termini in C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Come moderare testo con elenchi di termini personalizzati usando Content Moderator SDK per C#.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 144137109f97a8c2049430ed1e05117ea6c95d7f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564419"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Controllare testo in base a un elenco personalizzato di termini in C#

L'elenco globale di termini predefinito in Azure Content Moderator è sufficiente per quasi tutte le esigenze di moderazione del contenuto. Può tuttavia essere necessario filtrare termini specifici per la propria organizzazione. Può ad esempio essere opportuno contrassegnare con tag i nomi delle società concorrenti per una successiva analisi. 

È possibile usare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) per creare elenchi personalizzati di termini da usare con l'API di moderazione del testo.

Questo articolo contiene informazioni ed esempi di codice per iniziare a usare Content Moderator SDK per .NET allo scopo di:
- Crea un elenco
- Aggiungere termini a un elenco.
- Filtrare i termini rispetto a quelli inclusi in un elenco.
- Eliminare i termini da un elenco.
- Eliminare un elenco.
- Modificare le informazioni di un elenco.
- Aggiornare l'indice in modo da includere le modifiche apportate all'elenco in una nuova analisi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-up-for-content-moderator-services"></a>Eseguire la registrazione per i servizi Content Moderator

Per usare i servizi Content Moderator tramite l'API REST o l'SDK, è necessaria una chiave di sottoscrizione. Per ottenerne una, effettuare la sottoscrizione al servizio Content Moderator nel [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

1. Assegnare al progetto il nome **TermLists**. Selezionare questo progetto come progetto di avvio singolo per la soluzione.

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti per il progetto TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Aggiungere le istruzioni `using` riportate di seguito.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Creare il client di Content Moderator

Aggiungere il codice seguente per creare un client di Content Moderator per la sottoscrizione.

> [!IMPORTANT]
> Aggiornare i campi **AzureRegion** e **CMSubscriptionKey** con i valori di identificatore di area e chiave di sottoscrizione.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Aggiungere proprietà private

Aggiungere le proprietà private seguenti allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Creare un elenco di termini

Si crea un elenco di termini con **ContentModeratorClient.ListManagementTermLists.Create**. Il primo parametro per **Create** è una stringa contenente un tipo MIME, che deve essere "application/json". Per altre informazioni, vedere le [informazioni di riferimento sull'API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Il secondo parametro è un oggetto **Body** contenente un nome e una descrizione per il nuovo elenco di termini.

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 termini**.

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se questo limite viene superato, l'SDK genera un'eccezione con il codice di errore 429. Una chiave di livello gratuito prevede un limite di frequenza RPS pari a uno.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Aggiornare nome e la descrizione dell'elenco di termini

Si aggiornano le informazioni dell'elenco di termini con **ContentModeratorClient.ListManagementTermLists.Update**. Il primo parametro per **Update** è l'ID dell'elenco di termini. Il secondo parametro è un tipo MIME, che deve essere "application/json". Per altre informazioni, vedere le [informazioni di riferimento sull'API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Il terzo parametro è un oggetto **Body**, contenente il nuovo nome e la nuova descrizione.

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Aggiungere un termine a un elenco di termini

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Ottenere tutti i termini in un elenco di termini

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Aggiungere il codice per aggiornare l'indice di ricerca

Dopo aver apportato le modifiche a un elenco di termini, si aggiorna il relativo indice di ricerca per le modifiche da includere la volta successiva che si userà l'elenco dei termini per filtrare il testo. Questo è paragonabile al modo in cui un motore di ricerca sul desktop (se abilitato) o un motore di ricerca Web aggiorna continuamente il proprio indice per includere nuovi file o pagine.

Si aggiorna l'indice di ricerca di un elenco di termini con **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Filtrare il testo usando un elenco di termini

Si filtra il testo usando un elenco di termini con **ContentModeratorClient.TextModeration.ScreenText**, che accetta i parametri seguenti.

- La lingua dei termini nell'elenco di termini.
- Un tipo MIME, che può essere "text/html", "text/xml", "text/markdown" o "text/plain".
- Il testo da filtrare.
- Un valore booleano. Impostare questo campo su **true** per correggere automaticamente il testo prima di filtrarlo.
- Un valore booleano. Impostare questo campo su **true** per rilevare le informazioni di identificazione personale nel testo.
- L'ID dell'elenco di termini.

Per altre informazioni, vedere le [informazioni di riferimento sulle API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** restituisce un oggetto **Screen**, che ha una proprietà **Terms** che elenca i termini rilevati da Content Moderator durante lo screening. Si noti che, se Content Moderator non ha rilevato termini durante lo screening, la proprietà **Terms** ha il valore **null**.

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    read.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Eliminare termini ed elenchi

L'eliminazione di un termine o di un elenco è un'operazione semplice. È possibile usare l'SDK per eseguire queste attività:

- Eliminare un termine. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Eliminare tutti termini inclusi in un elenco senza eliminare l'elenco. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Eliminare un elenco e tutto il contenuto. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Eliminare un termine

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Eliminare tutti i termini in un elenco di termini

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Eliminare un elenco di termini

Aggiungere la definizione di metodo seguente allo spazio dei nomi TermLists, classe Program.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Comporre il metodo Main

Aggiungere la definizione del metodo **Main** allo spazio dei nomi **TermLists**, classe **Program**. Chiudere infine la classe **Program** e lo spazio dei nomi **TermLists**.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Eseguire l'applicazione per visualizzare l'output

L'output della console sarà simile al seguente:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Passaggi successivi

Consultare questa e altre guide introduttive di Content Moderator .NET per usare [Content Moderator SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e la [soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) prima di iniziare a implementare l'integrazione.
