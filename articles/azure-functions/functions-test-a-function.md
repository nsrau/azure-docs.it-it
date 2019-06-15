---
title: Test di Funzioni di Azure
description: Creare test automatizzati per una funzione C# in Visual Studio e la funzione JavaScript in Visual Studio Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funzioni di azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, test
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 9a078579fff355d7ddb996316af2a2136fb62335
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496400"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie per il test del codice in Funzioni di Azure

Questo articolo illustra come creare i test automatizzati per le Funzioni di Azure. 

Si consiglia di testare tutto il codice, tuttavia si potrebbero ottenere risultati migliori eseguendo il wrapping di una logica della funzione e creando dei test all'esterno della funzione. Eseguendo subito l'astrazione per la logica si limitano le righe di una funzione di codice e si consente alla funzione di assumere la piena responsabilità per la chiamata ad altre classi o moduli. Questo articolo, tuttavia, illustra come creare test automatizzati in un HTTP e la funzione attivata tramite timer.

Il contenuto seguente è suddiviso in due sezioni diverse con lo scopo di ottenere diversi linguaggi e ambienti di destinazione. È possibile imparare a compilare i test in:

- [C# in Visual Studio con xUnit](#c-in-visual-studio)
- [JavaScript in Visual Studio Code with Jest](#javascript-in-vs-code)

Il repository di esempio è disponibile in [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# in Visual Studio
Il seguente esempio illustra come creare un'app della funzione C# in Visual Studio ed eseguire i test con [xUnit](https://xunit.github.io).

![Test di Funzioni di Azure con C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configurazione

Per configurare l'ambiente, creare una funzione e testare l'app. I passaggi seguenti consentono di creare le app e le funzioni necessarie per supportare i test:

1. [Creare una nuova app funzioni](./functions-create-first-azure-function.md) e denominarla *Funzioni*
2. [Creare una funzione HTTP dal modello](./functions-create-first-azure-function.md) e denominarla *HttpTrigger*.
3. [Creare una funzione del timer dal modello](./functions-create-scheduled-function.md) e denominarla *TimerTrigger*.
4. [Creare un'app di test xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) in Visual Studio facendo clic su **File > Nuovo > Progetto > Visual C# > .NET Core > Progetto di test xUnit** e denominarlo *Functions.Test*. 
5. Usare Nuget per aggiungere un riferimento dall'app test [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Fare riferimento all'app *Funzioni* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) dall'app *Functions.Test*.

### <a name="create-test-classes"></a>Crea classi di test

Ora che vengono create le applicazioni, è possibile creare le classi usate per eseguire i test automatizzati.

Ogni funzione accetta un'istanza di [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) per gestire la registrazione dei messaggi. Alcuni test non registrano messaggi oppure non hanno alcun problema per la modalità di implementazione della registrazione. Altri test devono valutare i messaggi registrati per determinare se si sta passando un test.

La classe `ListLogger` è destinata a implementare l'interfaccia `ILogger` e a mantenere l'elenco interno dei messaggi per la valutazione durante un test.

**Fare doppio clic su** nella *Functions.Test* dell'applicazione e selezionare **Aggiungi > classe**, denominarla **NullScope.cs** e immettere il codice seguente:

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

Successivamente, **fare doppio clic su** nel *Functions.Test* dell'applicazione e selezionare **Aggiungi > classe**, denominarlo **ListLogger.cs** e immettere il codice seguente:

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

- **BeginScope**: Gli ambiti aggiungono del contesto alla registrazione. In questo caso, il test appena punti all'istanza statica di `NullScope` classe per consentire la funzione del test.

- **IsEnabled**: È disponibile un valore predefinito di `false`.

- **Log**: Questo metodo usa la funzione fornita `formatter` per formattare il messaggio e quindi aggiunge il testo risultante nella raccolta `Logs`.

La raccolta `Logs` è un'istanza di `List<string>` e viene inizializzata nel costruttore.

In seguito, **fare doppio clic** sull'applicazione *Functions.Test* e selezionare **Aggiungi > Classe**, denominarla **LoggerTypes.cs** e immettere il codice seguente:

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

In seguito, **fare doppio clic** sull'applicazione *Functions.Test* e selezionare **Aggiungi > Classe**, denominarla **TestFactory.cs** e immettere il codice seguente:

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

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
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

- **Dati**: Questa proprietà restituisce una raccolta di dati di esempio [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable). Le coppie chiave-valore rappresentano valori che vengono passati in una stringa di query.

- **CreateDictionary**: Questo metodo accetta una coppia chiave/valore come argomenti e restituisce un nuovo `Dictionary` usato per creare `QueryCollection` per rappresentare i valori della stringa di query.

- **CreateHttpRequest**: Questo metodo crea una richiesta HTTP inizializzata con i parametri della stringa di query fornita.

- **CreateLogger**: Basato sul tipo di logger, questo metodo restituisce una classe di logger usata per il test. `ListLogger` tiene traccia dei messaggi registrati disponibili per la valutazione nei test.

In seguito, **fare doppio clic** sull'applicazione *Functions.Test* e selezionare **Aggiungi > Classe**, denominarla **FunctionsTests.cs** e immettere il codice seguente:

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
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
I membri implementati in questa classe sono:

- **Http_trigger_should_return_known_string**: Questo test crea una richiesta con i valori della stringa della query di `name=Bill` a una funzione HTTP e controlla che venga restituita la risposta prevista.

- **Http_trigger_should_return_string_from_member_data**: Questo test usa attributi xUnit per fornire dati di esempio alla funzione HTTP.

- **Timer_should_log_message**: Questo test crea un'istanza di `ListLogger` e la passa a una funzione timer. Una volta eseguita la funzione, il log viene controllato per verificare che sia presente il messaggio previsto.

Se si desidera accedere alle impostazioni dell'applicazione nei test, è possibile usare [GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

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

Nella cartella *testing* aggiungere un nuovo file, denominarla **defaultContext.js**e aggiungere il codice seguente:

```javascript
module.exports = {
    log: jest.fn()
};
```
Questo modulo simula la funzione *log* per rappresentare il contesto di esecuzione predefinito.

Successivamente, aggiungere un nuovo file, denominarlo **defaultTimer.js**e aggiungere il codice seguente:

```javascript
module.exports = {
    IsPastDue: false
};
```
Questo modulo implementa la proprietà `IsPastDue` per realizzare che è come un'istanza del timer fittizia.

Successivamente, usare l'estensione delle Funzioni di Visual Studio Code per [creare una nuova funzione HTTP JavaScript](https://code.visualstudio.com/tutorials/functions-extension/getting-started) e denominarla *HttpTrigger*. Dopo aver creato la funzione, aggiungere un nuovo file nella stessa cartella denominata **index.test.js** e aggiungere il codice seguente:

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
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
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
