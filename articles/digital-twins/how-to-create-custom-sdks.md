---
title: Creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest
titleSuffix: Azure Digital Twins
description: Vedere come generare SDK personalizzati per usare i dispositivi gemelli digitali di Azure con linguaggi diversi da C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e2a7f09ac6ff3be119a07ed0a2162525801ceef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061850"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest

Attualmente, l'unico SDK del piano dati pubblicato per interagire con le API dei dispositivi gemelli digitali di Azure è per .NET (C#). Per informazioni generali su .NET SDK e sulle API, vedere [*procedura: usare gli SDK e le API per i dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md). Se si lavora in un'altra lingua, in questo articolo viene illustrato come generare un proprio SDK nel linguaggio preferito, usando autorest.

## <a name="set-up-your-machine"></a>Configurare il computer

Per generare un SDK, sarà necessario:
* [Autorest](https://github.com/Azure/autorest), versione 2.0.4413 (la versione 3 non è attualmente supportata)
* [Node.js](https://nodejs.org) come prerequisito di autorest
* Il [file spavalderia (openapi) di Azure Digital gemelli](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) ha diritto *digitaltwins.js*e la relativa cartella di esempi. Scaricare il file spavalderia e la relativa cartella degli esempi nel computer locale.

Quando il computer è dotato di tutti gli elementi dall'elenco precedente, è possibile usare autorest per creare l'SDK.

## <a name="create-the-sdk-with-autorest"></a>Creare l'SDK con autorest 

Se Node.js è stato installato, è possibile eseguire questo comando per verificare che sia installata la versione corretta di autorest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Per eseguire autorest nel file con estensione per i gemelli digitali di Azure, seguire questa procedura:
1. Copiare il file spavalderia di Digital gemelli di Azure e la cartella di esempi associata in una directory di lavoro.
2. Utilizzare una finestra del prompt dei comandi per passare alla directory di lavoro.
3. Eseguire autorest con il comando seguente. Sostituire il `<language>` segnaposto con la lingua scelta: `--python` , `--java` , `--go` e così via. (È possibile trovare l'elenco completo delle opzioni nel [file Leggimi di autorest](https://github.com/Azure/autorest)).

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Verrà quindi visualizzata una nuova cartella denominata *ADTApi* nella directory di lavoro. I file SDK generati avranno lo spazio dei nomi *ADTApi*. Lo spazio dei nomi continuerà a essere usato nei restanti esempi di utilizzo di questo articolo.

Autorest supporta un'ampia gamma di generatori di codice della lingua.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Aggiungere l'SDK a un progetto di Visual Studio

È possibile includere i file generati da autorest direttamente in una soluzione .NET. Tuttavia, è probabile che si voglia includere Azure Digital Gemells SDK in diversi progetti distinti (app client, app di funzioni di Azure e così via). Per questo motivo, può essere utile compilare un progetto separato, ovvero una libreria di classi .NET, dai file generati. È quindi possibile includere questo progetto di libreria di classi in diverse soluzioni come riferimento al progetto.

In questa sezione vengono fornite istruzioni su come compilare l'SDK come libreria di classi, che è il proprio progetto e che può essere incluso in altri progetti. Questa procedura si basa su **Visual Studio** (è possibile installare la versione più recente da [qui](https://visualstudio.microsoft.com/downloads/)).

Ecco i passaggi necessari:

1. Creare una nuova soluzione di Visual Studio per una libreria di classi
2. Usare *ADTApi* come nome del progetto
3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto *ADTApi* della soluzione generata e scegliere *Aggiungi > elemento esistente...*
4. Individuare la cartella in cui è stato generato l'SDK e selezionare i file a livello di radice
5. Premere "OK"
6. Aggiungere una cartella al progetto (fare clic con il pulsante destro del mouse sul progetto Esplora soluzioni e scegliere *aggiungi > nuova cartella*).
7. Assegnare un nome ai *modelli* di cartella
8. Fare clic con il pulsante destro del mouse sulla cartella *Models* in Esplora soluzioni e scegliere *Aggiungi > elemento esistente...*
9. Selezionare i file nella cartella *modelli* dell'SDK generato e fare clic su "OK".

Per compilare l'SDK correttamente, il progetto richiederà i riferimenti seguenti:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Per aggiungerli, aprire *strumenti > gestione pacchetti nuget > gestire i pacchetti NuGet per la soluzione...*.

1. Nel pannello verificare che sia selezionata la scheda *Sfoglia* .
2. Cerca *Microsoft. Rest*
3. Selezionare i `ClientRuntime` `ClientRuntime.Azure` pacchetti e e aggiungerli alla soluzione

È ora possibile compilare il progetto e includerlo come riferimento a un progetto in qualsiasi applicazione di dispositivi digitali gemelli di Azure che si scrive.

## <a name="general-guidelines-for-generated-sdks"></a>Linee guida generali per gli SDK generati

Questa sezione contiene informazioni generali e linee guida per l'uso dell'SDK generato.

### <a name="synchronous-and-asynchronous-calls"></a>Chiamate sincrone e asincrone

Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

### <a name="typed-and-untyped-data"></a>Dati tipizzati e non tipizzati

Le chiamate API REST in genere restituiscono oggetti fortemente tipizzati. Tuttavia, poiché i dispositivi gemelli digitali di Azure consentono agli utenti di definire tipi personalizzati per i dispositivi gemelli, non è possibile predefinire i dati restituiti statici per molte chiamate di Azure Digital gemelli. Al contrario, le API restituiscono tipi di wrapper fortemente tipizzati, se applicabile, e i dati del dispositivo gemello personalizzato si trovano in oggetti Json.NET (utilizzati ovunque il tipo di dati "oggetto" viene visualizzato nelle firme dell'API). È possibile eseguire il cast di questi oggetti in modo appropriato nel codice.

### <a name="error-handling"></a>Gestione degli errori

Ogni volta che si verifica un errore nell'SDK (inclusi errori HTTP come 404), l'SDK genererà un'eccezione. Per questo motivo, è importante incapsulare tutte le chiamate API nei blocchi try/catch.

Ecco un frammento di codice che tenta di aggiungere un dispositivo gemello e rileva gli eventuali errori in questo processo:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Paging

Autorest genera due tipi di modelli di paging per l'SDK:
* Uno per tutte le API, ad eccezione dell'API di query
* Uno per l'API di query

Nel modello di paging non di query sono disponibili due versioni di ciascuna chiamata:
* Una versione per effettuare la chiamata iniziale (ad esempio `DigitalTwins.ListEdges()` )
* Versione per ottenere le pagine seguenti. Queste chiamate hanno un suffisso "Next" (ad esempio `DigitalTwins.ListEdgesNext()` )

Ecco un frammento di codice che illustra come recuperare un elenco di pagine di relazioni in uscita da dispositivi gemelli digitali di Azure:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

Il secondo modello viene generato solo per l'API di query. Usa un oggetto in `continuationToken` modo esplicito.

Di seguito è riportato un esempio con questo modello:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare i passaggi per creare un'app client in cui è possibile usare l'SDK:
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)