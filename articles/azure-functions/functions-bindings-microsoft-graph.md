---
title: Associazioni di Microsoft Graph per le funzioni di Azure
description: Informazioni su come usare trigger e associazioni di Microsoft Graph in Funzioni di Azure.
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 63b94c0a9b77a3f3a6fd394a130bf8f132d51369
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Associazioni di Microsoft Graph per le funzioni di Azure

Questo articolo illustra come configurare e usare trigger e associazioni di Microsoft Graph in Funzioni di Azure. I trigger e le associazioni consentono di usare Funzioni di Azure con dati, informazioni dettagliate ed eventi di [Microsoft Graph](https://graph.microsoft.io).

L'estensione Microsoft Graph offre le associazioni seguenti:
- Un'[associazione di input per il token di autenticazione](#token-input) che consente di interagire con le API di Microsoft Graph.
- Un'[associazione di input per la tabella di Excel](#excel-input) che consente di leggere i dati di Excel.
- Un'[associazione di output per la tabella di Excel](#excel-output) che consente di modificare i dati di Excel.
- Oggetto [OneDrive file di input associazione](#onedrive-input) consente di leggere i file da OneDrive.
- Oggetto [OneDrive file di associazione di output](#onedrive-output) consente di scrivere i file in OneDrive.
- Un'[associazione di output per i messaggi di Outlook](#outlook-output) che consente di inviare un messaggio di posta elettronica tramite Outlook.
- Una raccolta di [trigger e associazioni per il webhook di Microsoft Graph](#webhooks) che consente di rispondere agli eventi di Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Le associazioni di Microsoft Graph sono attualmente in anteprima.

## <a name="setting-up-the-extensions"></a>Configurazione delle estensioni

Le associazioni di Microsoft Graph sono disponibili tramite le _estensioni dell'associazione_. Le estensioni dell'associazione sono componenti facoltativi per il runtime di Funzioni di Azure. In questa sezione viene illustrato come configurare le estensioni di token di autenticazione e di Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Abilitazione dell'anteprima di Funzioni 2.0

Sono disponibili solo per l'anteprima di Azure 2.0 funzioni estensioni di associazione. 

Per informazioni su come impostare un'app di funzione per utilizzare la versione di anteprima 2.0 del runtime di funzioni, vedere [come destinazione il runtime versione 2.0](functions-versions.md#target-the-version-20-runtime).

### <a name="installing-the-extension"></a>Installazione dell'estensione

Per installare un'estensione dal portale di Azure, passare a un modello o di associazione che fa riferimento a essa. Creare una nuova funzione e nella schermata di selezione del modello, scegliere lo scenario "Microsoft Graph". Selezionare uno dei modelli di questo scenario. In alternativa, è possibile passare alla scheda "Integrazione" di una funzione esistente e selezionare una delle associazioni illustrate in questo articolo.

In entrambi i casi, verrà visualizzato un avviso che specifica l'estensione da installare. Fare clic su **Installa** per ottenere l'estensione.

> [!Note] 
> Ogni estensione deve essere installata una sola volta per ogni app per le funzioni. In un piano a consumo il processo di installazione nel portale può richiedere fino a 10 minuti.

Se si usa Visual Studio, è possibile ottenere le estensioni installando questi pacchetti NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-authentication--authorization"></a>Configurazione dell'autenticazione / autorizzazione

Le associazioni descritte in questo articolo richiedono un'identità da utilizzare. In questo modo Microsoft Graph applica le autorizzazioni e le interazioni di controllo. L'identità può essere un utente con accesso all'applicazione o l'applicazione stessa. Per configurare l'identità, impostare [l'autenticazione del servizio App / autorizzazione](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) con Azure Active Directory. È anche necessario richiedere tutte le autorizzazioni delle risorse richieste per le funzioni.

> [!Note] 
> L'estensione di Microsoft Graph supporta solo l'autenticazione di Azure AD. Gli utenti devono accedere con un account aziendale o dell'istituto di istruzione.

Se si usa il portale di Azure, si verrà visualizzato un avviso sotto il prompt dei comandi per installare l'estensione. L'avviso viene richiesto di configurare l'autenticazione del servizio App / richiesta e autorizzazione richiede le autorizzazioni di un modello o dell'associazione. Fare clic su **Configura Azure AD ora** o **aggiungere autorizzazioni ora** come appropriato.



<a name="token-input"></a>
## <a name="auth-token"></a>Token di autenticazione

L'associazione di input token di autenticazione Ottiene token di Azure Active Directory per una determinata risorsa e fornisce il codice sotto forma di stringa. La risorsa può essere una qualsiasi risorsa per cui l'applicazione dispone di autorizzazioni. 

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#auth-token---example)
* [Attributes (Attributi) (Attributi)](#auth-token---attributes)
* [Configurazione](#auth-token---configuration)
* [Utilizzo](#auth-token---usage)

### <a name="auth-token---example"></a>Token di autenticazione - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token di autenticazione: esempio di script c#

Nell'esempio seguente ottiene informazioni sul profilo utente.

Il *function.json* file definisce un trigger HTTP con un'associazione di input token:

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

Il codice di script c# utilizza il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato:

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

#### <a name="auth-token---javascript-example"></a>Token di autenticazione - esempio JavaScript

Nell'esempio seguente ottiene informazioni sul profilo utente.

Il *function.json* file definisce un trigger HTTP con un'associazione di input token:

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

Il codice JavaScript Usa il token per effettuare una chiamata HTTP a Microsoft Graph e restituisce il risultato.

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

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/).

### <a name="auth-token---configuration"></a>Token di autenticazione - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Token`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il token di autenticazione. Vedere [Usare un'associazione di input per il token di autenticazione nel codice](#token-input-code).|
|**type**||Obbligatoria. Deve essere impostata su `token`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**identity**|**Identità**|Obbligatoria: l'identità che verrà usata per eseguire l'azione. Può avere uno dei valori seguenti:<ul><li><code>userFromRequest</code>: valido solo con il [trigger HTTP]. Usa l'identità dell'utente chiamante.</li><li><code>userFromId</code>: usa l'identità di un utente registrato in precedenza con l'ID specificato. Vedere la proprietà <code>userId</code>.</li><li><code>userFromToken</code>: usa l'identità rappresentata dal token specificato. Vedere la proprietà <code>userToken</code>.</li><li><code>clientCredentials</code>: usa l'identità dell'app per le funzioni.</li></ul>|
|**userId**|**UserId**  |Necessaria solo nel caso in cui _identity_ sia impostata su `userFromId`. ID dell'entità utente associato a un utente registrato in precedenza.|
|**userToken**|**UserToken**|Necessaria solo nel caso in cui _identity_ sia impostata su `userFromToken`. Un token valido per l'app per le funzioni. |
|**Risorsa**|**resource**|Richiesta un URL di risorsa di Azure AD per cui viene richiesto il token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token di autenticazione - utilizzo

L'associazione stessa non richiede alcuna autorizzazione di Azure AD, ma a seconda di come il token viene usato, potrebbe essere necessario richiedere autorizzazioni aggiuntive. Verificare i requisiti della risorsa a cui si desidera accedere con il token.

Il token viene sempre visualizzato nel codice sotto forma di stringa.




<a name="excel-input"></a>
## <a name="excel-input"></a>Input di Excel

L'associazione di input nella tabella di Excel legge il contenuto di una tabella di Excel archiviata in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#excel-input---example)
* [Attributes (Attributi) (Attributi)](#excel-input---attributes)
* [Configurazione](#excel-input---configuration)
* [Utilizzo](#excel-input---usage)

### <a name="excel-input---example"></a>Excel input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel input - esempio di script c#

Nell'esempio *function.json* file definisce un trigger HTTP con un'associazione di input di Excel:

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

Il codice di script c# seguente legge il contenuto della tabella specificata e li restituisce all'utente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel input - esempio JavaScript

Nell'esempio *function.json* file definisce un trigger HTTP con un'associazione di input di Excel:

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

Il codice JavaScript seguente legge il contenuto della tabella specificata e li restituisce all'utente.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel - input gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-input---configuration"></a>Excel input - configurazione

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
### <a name="excel-input---usage"></a>Excel input - utilizzo

Questa associazione richiede le autorizzazioni di Azure Active Directory seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Microsoft.Graph.WorkbookTable
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali










<a name="excel-output"></a>
## <a name="excel-output"></a>Output di Excel

Excel associazione di output modifica il contenuto di una tabella di Excel archiviata in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#excel-output---example)
* [Attributes (Attributi) (Attributi)](#excel-output---attributes)
* [Configurazione](#excel-output---configuration)
* [Utilizzo](#excel-output---usage)

### <a name="excel-output---example"></a>Excel - output esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel output - esempio di script c#

Nell'esempio seguente aggiunge righe a una tabella di Excel.

Il *function.json* file definisce un trigger HTTP con un Excel associazione di output:

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

Il codice di script c# aggiunge una nuova riga alla tabella (considerato uguale a colonna singola) in base all'input dalla stringa di query:

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

#### <a name="excel-output---javascript-example"></a>Excel - output esempio JavaScript

Nell'esempio seguente aggiunge righe a una tabella di Excel.

Il *function.json* file definisce un trigger HTTP con un Excel associazione di output:

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

Il codice JavaScript seguente aggiunge una nuova riga alla tabella (considerato uguale a colonna singola) in base all'input dalla stringa di query.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel - output gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-output---configuration"></a>Excel output - configurazione

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
|**updateType**|**Tipo aggiornamento**|Obbligatoria: tipo di modifica da apportare alla tabella. Può avere uno dei valori seguenti:<ul><li><code>update</code>: sostituisce il contenuto della tabella in OneDrive.</li><li><code>append</code>: aggiunge il payload alla fine della tabella in OneDrive mediante la creazione di nuove righe.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel output - utilizzo

Questa associazione richiede le autorizzazioni di Azure Active Directory seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Accesso completo ai file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali





<a name="onedrive-input"></a>
## <a name="file-input"></a>Input del file

Associazione di input OneDrive File legge il contenuto di un file archiviati in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#file-input---example)
* [Attributes (Attributi) (Attributi)](#file-input---attributes)
* [Configurazione](#file-input---configuration)
* [Utilizzo](#file-input---usage)

### <a name="file-input---example"></a>File di input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>File di input - esempio di script c#

Nell'esempio seguente viene letto un file che viene archiviato in OneDrive.

Il *function.json* file definisce un trigger HTTP con un'associazione di input di file di OneDrive:

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

Il codice di script c# legge il file specificato nella stringa di query e registra la sua lunghezza:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>File di input - esempio JavaScript

Nell'esempio seguente viene letto un file che viene archiviato in OneDrive.

Il *function.json* file definisce un trigger HTTP con un'associazione di input di file di OneDrive:

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

Il codice JavaScript seguente legge il file specificato nella stringa di query e restituisce la lunghezza.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>File di input, gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-input---configuration"></a>File di input - configurazione

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
### <a name="file-input---usage"></a>File di input - utilizzo

Questa associazione richiede le autorizzazioni di Azure Active Directory seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Leggere i file dell'utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- byte[]
- Flusso
- stringa
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>File di output

Il file di OneDrive associazione di output modifica il contenuto di un file archiviati in OneDrive.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#file-output---example)
* [Attributes (Attributi) (Attributi)](#file-output---attributes)
* [Configurazione](#file-output---configuration)
* [Utilizzo](#file-output---usage)

### <a name="file-output---example"></a>File di output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>File di output - esempio di script c#

Nell'esempio seguente scrive in un file che viene archiviato in OneDrive.

Il *function.json* file definisce un trigger HTTP con un OneDrive associazione di output:

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

Il codice di script c# Ottiene il testo dalla stringa di query e lo scrive nella radice OneDrive del chiamante in un file di testo (FunctionsTest.txt come definito nell'esempio precedente):

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

#### <a name="file-output---javascript-example"></a>File di output - esempio JavaScript

Nell'esempio seguente scrive in un file che viene archiviato in OneDrive.

Il *function.json* file definisce un trigger HTTP con un OneDrive associazione di output:

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

Il codice JavaScript Ottiene il testo dalla stringa di query e lo scrive in un file di testo (come definito nella configurazione precedente, FunctionsTest.txt) nella directory principale di OneDrive del chiamante.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>File di output, gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-output---configuration"></a>File di output - configurazione

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
#### <a name="file-output---usage"></a>File di output - utilizzo

Questa associazione richiede le autorizzazioni di Azure Active Directory seguenti:
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

Il messaggio di Outlook output associazione invia un messaggio di posta elettronica tramite Outlook.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#outlook-output---example)
* [Attributes (Attributi) (Attributi)](#outlook-output---attributes)
* [Configurazione](#outlook-output---configuration)
* [Utilizzo](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Output di Outlook - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook output - esempio di script c#

Nell'esempio seguente invia un messaggio di posta elettronica tramite Outlook.

Il *function.json* file definisce un trigger HTTP con un Outlook associazione di messaggio di output:

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

Il codice di script c# invia un messaggio di posta dal chiamante per un destinatario specificato nella stringa di query:

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

#### <a name="outlook-output---javascript-example"></a>Outlook output - esempio JavaScript

Nell'esempio seguente invia un messaggio di posta elettronica tramite Outlook.

Il *function.json* file definisce un trigger HTTP con un Outlook associazione di messaggio di output:

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

Il codice JavaScript invia un messaggio di posta dal chiamante per un destinatario specificato nella stringa di query:

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

### <a name="outlook-output---attributes"></a>Output - Outlook gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="outlook-output---configuration"></a>Output di Outlook - configurazione

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
### <a name="outlook-output---usage"></a>Outlook output - utilizzo

Questa associazione richiede le autorizzazioni di Azure Active Directory seguenti:
|Risorsa|Autorizzazione|
|--------|--------|
|Microsoft Graph|Inviare un messaggio di posta elettronica come utente|

L'associazione espone i tipi seguenti nelle funzioni .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- stringa
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali






## <a name="webhooks"></a>Webhook

I webhook consentono di rispondere agli eventi in Microsoft Graph. Per supportare i webhook, le funzioni devono creare, aggiornare e rispondere alle _sottoscrizioni webhook_. Una soluzione completa webhook richiede una combinazione delle associazioni seguenti:
- Un [trigger webhook di Microsoft Graph](#webhook-trigger) che consente di rispondere a un webhook in ingresso.
- Un'[associazione di input per la sottoscrizione webhook di Microsoft Graph](#webhook-input) che consente di elencare le sottoscrizioni esistenti e, facoltativamente, di aggiornarle.
- Un'[associazione di output per la sottoscrizione webhook di Microsoft Graph](#webhook-output) che consente di creare o eliminare le sottoscrizioni webhook.

Le associazioni se stessi non richiedono alcuna autorizzazione di Azure AD, ma è necessario richiedere le autorizzazioni relative al tipo di risorsa che si desidera rispondere al. Per un elenco delle autorizzazioni necessarie per ogni tipo di risorsa, consultare le [autorizzazioni per la sottoscrizione](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Per ulteriori informazioni su webhook, vedere [utilizzo webhook in Microsoft Graph].





## <a name="webhook-trigger"></a>Trigger Webhook

Il trigger webhook Microsoft Graph consente a una funzione rispondere a un webhook in ingresso da Microsoft Graph. Ogni istanza del trigger può rispondere a un tipo di risorsa di Microsoft Graph.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-trigger---example)
* [Attributes (Attributi) (Attributi)](#webhook-trigger---attributes)
* [Configurazione](#webhook-trigger---configuration)
* [Utilizzo](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Trigger Webhook - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Trigger Webhook - esempio di script c#

Nell'esempio seguente gestisce webhook dei messaggi in arrivo di Outlook. Per utilizzare un webhook trigger si [creare una sottoscrizione](#webhook-output---example), ed è possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scadano.

Il *function.json* file definisce un trigger webhook:

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

Il codice di script c# risponde ai messaggi di posta elettronica in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Azure funzioni" nell'oggetto:

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

#### <a name="webhook-trigger---javascript-example"></a>Trigger Webhook - esempio JavaScript

Nell'esempio seguente gestisce webhook dei messaggi in arrivo di Outlook. Per utilizzare un webhook trigger si [creare una sottoscrizione](#webhook-output---example), ed è possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scadano.

Il *function.json* file definisce un trigger webhook:

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

Il codice JavaScript risponde ai messaggi di posta elettronica in arrivo e registra il corpo di quelli inviati dal destinatario e contenenti "Azure funzioni" nell'oggetto:

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

### <a name="webhook-trigger---attributes"></a>Trigger Webhook: attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-trigger---configuration"></a>Trigger Webhook - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `GraphWebHookTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `graphWebhook`.|
|**direction**||Obbligatoria. Deve essere impostata su `trigger`.|
|**resourceType**|**Tipo di risorsa**|Obbligatoria: la risorsa del grafico per cui questa funzione deve rispondere ai webhook. Può avere uno dei valori seguenti:<ul><li><code>#Microsoft.Graph.Message</code>: le modifiche apportate ai messaggi di Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>: le modifiche apportate agli elementi radice di OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>-le modifiche apportate ai contatti personali di Outlook.</li><li><code>#Microsoft.Graph.Event</code>-le modifiche apportate agli elementi di calendario di Outlook.</li></ul>|

> [!Note]
> Un'app di funzione può avere solo una funzione che viene registrata per un determinato `resourceType` valore.

### <a name="webhook-trigger---usage"></a>Trigger Webhook - utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- SDK di Microsoft Graph tipi rilevanti per il tipo di risorsa, ad esempio `Microsoft.Graph.Message` o `Microsoft.Graph.DriveItem`.
- i tipi di oggetto personalizzati tramite l'associazione di modelli strutturali




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Input Webhook

Associazione di input webhook Microsoft Graph consente di recuperare l'elenco delle sottoscrizioni gestito da questa app di funzione. L'associazione legge dall'archivio di app di funzione, in modo non riflette le altre sottoscrizioni creati da all'esterno dell'app.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-input---example)
* [Attributes (Attributi) (Attributi)](#webhook-input---attributes)
* [Configurazione](#webhook-input---configuration)
* [Utilizzo](#webhook-input---usage)

### <a name="webhook-input---example"></a>Input Webhook - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook input - esempio di script c#

Nell'esempio seguente ottiene tutte le sottoscrizioni per l'utente chiamante e li elimina.

Il *function.json* file definisce un trigger HTTP con un'associazione di input di sottoscrizione e un output della sottoscrizione di associazione che utilizza l'azione di eliminazione:

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

Il codice di script c# Ottiene le sottoscrizioni e li elimina:

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

#### <a name="webhook-input---javascript-example"></a>Webhook input - esempio JavaScript

Nell'esempio seguente ottiene tutte le sottoscrizioni per l'utente chiamante e li elimina.

Il *function.json* file definisce un trigger HTTP con un'associazione di input di sottoscrizione e un output della sottoscrizione di associazione che utilizza l'azione di eliminazione:

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

Il codice JavaScript Ottiene le sottoscrizioni e li elimina:

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

### <a name="webhook-input---attributes"></a>Webhook input: attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-input---configuration"></a>Input Webhook - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `GraphWebHookSubscription`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**nome**||Obbligatoria: nome della variabile usato nel codice della funzione per il messaggio di posta elettronica. Vedere [Usare un'associazione di output per i messaggi di Outlook nel codice](#outlook-output-code).|
|**type**||Obbligatoria. Deve essere impostata su `graphWebhookSubscription`.|
|**direction**||Obbligatoria. Deve essere impostata su `in`.|
|**filter**|**Filter**| Se impostato su `userFromRequest`, quindi l'associazione verrà recuperare solo le sottoscrizioni di proprietà dell'utente chiamante (valido solo con [trigger HTTP]).| 

### <a name="webhook-input---usage"></a>Webhook input - utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- string[]
- Matrici per i tipi di oggetto personalizzati
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Output Webhook

La sottoscrizione webhook associazione di output consente di creare, eliminare e aggiornare le sottoscrizioni di webhook in Microsoft Graph.

In questa sezione sono disponibili le procedure seguenti:

* [Esempio](#webhook-output---example)
* [Attributes (Attributi) (Attributi)](#webhook-output---attributes)
* [Configurazione](#webhook-output---configuration)
* [Utilizzo](#webhook-output---usage)

### <a name="webhook-output---example"></a>Output Webhook - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook output - esempio di script c#

Nell'esempio seguente viene creata una sottoscrizione. È possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scadano.

Il *function.json* file definisce un trigger HTTP con un output della sottoscrizione di associazione tramite l'azione di creazione:

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

Il codice di script c# registra un webhook di questa app di funzione notifica quando l'utente chiamante riceve un messaggio di Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Webhook output - esempio JavaScript

Nell'esempio seguente viene creata una sottoscrizione. È possibile [aggiornare la sottoscrizione](#webhook-subscription-refresh) per evitare che scadano.

Il *function.json* file definisce un trigger HTTP con un output della sottoscrizione di associazione tramite l'azione di creazione:

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

Il codice JavaScript registra un webhook di questa app di funzione notifica quando l'utente chiamante riceve un messaggio di Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Output - Webhook gli attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-output---configuration"></a>Output Webhook - configurazione

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
|**subscriptionResource**|**SubscriptionResource**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Specifica la risorsa di Microsoft Graph di cui verranno monitorate le modifiche. Vedere [utilizzo webhook in Microsoft Graph]. |
|**changeType**|**ChangeType**|Necessaria solo nel caso in cui _action_ sia impostata su `create`. Indica il tipo di modifica nella risorsa sottoscritta che genera una notifica. I valori supportati sono: `created`, `updated`, `deleted`. È possibile combinare più valori usando un elenco delimitato da virgole.|

### <a name="webhook-output---usage"></a>Webhook output - utilizzo

L'associazione espone i tipi seguenti nelle funzioni .NET:
- stringa
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aggiornamento sottoscrizione Webhook

Esistono due approcci all'aggiornamento delle sottoscrizioni:

- Usare l'identità di applicazione per gestire tutte le sottoscrizioni. Questa operazione richiede il consenso di un amministratore di Azure Active Directory. Può essere usata con tutte le lingue supportate da Funzioni di Azure.
- Usare l'identità associata a ogni sottoscrizione associando manualmente ogni ID utente. Affinché questa operazione esegua l'associazione è necessario il codice personalizzato. Questa può essere usata solo dalle funzioni .NET.

In questa sezione contiene un esempio per ognuno di questi approcci:

* [Esempio di identità per App](#webhook-subscription-refresh---app-identity-example)
* [Esempio di identità utente](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aggiornamento sottoscrizione Webhook - esempio di identità per app

Vedere l'esempio specifico per ciascun linguaggio:

* [Script c# (con estensione csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Aggiornamento delle identità di App - esempio di script c#

Nell'esempio seguente viene utilizzata l'identità dell'applicazione per aggiornare una sottoscrizione.

Il *function.json* definisce un trigger timer con una sottoscrizione di associazione di input e una sottoscrizione di associazione di output:

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

Il codice di script c# consente di aggiornare le sottoscrizioni:

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

### <a name="app-identity-refresh---c-script-example"></a>Aggiornamento delle identità di App - esempio di script c#

Nell'esempio seguente viene utilizzata l'identità dell'applicazione per aggiornare una sottoscrizione.

Il *function.json* definisce un trigger timer con una sottoscrizione di associazione di input e una sottoscrizione di associazione di output:

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

Il codice JavaScript consente di aggiornare le sottoscrizioni:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Aggiornamento sottoscrizione Webhook - esempio di identità utente

Nell'esempio seguente viene utilizzata l'identità dell'utente per aggiornare una sottoscrizione.

Il *function.json* file definisce un trigger timer e rinvia l'associazione di input di sottoscrizione per il codice della funzione:

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

Il codice di script c# consente di aggiornare le sottoscrizioni e crea l'associazione di output nel codice, utilizzando l'identità di ciascun utente:

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
[utilizzo webhook in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
