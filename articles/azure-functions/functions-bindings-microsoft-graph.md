---
title: Associazioni di Microsoft Graph per Funzioni di Azure
description: Informazioni su come usare trigger e associazioni di Microsoft Graph in Funzioni di Azure.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: d774f0ca644793235a8c423b052b559d26e289c4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Associazioni di Microsoft Graph per Funzioni di Azure

Questo articolo illustra come configurare e usare trigger e associazioni di Microsoft Graph in Funzioni di Azure. I trigger e le associazioni consentono di usare Funzioni di Azure con dati, informazioni dettagliate ed eventi di [Microsoft Graph](https://graph.microsoft.io).

L'estensione Microsoft Graph offre le associazioni seguenti:
- Un'[associazione di input per il token di autenticazione](#token-input) che consente di interagire con le API di Microsoft Graph.
- Un'[associazione di input per la tabella di Excel](#excel-input) che consente di leggere i dati di Excel.
- Un'[associazione di output per la tabella di Excel](#excel-output) che consente di modificare i dati di Excel.
- Un'[associazione di input per i file di OneDrive](#onedrive-input) che consente di leggere i file di OneDrive.
- Un'[associazione di output per i file di OneDrive](#onedrive-output) che consente di scrivere nei file di OneDrive.
- Un'[associazione di output per i messaggi di Outlook](#outlook-output) che consente di inviare un messaggio di posta elettronica tramite Outlook.
- Una raccolta di [trigger e associazioni per il webhook di Microsoft Graph](#webhooks) che consente di rispondere agli eventi di Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Le associazioni di Microsoft Graph sono attualmente in anteprima per la versione 2.x di Funzioni di Azure. Non sono supportate in Funzioni versione 1.x.

## <a name="packages"></a>Pacchetti

L'associazione di input per il token di autenticazione è disponibile nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/). Le altre associazioni di Microsoft Graph sono incluse nel pacchetto [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/). Il codice sorgente del pacchetto si trova nel repository GitHub [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="setting-up-the-extensions"></a>Configurazione delle estensioni

Le associazioni di Microsoft Graph sono disponibili tramite le _estensioni dell'associazione_. Le estensioni dell'associazione sono componenti facoltativi per il runtime di Funzioni di Azure. Questa sezione illustra come configurare le estensioni dei token di autenticazione e di Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Abilitazione dell'anteprima di Funzioni 2.0

Le estensioni dell'associazione sono disponibili per l'anteprima di Funzioni di Azure 2.0. 

Per informazioni su come impostare un'app per le funzioni per l'uso della versione di anteprima 2.0 del runtime per Funzioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installazione dell'estensione

Per installare un'estensione dal portale di Azure, passare a un modello o a un'associazione che vi fa riferimento. Creare una nuova funzione e nella schermata di selezione del modello, scegliere lo scenario "Microsoft Graph". Selezionare uno dei modelli di questo scenario. In alternativa, è possibile passare alla scheda "Integrazione" di una funzione esistente e selezionare una delle associazioni trattate in questo articolo.

In entrambi i casi, verrà visualizzato un avviso che specifica l'estensione da installare. Fare clic su **Installa** per ottenere l'estensione. Ogni estensione deve essere installata una sola volta per ogni app per le funzioni. 

> [!Note] 
> In un piano a consumo il processo di installazione nel portale può richiedere fino a 10 minuti.

Se si usa Visual Studio, è possibile ottenere le estensioni installando i [pacchetti NuGet elencati in precedenza in questo articolo](#packages).

### <a name="configuring-authentication--authorization"></a>La configurazione del servizio di autenticazione/autorizzazione è in corso

Le associazioni descritte in questo articolo richiedono l'uso di un'identità. In questo modo Microsoft Graph applica le autorizzazioni e le interazioni di controllo. L'identità può essere un utente con accesso all'applicazione o l'applicazione stessa. Per configurare l'identità, impostare l'[autenticazione/autorizzazione del servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) con Azure Active Directory. È anche necessario richiedere tutte le autorizzazioni delle risorse richieste per le funzioni.

> [!Note] 
> L'estensione Microsoft Graph supporta solo l'autenticazione di Azure AD. Gli utenti devono accedere con un account aziendale o dell'istituto di istruzione.

Se si usa il portale di Azure, verrà visualizzato un avviso sotto il prompt relativo all'installazione dell'estensione. Gli avvisi chiedono di configurare l'autenticazione/autorizzazione del servizio app e richiedono eventuali autorizzazioni necessarie per il modello o l'associazione. Fare clic su **Configura AAD adesso** o **Aggiungi autorizzazioni adesso** in base alle esigenze.



<a name="token-input"></a>
## <a name="auth-token"></a>Token di autenticazione

Questa associazione di input per il token di autenticazione ottiene un token Azure AD per una determinata risorsa e lo inserisce nel codice sotto forma di stringa. La risorsa può essere una qualsiasi risorsa per cui l'applicazione dispone di autorizzazioni. 

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#auth-token---example)
* [Attributes (Attributi) (Attributi)](#auth-token---attributes)
* [Configurazione](#auth-token---configuration)
* [Utilizzo](#auth-token---usage)

### <a name="auth-token---example"></a>Token di autenticazione: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token di autenticazione: esempio di script C#

L'esempio seguente ottiene informazioni sul profilo utente.

Il file *function.json* definisce un trigger HTTP con un'associazione di input per il token:

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

Il codice di script C# usa il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato:

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

#### <a name="auth-token---javascript-example"></a>Token di autenticazione: esempio JavaScript

L'esempio seguente ottiene informazioni sul profilo utente.

Il file *function.json* definisce un trigger HTTP con un'associazione di input per il token:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Il codice JavaScript usa il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato.

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

### <a name="auth-token---attributes"></a>Token di autenticazione: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs).

### <a name="auth-token---configuration"></a>Token di autenticazione: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Token`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il token di autenticazione. Vedere [Usare un'associazione di input per il token di autenticazione nel codice](#token-input-code).|
|**type**||Obbligatoria. Deve essere impostata su `token`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**Risorsa**|**resource**|Obbligatoria: un URL della risorsa Azure AD per il quale viene richiesto il token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token di autenticazione: utilizzo

L'associazione stessa non richiede alcuna autorizzazione di Azure AD, ma a seconda di come viene usato il token, potrebbe essere necessario richiedere altre autorizzazioni. Verificare i requisiti della risorsa a cui si desidera accedere con il token.

Il token viene sempre visualizzato nel codice sotto forma di stringa.




<a name="excel-input"></a>
## <a name="excel-input"></a>Input di Excel

L'associazione di input per la tabella di Excel legge il contenuto di una tabella di Excel archiviata in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#excel-input---example)
* [Attributes (Attributi) (Attributi)](#excel-input---attributes)
* [Configurazione](#excel-input---configuration)
* [Utilizzo](#excel-input---usage)

### <a name="excel-input---example"></a>Input di Excel: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Input di Excel: esempio di script C#

Il file *function.json* seguente definisce un trigger HTTP con un'associazione di input per Excel:

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

Il codice di script C# seguente legge il contenuto della tabella specificata e la restituisce all'utente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Input di Excel: esempio JavaScript

Il file *function.json* seguente definisce un trigger HTTP con un'associazione di input per Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Il codice JavaScript seguente legge il contenuto della tabella specificata e la restituisce all'utente.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Input di Excel: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-input---configuration"></a>Input di Excel: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Excel`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per la tabella di Excel. Vedere [Usare un'associazione di input per la tabella di Excel nel codice](#excel-input-code).|
|**type**||Obbligatoria. Deve essere impostata su `excel`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|**Percorso**|Obbligatoria: il percorso della cartella di lavoro di Excel in OneDrive.|
|**worksheetName**|**WorksheetName**|Il foglio di lavoro in cui si trova la tabella.|
|**tableName**|**TableName**|Nome della tabella. Se non specificato, verrà usato il contenuto del foglio di lavoro.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Input di Excel: utilizzo

Questa associazione richiede le autorizzazioni Azure AD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Microsoft.Graph.WorkbookTable
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali










<a name="excel-output"></a>
## <a name="excel-output"></a>Output di Excel

Questa associazione di output di Excel modifica il contenuto di una tabella di Excel archiviata in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#excel-output---example)
* [Attributes (Attributi) (Attributi)](#excel-output---attributes)
* [Configurazione](#excel-output---configuration)
* [Utilizzo](#excel-output---usage)

### <a name="excel-output---example"></a>Output di Excel: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Output di Excel: esempio di script C#

L'esempio seguente aggiunge righe a una tabella di Excel.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per Excel:

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

Il codice di script C# aggiunge una nuova riga alla tabella, che si presuppone abbia una sola colonna, in base all'input dalla stringa di query:

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

#### <a name="excel-output---javascript-example"></a>Output di Excel: esempio JavaScript

L'esempio seguente aggiunge righe a una tabella di Excel.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Il codice JavaScript seguente aggiunge una nuova riga alla tabella, che si presuppone abbia una sola colonna, in base all'input dalla stringa di query.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Output di Excel: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-output---configuration"></a>Output di Excel: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Excel`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il token di autenticazione. Vedere [Usare un'associazione di ouput per la tabella di Excel nel codice](#excel-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `excel`.|
|**direction**||Obbligatoria. Deve essere impostata su `out`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**UserId** |**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|**Percorso**|Obbligatoria: il percorso della cartella di lavoro di Excel in OneDrive.|
|**worksheetName**|**WorksheetName**|Il foglio di lavoro in cui si trova la tabella.|
|**tableName**|**TableName**|Nome della tabella. Se non specificato, verrà usato il contenuto del foglio di lavoro.|
|**updateType**|**UpdateType**|Obbligatoria: tipo di modifica da apportare alla tabella. Può avere uno dei valori seguenti:<ul><li><code>update</code>: sostituisce il contenuto della tabella in OneDrive.</li><li><code>append</code>: aggiunge il payload alla fine della tabella in OneDrive mediante la creazione di nuove righe.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Output di Excel: utilizzo

Questa associazione richiede le autorizzazioni Azure AD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Accesso completo ai file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali





<a name="onedrive-input"></a>
## <a name="file-input"></a>Input di file

L'associazione di input di file di OneDrive legge il contenuto di un file archiviato in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#file-input---example)
* [Attributes (Attributi) (Attributi)](#file-input---attributes)
* [Configurazione](#file-input---configuration)
* [Utilizzo](#file-input---usage)

### <a name="file-input---example"></a>Input di file: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Input di file: esempio di script C#

L'esempio seguente legge un file archiviato in OneDrive.

Il file *function.json* definisce un trigger HTTP con un'associazione di input per il file OneDrive:

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

Il codice di script C# legge il file specificato nella stringa della query e ne registra la lunghezza:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Input di file: esempio JavaScript

L'esempio seguente legge un file archiviato in OneDrive.

Il file *function.json* definisce un trigger HTTP con un'associazione di input per il file OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Il codice JavaScript seguente legge il file specificato nella stringa della query e ne restituisce la lunghezza.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Input di file: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-input---configuration"></a>Input di file: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `OneDrive`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il file. Vedere [Usare un'associazione di input per i file di OneDrive nel codice](#onedrive-input-code).|
|**type**||Obbligatoria. Deve essere impostata su `onedrive`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|**Percorso**|Obbligatoria: il percorso del file in OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Input di file: utilizzo

Questa associazione richiede le autorizzazioni Azure AD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- byte[]
- Flusso
- stringa
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Output di file

L'associazione di output di file di OneDrive modifica il contenuto di un file archiviato in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#file-output---example)
* [Attributes (Attributi) (Attributi)](#file-output---attributes)
* [Configurazione](#file-output---configuration)
* [Utilizzo](#file-output---usage)

### <a name="file-output---example"></a>Output di file: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Output di file: esempio di script C#

L'esempio seguente scrive in un file archiviato in OneDrive.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per OneDrive:

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

Il codice di script C# ottiene il testo dalla stringa della query e lo scrive in un file di testo, ovvero in FunctionsTest.txt, come definito nell'esempio precedente, nella radice di OneDrive del chiamante:

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

#### <a name="file-output---javascript-example"></a>Output di file: esempio JavaScript

L'esempio seguente scrive in un file archiviato in OneDrive.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Il codice JavaScript ottiene il testo dalla stringa della query e lo scrive in un file di testo, ovvero in FunctionsTest.txt, come definito nell'esempio precedente, nella radice di OneDrive del chiamante.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Output di file: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-output---configuration"></a>Output di file: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `OneDrive`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il file. Vedere [Usare un'associazione di output per i file di OneDrive nel codice](#onedrive-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `onedrive`.|
|**direction**||Obbligatoria. Deve essere impostata su `out`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**UserId** |**userId** |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**path**|**Percorso**|Obbligatoria: il percorso del file in OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Output di file: utilizzo

Questa associazione richiede le autorizzazioni Azure AD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Accesso completo ai file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- byte[]
- Flusso
- stringa
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Output di Outlook

L'associazione di output del messaggio di Outlook invia un messaggio di posta tramite Outlook.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#outlook-output---example)
* [Attributes (Attributi) (Attributi)](#outlook-output---attributes)
* [Configurazione](#outlook-output---configuration)
* [Utilizzo](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Output di Outlook: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Output di Outlook: esempio di script C#

L'esempio seguente invia un messaggio di posta elettronica tramite Outlook.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per il messaggio di Outlook:

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

Il codice di script C# invia un messaggio di posta dal chiamante a un destinatario specificato nella stringa della query:

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

#### <a name="outlook-output---javascript-example"></a>Output di Outlook: esempio JavaScript

L'esempio seguente invia un messaggio di posta elettronica tramite Outlook.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per il messaggio di Outlook:

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

Il codice JavaScript invia un messaggio di posta dal chiamante a un destinatario specificato nella stringa della query:

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

### <a name="outlook-output---attributes"></a>Output di Outlook: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs).

### <a name="outlook-output---configuration"></a>Output di Outlook: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Outlook`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `outlook`.|
|**direction**||Obbligatoria. Deve essere impostata su `out`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Output di Outlook: utilizzo

Questa associazione richiede le autorizzazioni Azure AD seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Inviare un messaggio di posta elettronica come utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- stringa
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali






## <a name="webhooks"></a>Webhook

I webhook consentono di rispondere agli eventi in Microsoft Graph. Per supportare i webhook, le funzioni devono creare, aggiornare e rispondere alle _sottoscrizioni webhook_. Una soluzione webhook completa richiede una combinazione delle associazioni seguenti:
- Un [trigger webhook di Microsoft Graph](#webhook-trigger) che consente di rispondere a un webhook in ingresso.
- Un'[associazione di input per la sottoscrizione webhook di Microsoft Graph](#webhook-input) che consente di elencare le sottoscrizioni esistenti e, facoltativamente, di aggiornarle.
- Un'[associazione di output per la sottoscrizione webhook di Microsoft Graph](#webhook-output) che consente di creare o eliminare le sottoscrizioni webhook.

Le associazioni stesse non richiedono alcuna autorizzazione Azure AD, ma è necessario richiedere le autorizzazioni relative al tipo di risorsa a cui si vuole rispondere. Per un elenco delle autorizzazioni necessarie per ogni tipo di risorsa, consultare le [autorizzazioni per la sottoscrizione](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Per altre informazioni sui webhook, vedere [Usare i webhook in Microsoft Graph].





## <a name="webhook-trigger"></a>Trigger di webhook

Il trigger di webhook di Microsoft Graph consente di rispondere a un webhook in ingresso di Microsoft Graph. Ogni istanza del trigger può rispondere a un tipo di risorsa di Microsoft Graph.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-trigger---example)
* [Attributes (Attributi) (Attributi)](#webhook-trigger---attributes)
* [Configurazione](#webhook-trigger---configuration)
* [Utilizzo](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Trigger di webhook: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Trigger di webhook: esempio di script C#

L'esempio seguente gestisce i webhook per i messaggi di Outlook in arrivo. Per usare un trigger di webhook, si [crea una sottoscrizione](#webhook-output---example) che è possibile [aggiornare](#webhook-subscription-refresh) per evitare che scada.

Il file *function.json* definisce un trigger di webhook:

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

Il codice di script C# risponde ai messaggi di posta in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Funzioni di Azure" nell'oggetto:

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

#### <a name="webhook-trigger---javascript-example"></a>Trigger di webhook: esempio JavaScript

L'esempio seguente gestisce i webhook per i messaggi di Outlook in arrivo. Per usare un trigger di webhook, si [crea una sottoscrizione](#webhook-output---example) che è possibile [aggiornare](#webhook-subscription-refresh) per evitare che scada.

Il file *function.json* definisce un trigger di webhook:

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

Il codice JavaScript risponde ai messaggi di posta in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Funzioni di Azure" nell'oggetto:

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

### <a name="webhook-trigger---attributes"></a>Trigger di webhook: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs).

### <a name="webhook-trigger---configuration"></a>Trigger di webhook: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `GraphWebHookTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `graphWebhook`.|
|**direction**||Obbligatoria. Deve essere impostata su `trigger`.|
|**resourceType**|**ResourceType**|Obbligatoria: la risorsa del grafico per cui questa funzione deve rispondere ai webhook. Può avere uno dei valori seguenti:<ul><li><code>#Microsoft.Graph.Message</code>: le modifiche apportate ai messaggi di Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>: le modifiche apportate agli elementi radice di OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>: modifiche apportate ai contatti personali in Outlook.</li><li><code>#Microsoft.Graph.Event</code>: modifiche apportate agli elementi del calendario di Outlook.</li></ul>|

> [!Note]
> Un'app per le funzioni può avere solo una funzione registrata per un determinato valore di `resourceType`.

### <a name="webhook-trigger---usage"></a>Trigger di webhook: utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- Tipi di Microsoft Graph SDK pertinenti al tipo di risorsa, ad esempio `Microsoft.Graph.Message` o `Microsoft.Graph.DriveItem`.
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Input di webhook

L'associazione di input per il webhook di Microsoft Graph consente di recuperare l'elenco delle sottoscrizioni gestito da questa app per le funzioni. L'associazione legge dall'archiviazione app per le funzioni, quindi non riflette le altre sottoscrizioni create all'esterno dell'app.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-input---example)
* [Attributes (Attributi) (Attributi)](#webhook-input---attributes)
* [Configurazione](#webhook-input---configuration)
* [Utilizzo](#webhook-input---usage)

### <a name="webhook-input---example"></a>Input di webhook: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Input di webhook: esempio di script C#

L'esempio seguente recupera tutte le sottoscrizioni per l'utente chiamante e le elimina.

Il file *function.json* definisce un trigger HTTP con un'associazione di input e un'associazione di output per la sottoscrizione che usa un'azione di eliminazione:

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

Il codice di script C# ottiene le sottoscrizioni e le elimina:

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

#### <a name="webhook-input---javascript-example"></a>Input di webhook: esempio JavaScript

L'esempio seguente recupera tutte le sottoscrizioni per l'utente chiamante e le elimina.

Il file *function.json* definisce un trigger HTTP con un'associazione di input e un'associazione di output per la sottoscrizione che usa un'azione di eliminazione:

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

Il codice JavaScript ottiene le sottoscrizioni e le elimina:

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

### <a name="webhook-input---attributes"></a>Input di webhook: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-input---configuration"></a>Input di webhook: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `GraphWebHookSubscription`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `graphWebhookSubscription`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**filter**|**Filter**| Se impostata su `userFromRequest`, l'associazione recupererà solo le sottoscrizioni di proprietà dell'utente chiamante. Questa operazione è valida solo con il [trigger HTTP].| 

### <a name="webhook-input---usage"></a>Input di webhook: utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- string[]
- Matrici per i tipi di oggetto personalizzati
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Output di webhook

L'associazione di output per la sottoscrizione webhook consente di creare, eliminare e aggiornare le sottoscrizioni webhook in Microsoft Graph.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-output---example)
* [Attributes (Attributi) (Attributi)](#webhook-output---attributes)
* [Configurazione](#webhook-output---configuration)
* [Utilizzo](#webhook-output---usage)

### <a name="webhook-output---example"></a>Output di webhook: esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Output di webhook: esempio di script C#

L'esempio seguente creata una sottoscrizione. È possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scada.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per la sottoscrizione tramite un'azione di creazione:

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

Il codice di script C# registra un webhook che invia una notifica all'app per le funzioni quando l'utente chiamante riceve un messaggio di Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Output di webhook: esempio JavaScript

L'esempio seguente creata una sottoscrizione. È possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scada.

Il file *function.json* definisce un trigger HTTP con un'associazione di output per la sottoscrizione tramite un'azione di creazione:

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

Il codice JavaScript registra un webhook che invia una notifica all'app per le funzioni quando l'utente chiamante riceve un messaggio di Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Output di webhook: attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-output---configuration"></a>Output di webhook: configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `GraphWebHookSubscription`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `graphWebhookSubscription`.|
|**direction**||Obbligatoria. Deve essere impostata su `out`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**action**|**Azione**|Obbligatoria: specifica l'azione che l'associazione deve eseguire. Può avere uno dei valori seguenti:<ul><li><code>create</code>: registra una nuova sottoscrizione.</li><li><code>delete</code>: elimina una sottoscrizione specifica.</li><li><code>refresh</code>: consente di aggiornare una sottoscrizione specifica per impedire che scada.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Specifica la risorsa di Microsoft Graph di cui verranno monitorate le modifiche. Vedere [Usare i webhook in Microsoft Graph]. |
|**changeType**|**ChangeType**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Indica il tipo di modifica nella risorsa sottoscritta che genera una notifica. I valori supportati sono: `created`, `updated`, `deleted`. È possibile combinare più valori usando un elenco delimitato da virgole.|

### <a name="webhook-output---usage"></a>Output di webhook: utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aggiornamento della sottoscrizione webhook

Esistono due approcci all'aggiornamento delle sottoscrizioni:

- Usare l'identità di applicazione per gestire tutte le sottoscrizioni. Questa operazione richiede il consenso di un amministratore di Azure Active Directory. Può essere usata con tutte le lingue supportate da Funzioni di Azure.
- Usare l'identità associata a ogni sottoscrizione associando manualmente ogni ID utente. Affinché questa operazione esegua l'associazione è necessario il codice personalizzato. Questa può essere usata solo dalle funzioni .NET.

In questa sezione contiene un esempio per ognuno di questi approcci:

* [Esempio di identità app](#webhook-subscription-refresh---app-identity-example)
* [Esempio di identità utente](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aggiornamento della sottoscrizione webhook: esempio di identità app

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Aggiornamento dell'identità app: esempio di script C#

L'esempio seguente usa l'identità di applicazione per aggiornare una sottoscrizione.

*function.json* definisce un trigger di timer con un'associazione di input e un'associazione di output per la sottoscrizione:

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

Il codice di script C# aggiorna le sottoscrizioni:

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

### <a name="app-identity-refresh---c-script-example"></a>Aggiornamento dell'identità app: esempio di script C#

L'esempio seguente usa l'identità di applicazione per aggiornare una sottoscrizione.

*function.json* definisce un trigger di timer con un'associazione di input e un'associazione di output per la sottoscrizione:

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

Il codice JavaScript aggiorna le sottoscrizioni:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Aggiornamento della sottoscrizione webhook: esempio di identità utente

L'esempio seguente usa l'identità utente per aggiornare una sottoscrizione.

Il file *function.json* definisce un trigger di timer e rinvia l'associazione di input per la sottoscrizione al codice della funzione:

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

Il codice di script C# aggiorna le sottoscrizioni e crea l'associazione di output nel codice, usando l'identità di ogni utente:

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

[trigger HTTP]: functions-bindings-http-webhook.md
[Usare i webhook in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
