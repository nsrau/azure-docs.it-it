---
title: Associazioni di Microsoft Graph in Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare trigger e associazioni di Microsoft Graph in Funzioni di Azure.
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0eb0ac63c7dbb9d6cbba093937231e93670529e
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="azure-functions-microsoft-graph-bindings"></a>Associazioni di Microsoft Graph in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e usare trigger e associazioni di Microsoft Graph in Funzioni di Azure.
I trigger e le associazioni consentono di usare Funzioni di Azure con dati, informazioni dettagliate ed eventi di [Microsoft Graph](https://graph.microsoft.io).

L'estensione Microsoft Graph offre le associazioni seguenti:
- Un'[associazione di input per il token di autenticazione](#token-input) che consente di interagire con le API di Microsoft Graph.
- Un'[associazione di input per la tabella di Excel](#excel-input) che consente di leggere i dati di Excel.
- Un'[associazione di output per la tabella di Excel](#excel-output) che consente di modificare i dati di Excel.
- Un'[associazione di input per i file di OneDrive](#onedrive-input) che consente di leggere i file di OneDrive.
- Un'[associazione di output per i file di OneDrive](#onedrive-output) che consente di scrivere sui file di OneDrive.
- Un'[associazione di output per i messaggi di Outlook](#outlook-output) che consente di inviare un messaggio di posta elettronica tramite Outlook.
- Una raccolta di [trigger e associazioni per il webhook di Microsoft Graph](#webhooks) che consente di rispondere agli eventi di Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Le associazioni di Microsoft Graph sono attualmente in anteprima.

## <a name="setting-up-the-extensions"></a>Configurazione delle estensioni

Le associazioni di Microsoft Graph sono disponibili tramite le _estensioni dell'associazione_. Le estensioni dell'associazione sono componenti facoltativi per il runtime di Funzioni di Azure. Questa sezione illustra come configurare le estensioni dei token di autenticazione e di Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Abilitazione dell'anteprima di Funzioni 2.0

Le estensioni dell'associazione sono disponibili solo per l'anteprima di Funzioni di Azure 2.0. Per abilitare Funzioni 2.0, impostare l'impostazione dell'applicazione `FUNCTIONS_EXTENSION_VERSION` su "beta".  Per informazioni su come configurare le impostazioni dell'applicazione, vedere [Impostazioni applicazione in Funzioni di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings).

### <a name="installing-the-extension"></a>Installazione dell'estensione

Per installare un'estensione dal portale di Azure, è necessario passare a un modello o a un'associazione che vi fa riferimento. Creare una nuova funzione e nella schermata di selezione del modello, scegliere lo scenario "Microsoft Graph". Selezionare uno dei modelli di questo scenario. In alternativa, è possibile passare alla scheda "Integrazione" di una funzione esistente e selezionare una delle associazioni trattate in questo argomento.

In entrambi i casi, verrà visualizzato un avviso che specifica l'estensione da installare. Fare clic su **Installa** per ottenere l'estensione.

> [!Note] 
> Ogni estensione deve essere installata una sola volta per ogni app per le funzioni. In un piano a consumo il processo di installazione nel portale può richiedere fino a 10 minuti.

Se si usa Visual Studio, è possibile ottenere le estensioni installando questi pacchetti NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Configurazione dell'autenticazione/autorizzazione del servizio app

Le associazioni descritte in questo argomento richiedono l'uso di un'identità. In questo modo Microsoft Graph applica le autorizzazioni e le interazioni di controllo. L'identità può essere un utente con accesso all'applicazione o l'applicazione stessa. Per configurare l'identità, è necessario impostare [l'autenticazione/autorizzazione del servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) con Azure Active Directory. È anche necessario richiedere tutte le autorizzazioni delle risorse richieste per le funzioni.

> [!Note] 
> L'estensione Microsoft Graph supporta solo l'autenticazione AAD. Gli utenti devono accedere con un account aziendale o dell'istituto di istruzione.

Se si usa il portale di Azure, sotto la richiesta di installazione dell'estensione verrà visualizzato un avviso che richiede di configurare l'autenticazione/autorizzazione del servizio app e tutte le autorizzazioni necessarie per il modello o l'associazione. Fare clic su **Configura AAD adesso** o **Add permissions now** (Aggiungi autorizzazioni adesso) in base alle esigenze.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Associazione di input per il token di autenticazione

Questa associazione ottiene un token AAD per una determinata risorsa e lo inserisce nel codice sotto forma di stringa. La risorsa può essere una qualsiasi risorsa per cui l'applicazione dispone di autorizzazioni. 

### <a name="configuring-an-auth-token-input-binding"></a>Configurazione di un'associazione di input per il token di autenticazione

L'associazione stessa non richiede alcuna autorizzazione di AAD, ma a seconda di come viene usato il token, potrebbe essere necessario richiedere altre autorizzazioni. Verificare i requisiti della risorsa a cui si desidera accedere con il token.

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il token di autenticazione. Vedere [Usare un'associazione di input per il token di autenticazione nel codice](#token-input-code).|
|**type**|Obbligatoria. Deve essere impostata su `token`.|
|**direction**|Obbligatoria. Deve essere impostata su `in`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**resource**|Obbligatoria: un URL della risorsa AAD per il quale viene richiesto il token.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Usare un'associazione di input per il token di autenticazione nel codice

Il token viene sempre visualizzato nel codice sotto forma di stringa.

#### <a name="sample-getting-user-profile-information"></a>Esempio: recupero di informazioni sul profilo utente

Si supponga di avere il file function.json seguente, che definisce un trigger HTTP con un'associazione di input per il token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente usa il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

L'esempio JS seguente usa il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato. In `function.json` sopra sostituire prima `$return` con `res`.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Associazione di input per la tabella di Excel

Questa associazione legge il contenuto di una tabella di Excel archiviata in OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Configurazione di un'associazione di input per la tabella di Excel

Questa associazione richiede le autorizzazioni AAD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per la tabella di Excel. Vedere [Usare un'associazione di input per la tabella di Excel nel codice](#excel-input-code).|
|**type**|Obbligatoria. Deve essere impostata su `excel`.|
|**direction**|Obbligatoria. Deve essere impostata su `in`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|Obbligatoria: il percorso della cartella di lavoro di Excel in OneDrive.|
|**worksheetName**|Il foglio di lavoro in cui si trova la tabella.|
|**tableName**|Nome della tabella. Se non specificato, verrà usato il contenuto del foglio di lavoro.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Usare un'associazione di input per la tabella di Excel nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Microsoft.Graph.WorkbookTable
- Tipi di oggetto personalizzati tramite l'associazione di modelli strutturali

#### <a name="sample-reading-an-excel-table"></a>Esempio: lettura di una tabella di Excel

Si supponga di avere il file function.json seguente che definisce un trigger HTTP con un'associazione di input per Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente aggiunge la lettura del contenuto della tabella specificata e la restituisce all'utente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

L'esempio JS seguente aggiunge la lettura del contenuto della tabella specificata e la restituisce all'utente. In `function.json` sopra sostituire prima `$return` con `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Associazione di output per la tabella di Excel

Questa associazione modifica il contenuto di una tabella di Excel archiviata in OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Configurazione di un'associazione di output per la tabella di Excel

Questa associazione richiede le autorizzazioni AAD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Accesso completo ai file dell'utente|

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il token di autenticazione. Vedere [Usare un'associazione di ouput per la tabella di Excel nel codice](#excel-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `excel`.|
|**direction**|Obbligatoria. Deve essere impostata su `out`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|Obbligatoria: il percorso della cartella di lavoro di Excel in OneDrive.|
|**worksheetName**|Il foglio di lavoro in cui si trova la tabella.|
|**tableName**|Nome della tabella. Se non specificato, verrà usato il contenuto del foglio di lavoro.|
|**updateType**|Obbligatoria: tipo di modifica da apportare alla tabella. Può avere uno dei valori seguenti:<ul><li><code>update</code>: sostituisce il contenuto della tabella in OneDrive.</li><li><code>append</code>: aggiunge il payload alla fine della tabella in OneDrive mediante la creazione di nuove righe.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Usare un'associazione di ouput per la tabella di Excel nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipi di oggetto personalizzati tramite l'associazione di modelli strutturali

#### <a name="sample-adding-rows-to-an-excel-table"></a>Esempio: aggiunta di righe a una tabella di Excel

Si supponga di avere il file function.json seguente che definisce un trigger HTTP con un'associazione di output per Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


L'esempio C# seguente aggiunge una nuova riga alla tabella, che si presuppone abbia una sola colonna, in base all'input dalla stringa di query:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

L'esempio JS seguente aggiunge una nuova riga alla tabella, che si presuppone abbia una sola colonna, in base all'input dalla stringa di query. In `function.json` sopra sostituire prima `$return` con `res`.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Associazione di input per i file di OneDrive

Questa associazione legge il contenuto di un file archiviato in OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Configurazione di un'associazione di input per i file di OneDrive

Questa associazione richiede le autorizzazioni AAD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il file. Vedere [Usare un'associazione di input per i file di OneDrive nel codice](#onedrive-input-code).|
|**type**|Obbligatoria. Deve essere impostata su `onedrive`.|
|**direction**|Obbligatoria. Deve essere impostata su `in`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|Obbligatoria: il percorso del file in OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Usare un'associazione di input per i file di OneDrive nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- byte[]
- Flusso
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Esempio: lettura di un file di OneDrive

Si supponga di avere il file function.json seguente, che definisce un trigger HTTP con un'associazione di input per OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente legge il file specificato nella stringa della query e ne registra la lunghezza:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

L'esempio JS seguente legge il file specificato nella stringa della query e ne restituisce la lunghezza. In `function.json` sopra sostituire prima `$return` con `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Associazione di output per i file di OneDrive

Questa associazione modifica il contenuto di un file archiviato in OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Configurazione di un'associazione di output per i file di OneDrive

Questa associazione richiede le autorizzazioni AAD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Accesso completo ai file dell'utente|

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il file. Vedere [Usare un'associazione di output per i file di OneDrive nel codice](#onedrive-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `onedrive`.|
|**direction**|Obbligatoria. Deve essere impostata su `out`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|Obbligatoria: il percorso del file in OneDrive.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Usare un'associazione di output per i file di OneDrive nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- byte[]
- Flusso
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Esempio: scrittura in un file di OneDrive

Si supponga di avere il file function.json seguente, che definisce un trigger HTTP con un'associazione di output per OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente ottiene il testo dalla stringa della query e lo scrive in un file di testo, ovvero in FunctionsTest.txt, come definito nella configurazione precedente, nella radice di OneDrive del chiamante:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
L'esempio JS seguente ottiene il testo dalla stringa della query e lo scrive in un file di testo, ovvero in FunctionsTest.txt, come definito nella configurazione precedente, nella radice di OneDrive del chiamante. In `function.json` sopra sostituire prima `$return` con `res`.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Associazione di output per i messaggi di Outlook

Inviare un messaggio di posta elettronica tramite Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Configurazione di un'associazione di output per i messaggi di Outlook

Questa associazione richiede le autorizzazioni AAD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Inviare un messaggio di posta elettronica come utente|

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `outlook`.|
|**direction**|Obbligatoria. Deve essere impostata su `out`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Usare un'associazione di output per i messaggi di Outlook nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipi di oggetto personalizzati tramite l'associazione di modelli strutturali

#### <a name="sample-sending-an-email-through-outlook"></a>Esempio: invio di un messaggio di posta elettronica tramite Outlook

Si supponga di avere il file function.json seguente che definisce un trigger HTTP con un'associazione di output per i messaggi di Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente invia un messaggio di posta dal chiamante a un destinatario specificato nella stringa della query:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

L'esempio JS seguente invia un messaggio di posta dal chiamante a un destinatario specificato nella stringa della query:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Associazioni per il webhook di Microsoft Graph

I webhook consentono di rispondere agli eventi in Microsoft Graph. Per supportare i webhook, le funzioni devono creare, aggiornare e rispondere alle _sottoscrizioni webhook_. Una soluzione webhook completa richiede una combinazione delle associazioni seguenti:
- Un [trigger webhook di Microsoft Graph](#webhook-trigger) che consente di rispondere a un webhook in ingresso.
- Un'[associazione di input per la sottoscrizione webhook di Microsoft Graph](#webhook-input) che consente di elencare le sottoscrizioni esistenti e, facoltativamente, di aggiornarle.
- Un'[associazione di output per la sottoscrizione webhook di Microsoft Graph](#webhook-output) che consente di creare o eliminare le sottoscrizioni webhook.

Le associazioni stesse non richiedono alcuna autorizzazione AAD, ma è necessario richiedere le autorizzazioni relative al tipo di risorsa a cui si desidera rispondere. Per un elenco delle autorizzazioni necessarie per ogni tipo di risorsa, consultare le [autorizzazioni per la sottoscrizione](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Per altre informazioni sui webhook, vedere [Usare i webhook in Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Trigger per il webhook di Microsoft Graph

Questo trigger consente di rispondere a un webhook in ingresso di Microsoft Graph. Ogni istanza del trigger può rispondere a un tipo di risorsa di Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Configurazione di un trigger per il webhook di Microsoft Graph

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `graphWebhook`.|
|**direction**|Obbligatoria. Deve essere impostata su `trigger`.|
|**resourceType**|Obbligatoria: la risorsa del grafico per cui questa funzione deve rispondere ai webhook. Può avere uno dei valori seguenti:<ul><li><code>#Microsoft.Graph.Message</code>: le modifiche apportate ai messaggi di Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>: le modifiche apportate agli elementi radice di OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> Un'app per le funzioni può avere solo una funzione registrata per un determinato valore di `resourceType`.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Uso di un trigger per il webhook di Microsoft Graph nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- i tipi di SDK di Microsoft Graph rilevanti per il tipo di risorsa, ad esempio Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali

Per esempi sull'uso di questa associazione nel codice, vedere [Esempi di webhook di Microsoft Graph](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Associazione di input per la sottoscrizione webhook di Microsoft Graph

Questa associazione consente di recuperare l'elenco delle sottoscrizioni gestito da questa app per le funzioni. L'associazione legge dall'archivio dell'app per le funzioni e non riflette le altre sottoscrizioni create all'esterno dell'app.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Configurazione dell'associazione di input per la sottoscrizione webhook di Microsoft Graph

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `graphWebhookSubscription`.|
|**direction**|Obbligatoria. Deve essere impostata su `in`.|
|**filter**| Se impostata su `userFromRequest`, l'associazione recupererà solo le sottoscrizioni di proprietà dell'utente chiamante. Questa operazione è valida solo con il [trigger HTTP].| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Uso dell'associazione di input per la sottoscrizione webhook di Microsoft Graph nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- string[]
- Matrici per i tipi di oggetto personalizzati
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

Per esempi sull'uso di questa associazione nel codice, vedere [Esempi di webhook di Microsoft Graph](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Associazione di output per la sottoscrizione webhook di Microsoft Graph

Questa associazione consente di creare, eliminare e aggiornare le sottoscrizioni webhook in Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Configurazione dell'associazione di output per la sottoscrizione webhook di Microsoft Graph

L'associazione supporta le proprietà seguenti:

|Proprietà|Descrizione|
|--------|--------|
|**nome**|Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**|Obbligatoria. Deve essere impostata su `graphWebhookSubscription`.|
|**direction**|Obbligatoria. Deve essere impostata su `out`.|
|**identity**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**action**|Obbligatoria: specifica l'azione che l'associazione deve eseguire. Può avere uno dei valori seguenti:<ul><li><code>create</code>: registra una nuova sottoscrizione.</li><li><code>delete</code>: elimina una sottoscrizione specifica.</li><li><code>refresh</code>: consente di aggiornare una sottoscrizione specifica per impedire che scada.</li></ul>|
|**subscriptionResource**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Specifica la risorsa di Microsoft Graph di cui verranno monitorate le modifiche. Vedere [Usare i webhook in Microsoft Graph]. |
|**changeType**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Indica il tipo di modifica nella risorsa sottoscritta che genera una notifica. I valori supportati sono: `created`, `updated`, `deleted`. È possibile combinare più valori usando un elenco delimitato da virgole.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Uso di un'associazione di output per la sottoscrizione webhook di Microsoft Graph nel codice

L'associazione espone i tipi seguenti nelle funzioni .NET:
- string
- Microsoft.Graph.Subscription

Per esempi sull'uso di questa associazione nel codice, vedere [Esempi di webhook di Microsoft Graph](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Esempi di webhook di Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Esempio: creazione di una sottoscrizione

Si supponga di avere il file function.json seguente, che definisce un trigger HTTP con un'associazione di output per la sottoscrizione tramite un'azione di creazione:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente registra un webhook che invia una notifica all'app per le funzioni quando l'utente chiamante riceve un messaggio di Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

L'esempio JS seguente registra un webhook che invia una notifica all'app per le funzioni quando l'utente chiamante riceve un messaggio di Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Esempio: gestione delle notifiche

Si supponga di avere il file function.json seguente che definisce il trigger webhook di Graph per gestire i messaggi di Outlook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente risponde ai messaggi di posta in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Funzioni di Azure" nell'oggetto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

L'esempio JS seguente risponde ai messaggi di posta in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Funzioni di Azure" nell'oggetto:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Esempio: eliminazione delle sottoscrizioni

Si supponga di avere il file function.json seguente, che definisce un trigger HTTP con un'associazione di input e un'associazione di output per la sottoscrizione tramite un'azione di eliminazione:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente recupera tutte le sottoscrizioni per l'utente chiamante e le elimina:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

L'esempio JS seguente recupera tutte le sottoscrizioni per l'utente chiamante e le elimina:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Esempio: aggiornamento delle sottoscrizioni

Esistono due approcci all'aggiornamento delle sottoscrizioni:
- Usare l'identità di applicazione per gestire tutte le sottoscrizioni. Questa operazione richiede il consenso di un amministratore di Azure Active Directory. Può essere usata con tutte le lingue supportate da Funzioni di Azure.
- Usare l'identità associata a ogni sottoscrizione associando manualmente ogni ID utente. Affinché questa operazione esegua l'associazione è necessario il codice personalizzato. Questa può essere usata solo dalle funzioni .NET.

Entrambe le opzioni sono mostrate di seguito.

**Uso dell'identità di applicazione**

Si supponga di avere il file function.json seguente, che definisce un trigger timer con un'associazione di input e un'associazione di output per la sottoscrizione tramite l'identità di applicazione:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente consente di aggiornare le sottoscrizioni in base a un timer, usando l'identità di applicazione:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

L'esempio JS seguente consente di aggiornare le sottoscrizioni in base a un timer, usando l'identità di applicazione:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Uso delle identità utente dinamiche**

Per l'opzione alternativa, si supponga di avere il file function.json seguente che definisce un trigger timer, posticipando l'associazione all'associazione di input per la sottoscrizione nel codice della funzione:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

L'esempio C# seguente consente di aggiornare le sottoscrizioni in base a un timer, usando ogni identità dell'utente tramite la creazione dell'associazione di output nel codice:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[trigger HTTP]: functions-bindings-http-webhook.md
[Usare i webhook in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

