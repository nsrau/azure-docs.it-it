---
title: Test di Funzioni di Azure
description: Creare test automatizzati per una funzione C# in Visual Studio e la funzione JavaScript in Visual Studio Code
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: ff64d5c17174f8e1e67111ebca9ccf050deb2f26
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409655"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie per il test del codice in Funzioni di Azure

Questo articolo illustra come creare i test automatizzati per le Funzioni di Azure.

Si consiglia di testare tutto il codice, tuttavia si potrebbero ottenere risultati migliori eseguendo il wrapping di una logica della funzione e creando dei test all'esterno della funzione. Eseguendo subito l'astrazione per la logica si limitano le righe di una funzione di codice e si consente alla funzione di assumere la piena responsabilità per la chiamata ad altre classi o moduli. In questo articolo, tuttavia, viene illustrato come creare test automatizzati su una funzione attivata da timer e HTTP.

Il contenuto seguente è suddiviso in due sezioni diverse con lo scopo di ottenere diversi linguaggi e ambienti di destinazione. È possibile imparare a compilare i test in:

- [C# in Visual Studio con xUnit](#c-in-visual-studio)
- [JavaScript in Visual Studio Code with Jest](#javascript-in-vs-code)

Il repository di esempio è disponibile in [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# in Visual Studio

Il seguente esempio illustra come creare un'app della funzione C# in Visual Studio ed eseguire i test con [xUnit](https://xunit.github.io).

![Test di Funzioni di Azure con C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configurazione

Per configurare l'ambiente, creare una funzione e testare l'app. I passaggi seguenti consentono di creare le app e le funzioni necessarie per supportare i test:

1. [Creare una nuova app funzioni](./functions-create-first-azure-function.md) e denominarla **Funzioni**
2. [Creare una funzione HTTP dal modello](./functions-create-first-azure-function.md) e denominarla **MyHttpTrigger**.
3. [Creare una funzione timer dal modello](./functions-create-scheduled-function.md) e denominarla **MyTimerTrigger**.
4. [Creare un'app di test xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) nella soluzione e denominarla **Functions. tests**.
5. Usare NuGet per aggiungere un riferimento dall'app di test a [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Fare riferimento all'app *funzioni*](/visualstudio/ide/managing-references-in-a-project?view=vs-2017) dall'app *Functions. tests* .

### <a name="create-test-classes"></a>Crea classi di test

Ora che i progetti sono stati creati, è possibile creare le classi usate per eseguire i test automatizzati.

Ogni funzione accetta un'istanza di [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) per gestire la registrazione dei messaggi. Alcuni test non registrano messaggi oppure non hanno alcun problema per la modalità di implementazione della registrazione. Altri test devono valutare i messaggi registrati per determinare se si sta passando un test.

Verrà creata una nuova classe denominata `ListLogger` che include un elenco interno di messaggi da valutare durante un test. Per implementare l' `ILogger` interfaccia richiesta, la classe richiede un ambito. La classe seguente simula un ambito per i test case da passare alla `ListLogger` classe.

Creare una nuova classe nel progetto *Functions. tests* denominato **NullScope.cs** e immettere il codice seguente:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Successivamente, creare una nuova classe nel progetto *Functions. tests* denominato **ListLogger.cs** e immettere il codice seguente:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

La classe `ListLogger` implementa i membri seguenti come contrattato dall'interfaccia `ILogger`:

- **BeginScope** : gli ambiti aggiungono il contesto alla registrazione. In questo caso, il test punta solo all'istanza statica sulla `NullScope` classe per consentire il funzionamento del test.

- **IsEnabled** : viene fornito un valore predefinito di `false` .

- **Log** : questo metodo usa la `formatter` funzione specificata per formattare il messaggio e quindi aggiunge il testo risultante alla `Logs` raccolta.

La raccolta `Logs` è un'istanza di `List<string>` e viene inizializzata nel costruttore.

Successivamente, creare un nuovo file nel progetto *Functions. tests* denominato **LoggerTypes.cs** e immettere il codice seguente:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Questa enumerazione specifica il tipo di logger usato dai test.

A questo punto, creare una nuova classe nel progetto *Functions. tests* denominato **TestFactory.cs** e immettere il codice seguente:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

La classe `TestFactory` implementa i seguenti membri:

- **Data** : questa proprietà restituisce una raccolta di dati di esempio [IEnumerable](/dotnet/api/system.collections.ienumerable) . Le coppie chiave-valore rappresentano valori che vengono passati in una stringa di query.

- **CreateDictionary** : questo metodo accetta una coppia chiave/valore come argomenti e restituisce un nuovo oggetto `Dictionary` usato per creare `QueryCollection` per rappresentare i valori della stringa di query.

- **CreateHttpRequest** : questo metodo crea una richiesta http inizializzata con i parametri della stringa di query specificati.

- **CreateLogger** : in base al tipo di logger, questo metodo restituisce una classe logger utilizzata per il testing. `ListLogger` tiene traccia dei messaggi registrati disponibili per la valutazione nei test.

Infine, creare una nuova classe nel progetto *Functions. tests* denominato **FunctionsTests.cs** e immettere il codice seguente:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

I membri implementati in questa classe sono:

- **Http_trigger_should_return_known_string** : questo test crea una richiesta con i valori della stringa di query di `name=Bill` in una funzione http e verifica che venga restituita la risposta prevista.

- **Http_trigger_should_return_string_from_member_data** : questo test usa gli attributi xUnit per fornire dati di esempio alla funzione http.

- **Timer_should_log_message** : questo test crea un'istanza di `ListLogger` e la passa a una funzione timer. Una volta eseguita la funzione, il log viene controllato per verificare che sia presente il messaggio previsto.

Se si desidera accedere alle impostazioni dell'applicazione nei test, è possibile [inserire](./functions-dotnet-dependency-injection.md) `IConfiguration` nella funzione un'istanza con valori di variabili di ambiente fittizi.

### <a name="run-tests"></a>Esecuzione dei test

Per eseguire i test, passare al **Explorer di Test** e fare clic su **Esegui tutto**.

![Test di Funzioni di Azure con C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Esecuzione del debug dei test

Per eseguire il debug dei test, impostare un punto di interruzione in un test, passare a **Explorer del Test** e fare clic su **Esegui > Ultima esecuzione del Debug**.

## <a name="javascript-in-vs-code"></a>JavaScript in Visual Studio Code

Il seguente esempio illustra come creare un'app della funzione di JavaScript in Visual Studio Code ed eseguire i test con [Jest](https://jestjs.io). Questa procedura usa l'[estensione delle funzioni di Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per creare le Funzioni di Azure.

![Eseguire il test delle Funzioni di Azure con JavaScript in Visual Studio Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configurazione

Per configurare l'ambiente, inizializzare una nuova app Node.js in una cartella vuota eseguendo `npm init`.

```bash
npm init -y
```

In seguito, installare Jest eseguendo il comando seguente:

```bash
npm i jest
```

A questo punto aggiornare _package. JSON_ per sostituire il comando test esistente con il comando seguente:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Creare moduli del test

Con il progetto inizializzato, è possibile creare i moduli usati per eseguire i test automatizzati. Iniziare creando una nuova cartella denominata *testing* per contenere i moduli di supporto.

Nella cartella *testing* aggiungere un nuovo file, denominarla **defaultContext.js** e aggiungere il codice seguente:

```javascript
module.exports = {
    log: jest.fn()
};
```

Questo modulo simula la funzione *log* per rappresentare il contesto di esecuzione predefinito.

Successivamente, aggiungere un nuovo file, denominarlo **defaultTimer.js** e aggiungere il codice seguente:

```javascript
module.exports = {
    IsPastDue: false
};
```

Questo modulo implementa la proprietà `IsPastDue` per realizzare che è come un'istanza del timer fittizia. Le configurazioni del timer come le espressioni NCRONTAB non sono necessarie in questo caso perché il test harness chiama semplicemente la funzione direttamente per testare il risultato.

Successivamente, usare l'estensione delle Funzioni di Visual Studio Code per [creare una nuova funzione HTTP JavaScript](/azure/developer/javascript/tutorial-vscode-serverless-node-01) e denominarla *HttpTrigger*. Dopo aver creato la funzione, aggiungere un nuovo file nella stessa cartella denominata **index.test.js** e aggiungere il codice seguente:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

La funzione HTTP dal modello restituisce una stringa di "Hello" concatenati con il nome specificato nella stringa di query. Questo test consente di creare un'istanza fittizia di una richiesta e la passa alla funzione HTTP. Il test verifica che il metodo *log* venga chiamato una sola volta e che il testo restituito sia uguale a "Hello Bill".

Successivamente, usare l'estensione delle Funzioni di Visual Studio Code per creare una nuova funzione timer JavaScript e denominarla *TimerTrigger*. Dopo aver creato la funzione, aggiungere un nuovo file nella stessa cartella denominata **index.test.js** e aggiungere il codice seguente:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

La funzione timer dal modello registra un messaggio alla fine del corpo della funzione. Questo test assicura che la funzione *log* venga chiamata una sola volta.

### <a name="run-tests"></a>Esecuzione dei test

Per eseguire i test, premere **CTRL + ~** per aprire la finestra di comando ed eseguire `npm test`:

```bash
npm test
```

![Eseguire il test delle Funzioni di Azure con JavaScript in Visual Studio Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Esecuzione del debug dei test

Per eseguire il debug dei test, aggiungere la configurazione seguente per il file *launch.json* file:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Successivamente, impostare un punto di interruzione del test e premere **F5**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come scrivere i test automatizzati per le funzioni, continuare con queste risorse:

- [Eseguire manualmente una funzione non attivata da HTTP](./functions-manually-run-non-http.md)
- [Gestione degli errori di Funzioni di Azure](./functions-bindings-error-pages.md)
- [Debug locale del trigger della Griglia di eventi di Funzioni di Azure](./functions-debug-event-grid-trigger-local.md)
