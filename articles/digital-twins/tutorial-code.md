---
title: "Esercitazione: Scrivere il codice di un'app client"
titleSuffix: Azure Digital Twins
description: Esercitazione su come scrivere il codice minimo per un'app client usando .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 8e7ad721eba103679f55886053e8ba9e888573c0
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057485"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Esercitazione: Scrivere codice con le API di Gemelli digitali di Azure

È comune per gli sviluppatori che usano Gemelli digitali di Azure scrivere un'applicazione client per interagire con la loro istanza del servizio. Questa esercitazione destinata agli sviluppatori offre un'introduzione alla programmazione per il servizio Gemelli digitali di Azure, usando la [libreria client di Gemelli digitali IoT di Azure per .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Viene descritta la procedura dettagliata per scrivere un'app client console in C# a partire da zero.

> [!div class="checklist"]
> * Configurare il progetto
> * Iniziare a scrivere il codice del progetto   
> * Esempio di codice completo
> * Pulire le risorse
> * Passaggi successivi

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione usa la riga di comando per la configurazione e le operazioni del progetto. Pertanto, è possibile usare qualsiasi editor di codice per eseguire gli esercizi.

Per iniziare, è necessario avere:
* Qualsiasi editor di codice
* **.NET Core 3.1** nel computer di sviluppo. È possibile scaricare questa versione di .NET Core SDK multipiattaforma dalla pagina di [download di .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Configurare il progetto

Quando si è pronti ad accedere all'istanza di Gemelli digitali di Azure, iniziare a configurare il progetto dell'app client. 

Aprire un prompt dei comandi o un'altra finestra della console nel computer e creare una directory di progetto vuota in cui archiviare il lavoro durante questa esercitazione. Assegnare alla directory un nome a scelta, ad esempio *DigitalTwinsCodeTutorial*.

Passare alla nuova directory.

Nella directory del progetto creare un progetto di app console .NET vuoto. Nella finestra di comando eseguire il comando seguente per creare un progetto C# minimo per la console:

```cmd/sh
dotnet new console
```

Nella directory verranno creati diversi file, tra cui uno denominato *Program.cs* in cui verrà scritta la maggior parte del codice.

Aggiungere quindi due dipendenze necessarie per l'uso di Gemelli digitali di Azure:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

La prima dipendenza è la [libreria client di Gemelli digitali IoT di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). La seconda dipendenza fornisce strumenti che consentono di eseguire l'autenticazione in Azure.

Tenere aperta la finestra di comando perché si continuerà a usarla nell'intera esercitazione.

## <a name="get-started-with-project-code"></a>Iniziare a scrivere il codice del progetto

In questa sezione si inizierà a scrivere il codice del nuovo progetto di app per l'uso con Gemelli digitali di Azure. Le azioni descritte includono:
* Autenticazione per il servizio
* Caricamento di un modello
* Rilevamento degli errori
* Creazione di gemelli digitali
* Creazione di relazioni
* Esecuzione di query sui gemelli digitali

Alla fine dell'esercitazione è anche disponibile una sezione che mostra il codice completo. È possibile usarlo come riferimento per verificare il programma man mano che si procede.

Per iniziare, aprire il file *Program.cs* in qualsiasi editor di codice. Verrà visualizzato un modello di codice minimo simile al seguente:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Per prima cosa, aggiungere alcune righe di `using` all'inizio del codice per eseguire il pull delle dipendenze necessarie.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

In seguito verrà aggiunto codice in questo file per inserire alcune funzionalità. 

### <a name="authenticate-against-the-service"></a>Autenticazione per il servizio

La prima operazione che dovrà essere eseguita dall'app è l'autenticazione per il servizio Gemelli digitali di Azure. Quindi, è possibile creare una classe client del servizio per accedere alle funzioni dell'SDK.

Per eseguire l'autenticazione, sono necessarie tre informazioni:
* L'*ID directory (tenant)* per la sottoscrizione
* L'*ID applicazione (client)* creato in precedenza quando è stata creata l'istanza di Gemelli digitali di Azure
* Il *nome host* dell'istanza di Gemelli digitali di Azure

>[!TIP]
> Se non si conosce l'*ID directory (tenant)* , è possibile ottenerlo eseguendo questo comando in [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli
> az account show --query tenantId
> ```

In *Program.cs* incollare il codice seguente sotto la riga "Hello, World!" nel metodo `Main`. Impostare il valore di `adtInstanceUrl` su *hostName* dell'istanza di Gemelli digitali di Azure, `clientId` sull'*ID applicazione* e `tenantId` sull'*ID directory*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Salvare il file. 

Si noti che in questo esempio vengono usate le credenziali interattive di un browser:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Questo tipo di credenziali genererà l'apertura di una finestra del browser, in cui si chiede di specificare le credenziali di Azure. 

>[!NOTE]
> Per informazioni su altri tipi di credenziali, vedere la documentazione relativa alle [librerie di autenticazione di Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md).

Nella finestra di comando eseguire il codice con questo comando: 

```cmd/sh
dotnet run
```

Le dipendenze verranno ripristinate alla prima esecuzione, quindi verrà eseguito il programma. 
* Se non si verificano errori, il programma stampa *Service client created - ready to go* per indicare che il client del servizio è stato creato ed è possibile continuare.
* Poiché questo progetto non include ancora un sistema di gestione degli errori, se qualcosa va storto il codice genererà un'eccezione.

### <a name="upload-a-model"></a>Caricare un modello

Gemelli digitali di Azure non include un vocabolario di dominio intrinseco. I tipi di elementi dell'ambiente che è possibile rappresentare in Gemelli digitali di Azure vengono definiti dall'utente usando i **modelli**. I [modelli](concepts-models.md) sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Forniscono modelli definiti dall'utente per i [gemelli digitali](concepts-twins-graph.md) da seguire e di cui creare istanze in seguito. Sono scritti in un linguaggio simile a JSON, **DTDL (Digital Twin Definition Language)** .

Il primo passaggio per la creazione di una soluzione di Gemelli digitali di Azure consiste nel definire almeno un modello in un file DTDL.

Nella directory in cui è stato creato il progetto creare un nuovo file con estensione *json* denominato *SampleModel.json*. Incollare il corpo del file seguente: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Se per questa esercitazione si usa Visual Studio, è possibile selezionare il nuovo file JSON e impostare la proprietà *Copia nella directory di output* di Controllo proprietà su *Copia se è più recente* o su *Copia sempre*. In questo modo Visual Studio troverà il file JSON con il percorso predefinito quando si eseguirà il programma con **F5** durante il resto dell'esercitazione.

> [!TIP] 
> È disponibile un [esempio di validator DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) indipendente dal linguaggio che è possibile usare per controllare i documenti del modello e verificare che il file DTDL sia valido. È basato sulla libreria di parser DTDL, descritta in maggior dettaglio in [*Procedura: Analizzare e convalidare modelli*](how-to-parse-models.md).

Successivamente, aggiungere altro codice al file *Program.cs* per caricare il modello appena creato nell'istanza di Gemelli digitali di Azure.

Aggiungere prima di tutto alcune istruzioni `using` all'inizio del file:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Prepararsi quindi a usare i metodi asincroni dell'SDK del servizio C# cambiando la firma del metodo `Main` per consentire l'esecuzione asincrona. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> L'uso di `async` non è strettamente necessario, in quanto l'SDK fornisce anche versioni sincrone di tutte le chiamate. Questa esercitazione illustra come usare `async`.

Quindi aggiungere il primo frammento di codice che interagisce con il servizio Gemelli digitali di Azure. Questo codice carica il file DTDL creato dal disco e quindi lo carica nell'istanza del servizio Gemelli digitali di Azure. 

Incollare il codice seguente sotto il codice di autorizzazione aggiunto in precedenza.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Nella finestra di comando eseguire il programma con questo comando: 

```cmd/sh
dotnet run
```
Nell'output verrà stampato "Caricare un modello", ma non è ancora disponibile alcun output per indicare se i modelli sono stati caricati correttamente o meno.

Per aggiungere un'istruzione di stampa che indica se i modelli sono stati caricati correttamente, aggiungere il codice seguente subito dopo la sezione precedente:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```
Prima di eseguire di nuovo il programma per testare questo nuovo codice, tenere presente che l'ultima volta in cui è stato eseguito il programma, il modello è già stato caricato. Gemelli digitali di Azure non consentiranno di caricare lo stesso modello due volte. Se pertanto si tenta di caricare nuovamente lo stesso modello, il programma dovrebbe generare un'eccezione.

Ora eseguire di nuovo il programma con questo comando nella finestra di comando:

```cmd/sh
dotnet run
```

Il programma dovrà generare un'eccezione. Quando si prova a caricare un modello che è già stato caricato, il servizio restituisce un errore di tipo "richiesta non valida" tramite l'API REST. Di conseguenza, l'SDK del client di Gemelli digitali di Azure genererà a sua volta un'eccezione, per ogni codice restituito dal servizio diverso da success. 

Nella sezione successiva vengono illustrate eccezioni come questa, con informazioni su come gestirle nel codice.

### <a name="catch-errors"></a>Rilevare gli errori

Per evitare l'arresto anomalo del programma, è possibile aggiungere il codice di eccezione intorno al codice di caricamento del modello. Eseguire il wrapping della chiamata client esistente `client.CreateModelsAsync` in un gestore try/catch, come indicato di seguito:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```
Se ora si esegue il programma con `dotnet run` nella finestra di comando, si noterà che si riceve un codice di errore. L'output è simile al seguente:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Model with same ID already exists dtmi:com:contoso:SampleModel;1. Use Model_List API to view models that already exist. See the Swagger example. (http://aka.ms/ModelListSwSmpl):}}

Headers:
api-supported-versions: REDACTED
Date: Thu, 10 Sep 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Da questo punto in poi, nell'esercitazione verrà eseguito il wrapping di tutte le chiamate ai metodi del servizio in gestori try/catch.

### <a name="create-digital-twins"></a>Creare i gemelli digitali

Dopo aver caricato un modello in Gemelli digitali di Azure, è possibile usare la relativa definizione per creare i **gemelli digitali**. I [gemelli digitali](concepts-twins-graph.md) sono istanze di un modello e rappresentano le entità all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, le stanze di un edificio o i fari di un'auto. Questa sezione crea alcuni gemelli digitali basati sul modello caricato in precedenza.

Aggiungere una nuova istruzione `using` all'inizio, perché sarà necessario il serializzatore JSON .NET incorporato in `System.Text.Json`:

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Aggiungere quindi il codice seguente alla fine del metodo `Main` per creare e inizializzare tre gemelli digitali basati su questo modello.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Nella finestra di comando eseguire il programma con `dotnet run`. Ripetere quindi questo comando per eseguire di nuovo il programma. 

Si noti che non viene generato alcun errore quando i gemelli vengono creati per la seconda volta, anche se esistono già dopo la prima esecuzione. A differenza della creazione del modello, la creazione di un gemello è, a livello REST, una chiamata *PUT* con semantica *upsert*. Questo significa che se un gemello esiste già, il tentativo di crearlo di nuovo non farà altro che sostituirlo. Non vengono generati errori.

### <a name="create-relationships"></a>Creare relazioni

Successivamente, è possibile creare **relazioni** tra i gemelli creati, per connetterli in un **grafo dei gemelli**. I [grafi dei gemelli](concepts-twins-graph.md) vengono usati per rappresentare l'intero ambiente.

Per poter creare relazioni, è necessario lo spazio dei nomi `Azure.DigitalTwins.Core.Serialization`, aggiunto al progetto in precedenza con l'istruzione `using`:

```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Aggiungere un nuovo metodo statico alla classe `Program`, sotto il metodo `Main`:
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Aggiungere quindi il codice seguente alla fine del metodo `Main` per chiamare il codice `CreateRelationship`:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Nella finestra di comando eseguire il programma con `dotnet run`.

Si noti che Gemelli digitali di Azure non consente di creare una relazione se ne esiste già una con lo stesso ID. Quindi, se si esegue il programma più volte, verranno visualizzate eccezioni alla creazione della relazione. Questo codice rileva le eccezioni e le ignora. 

### <a name="list-relationships"></a>Elencare le relazioni

Il codice successivo che verrà aggiunto consente di visualizzare l'elenco di relazioni create.

Aggiungere il nuovo metodo seguente alla classe `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Aggiungere quindi il codice seguente alla fine del metodo `Main` per chiamare il codice `ListRelationships`:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Nella finestra di comando eseguire il programma con `dotnet run`. Verrà visualizzato un elenco di tutte le relazioni create.

Di seguito è riportato un esempio di output:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Type name: System.Collections.Generic.Dictionary'2[System.String,System.String]: dtmi:contosocom:DigitalTwins:SampleModel;1
Create twin: sampleTwin-0
Create twin: sampleTwin-1
Create twin: sampleTwin-2
Created relationship successfully
Created relationship successfully
Twin sampleTwin-0 is connected to:
-contains->sampleTwin-1
-contains->sampleTwin-2

```

### <a name="query-digital-twins"></a>Eseguire query sui gemelli digitali

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente.

L'ultima sezione del codice da aggiungere in questa esercitazione esegue una query sull'istanza di Gemelli digitali di Azure. La query usata in questo esempio restituisce tutti i gemelli digitali dell'istanza.

Aggiungere il codice seguente alla fine del metodo `Main`:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Nella finestra di comando eseguire il programma con `dotnet run`. Nell'output verranno visualizzati tutti i dispositivi gemelli digitali di questa istanza.

## <a name="complete-code-example"></a>Esempio di codice completo

A questo punto dell'esercitazione si avrà un'app client completa, in grado di eseguire azioni di base con Gemelli digitali di Azure. Per riferimento, di seguito è riportato il codice completo del programma nel file *Program.cs*:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Pulire le risorse
 
L'istanza usata in questa esercitazione può essere riutilizzata nell'esercitazione successiva, [*Esercitazione: Esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md). Se si prevede di continuare con l'esercitazione successiva, è possibile mantenere l'istanza di Gemelli digitali di Azure configurata qui.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Infine, eliminare la cartella del progetto creata nel computer locale.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'applicazione client console .NET da zero. È stato scritto il codice per l'app client per eseguire le azioni di base con un'istanza di Gemelli digitali di Azure.

Continuare con l'esercitazione successiva per esplorare le operazioni che è possibile eseguire con questa app client di esempio: 

> [!div class="nextstepaction"]
> [*Esercitazione: Esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md)