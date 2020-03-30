---
title: PowerShell developer reference for Azure Functions
description: Comprendere come sviluppare funzioni tramite PowerShell.Understand how to develop functions by using PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276738"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guida per gli sviluppatori di Azure Functions PowerShellAzure Functions PowerShell developer guide

Questo articolo fornisce informazioni dettagliate su come si scrivono Funzioni di Azure usando PowerShell.This article provides details about how you write Azure Functions using PowerShell.

Una funzione di Azure di PowerShell (funzione) è rappresentata come uno script di PowerShell che viene eseguito quando viene attivato. Ogni script di `function.json` funzione ha un file correlato che definisce il modo in cui la funzione si comporta, ad esempio come viene attivata e i relativi parametri di input e output. Per altre informazioni, vedere [l'articolo Trigger e associazione](functions-triggers-bindings.md). 

Analogamente ad altri tipi di funzioni, le funzioni di script di PowerShell accettano parametri che corrispondono ai nomi di tutte le associazioni di input definite nel `function.json` file. Viene `TriggerMetadata` inoltre passato un parametro che contiene informazioni aggiuntive sul trigger che ha avviato la funzione.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È inoltre necessario aver completato la guida introduttiva di Funzioni per PowerShell per creare la prima funzione di PowerShell.You should also have completed the [Functions quickstart for PowerShell](functions-create-first-function-powershell.md) to create your first PowerShell function.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle richiesta per un progetto PowerShell è simile alla seguente. Questa impostazione predefinita non può essere modificata. Per altre informazioni, vedere la sezione [scriptFile](#configure-function-scriptfile) di seguito.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Alla radice del progetto, c'è [`host.json`](functions-host-json.md) un file condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione dispone di una cartella con il proprio file`function.json`di codice (con estensione ps1) e un file di configurazione di associazione ( ). Il nome della directory padre del file function.json è sempre il nome della funzione.

Alcune associazioni richiedono la `extensions.csproj` presenza di un file. Le estensioni di associazione, necessarie nella [versione 2.x e nelle versioni successive](functions-versions.md) del runtime di Funzioni, sono definite nel `extensions.csproj` file, con i file di libreria effettivi nella `bin` cartella. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](functions-bindings-register.md#extension-bundles). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

Nelle app per le funzioni di `profile.ps1` PowerShell, è possibile che si disponga di un oggetto che viene eseguito all'avvio di un'app per le funzioni (altrimenti noto come *[avvio a freddo.](#cold-start)* Per altre informazioni, vedere Profilo di [PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definizione di uno script di PowerShell come funzioneDefining a PowerShell script as a function

Per impostazione predefinita, il runtime di Funzioni cerca la funzione in `run.ps1`, dove `run.ps1` condivide la stessa directory padre del file `function.json` corrispondente.

Allo script vengono passati diversi argomenti sull'esecuzione. Per gestire questi parametri, aggiungere un `param` blocco all'inizio dello script come nell'esempio seguente:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametro TriggerMetadata

Il `TriggerMetadata` parametro viene utilizzato per fornire informazioni aggiuntive sul trigger. I metadati aggiuntivi variano dall'associazione `sys` all'associazione, ma contengono tutti una proprietà che contiene i dati seguenti:The additional metadata varies from binding to binding but they all contain a property that contains the following data:

```powershell
$TriggerMetadata.sys
```

| Proprietà   | Descrizione                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, in UTC, la funzione è stata attivata        | Datetime |
| MethodName | Il nome della funzione che è stata attivata     | string   |
| RandGuid   | un guid univoco per questa esecuzione della funzione | string   |

Ogni tipo di trigger ha un set di metadati diverso. Ad esempio, `$TriggerMetadata` `QueueTrigger` for `InsertionTime`contiene, tra le altre cose, `Id`, `DequeueCount`, . Per ulteriori informazioni sui metadati del trigger di coda, visitare la [documentazione ufficiale per i trigger di coda](functions-bindings-storage-queue-trigger.md#message-metadata). Controllare la documentazione sui [trigger](functions-triggers-bindings.md) con cui si sta lavorando per vedere cosa viene all'interno dei metadati del trigger.

## <a name="bindings"></a>Associazioni

In PowerShell le associazioni vengono [configurate](functions-triggers-bindings.md) e definite in function.json di una funzione. Le funzioni interagiscono con le associazioni in molti modi.

### <a name="reading-trigger-and-input-data"></a>Lettura dei dati di trigger e di input

Le associazioni di trigger e input vengono lette come parametri passati alla funzione. Le associazioni di `direction` input `in` hanno un set su in function.json.Input bindings have a set to in function.json. La `name` proprietà `function.json` definita in è il nome `param` del parametro, nel blocco. Poiché PowerShell usa parametri denominati per l'associazione, l'ordine dei parametri non è rilevante. Tuttavia, è consigliabile seguire l'ordine delle associazioni `function.json`definite nel file .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Scrittura dei dati di output

In Funzioni, un'associazione di output è `direction` impostata su `out` in function.json. È possibile scrivere in un'associazione di output utilizzando il `Push-OutputBinding` cmdlet, disponibile per il runtime di Funzioni. In tutti i `name` casi, la proprietà `function.json` dell'associazione come definita in corrisponde al `Name` parametro del `Push-OutputBinding` cmdlet.

Di seguito viene `Push-OutputBinding` illustrato come chiamare nello script di funzione:The following shows how to call in your function script:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

È anche possibile passare un valore per un'associazione specifica tramite la pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`si comporta in modo diverso in `-Name`base al valore specificato per :

* Quando il nome specificato non può essere risolto in un'associazione di output valida, viene generato un errore.

* Quando l'associazione di output accetta una `Push-OutputBinding` raccolta di valori, è possibile chiamare ripetutamente per eseguire il push di più valori.

* Quando l'associazione di output accetta solo `Push-OutputBinding` un valore singleton, la chiamata una seconda volta genera un errore.

#### <a name="push-outputbinding-syntax"></a>Sintassi `Push-OutputBinding`

Di seguito sono riportati i parametri validi per la chiamata: `Push-OutputBinding`

| Nome | Type | Posizione | Descrizione |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | string | 1 | Nome dell'associazione di output che si desidera impostare. |
| **`-Value`** | Oggetto | 2 | Valore dell'associazione di output che si desidera impostare, accettato dalla pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | denominata | (Facoltativo) Se specificato, forza il valore da impostare per un'associazione di output specificata. | 

Sono supportati anche i seguenti parametri comuni: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Per ulteriori informazioni, vedere [Informazioni su CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Esempio Push-OutputBinding: risposte HTTP

Un trigger HTTP restituisce una `response`risposta utilizzando un'associazione di output denominata . Nell'esempio seguente, l'associazione di output di `response` ha il valore di "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Poiché l'output è a HTTP, che accetta solo un `Push-OutputBinding` valore singleton, viene generato un errore quando viene chiamato una seconda volta.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Per gli output che accettano solo valori `-Clobber` singleton, è possibile utilizzare il parametro per eseguire l'override del valore precedente anziché tentare di aggiungere a una raccolta. Nell'esempio seguente si presuppone che sia già stato aggiunto un valore. Utilizzando `-Clobber`, la risposta dell'esempio seguente esegue l'override del valore esistente per restituire un valore di "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Esempio Push-OutputBinding: associazione di output della coda

`Push-OutputBinding`viene utilizzato per inviare dati alle associazioni di output, ad esempio [un'associazione](functions-bindings-storage-queue-output.md)di output di Archiviazione coda di Azure. Nell'esempio seguente, il messaggio scritto nella coda ha il valore "output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

L'associazione di output per una coda di archiviazione accetta più valori di output. In questo caso, chiamando l'esempio seguente dopo la prima scrittura nella coda un elenco con due elementi: "output #1" e "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Nell'esempio seguente, quando viene chiamato dopo i due precedenti, vengono aggiunti altri due valori alla raccolta di output:The following example, when called after the previous two, adds two more values to the output collection:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando viene scritto nella coda, il messaggio contiene questi quattro valori: "output #1", "output #2", "output #3" e "output #4".

#### <a name="get-outputbinding-cmdlet"></a>Cmdlet di `Get-OutputBinding`

È possibile `Get-OutputBinding` utilizzare il cmdlet per recuperare i valori attualmente impostati per le associazioni di output. Questo cmdlet recupera una tabella hash che contiene i nomi delle associazioni di output con i rispettivi valori. 

Di seguito è riportato un esempio di utilizzo per restituire i valori di associazione corrente:The following is an example of using `Get-OutputBinding` to return current binding values:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`contiene anche un `-Name`parametro denominato , che può essere utilizzato per filtrare l'associazione restituita, come nell'esempio seguente:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

In `Get-OutputBinding`.

## <a name="logging"></a>Registrazione

La registrazione nelle funzioni di PowerShell funziona come la normale registrazione di PowerShell.Logging in PowerShell functions works like regular PowerShell logging. È possibile utilizzare i cmdlet di registrazione per scrivere in ogni flusso di output. Ogni cmdlet esegue il mapping a un livello di log utilizzato da Funzioni.

| Livello di registrazione delle funzioni | Cmdlet per la registrazione |
| ------------- | -------------- |
| Errore | **`Write-Error`** |
| Avviso | **`Write-Warning`**  | 
| Informazioni | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informazioni | Scrive nella registrazione a livello _di informazioni._ |
| Debug | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oltre a questi cmdlet, tutto ciò che viene `Information` scritto nella pipeline viene reindirizzato al livello di log e visualizzato con la formattazione predefinita di PowerShell.

> [!IMPORTANT]
> L'utilizzo dei `Write-Verbose` cmdlet or `Write-Debug` non è sufficiente per visualizzare la registrazione a livello di verbose e debug. È inoltre necessario configurare la soglia del livello di log, che dichiara il livello di log effettivamente desiderato. Per altre informazioni, vedere [Configurare il livello di log dell'app per le funzioni.](#configure-the-function-app-log-level)

### <a name="configure-the-function-app-log-level"></a>Configurare il livello di log dell'app per le funzioni

Funzioni di Azure consente di definire il livello di soglia per semplificare il controllo del modo in cui Funzioni scrive nei log. Per impostare la soglia per tutte le `logging.logLevel.default` tracce scritte nella console, utilizzare la proprietà nel [ `host.json` riferimento][host.json]del file . Questa impostazione si applica a tutte le funzioni dell'app per le funzioni.

Nell'esempio seguente viene impostata la soglia per abilitare la registrazione dettagliata per `MyFunction`tutte le funzioni, ma viene impostata la soglia per abilitare la registrazione di debug per una funzione denominata :

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Per altre informazioni, vedere il [riferimento su host.json].

### <a name="viewing-the-logs"></a>Visualizzazione dei registri

Se l'app per le funzioni è in esecuzione in Azure, è possibile usare Application Insights per monitorarla. Vedere [Monitoraggio di Funzioni di Azure](functions-monitoring.md) per altre informazioni sulla visualizzazione e sull'esecuzione di query su log di funzioni.

Se si esegue l'app per le funzioni in locale per lo sviluppo, i log per impostazione predefinita il file system. Per visualizzare i log nella `AZURE_FUNCTIONS_ENVIRONMENT` console, `Development` impostare la variabile di ambiente su prima di avviare l'app per le funzioni.

## <a name="triggers-and-bindings-types"></a>Tipi di trigger e associazioniTriggers and bindings types

Sono disponibili numerosi trigger e associazioni da usare con l'app per le funzioni. L'elenco completo dei trigger e delle associazioni [è disponibile qui](functions-triggers-bindings.md#supported-bindings).

Tutti i trigger e le associazioni sono rappresentati nel codice come alcuni tipi di dati reali:All triggers and bindings are represented in code as a few real data types:

* Hashtable
* string
* byte[]
* INT
* double
* HttpRequestContext (Informazioni in base a HttpRequestContext
* HttpResponseContext

I primi cinque tipi in questo elenco sono tipi .NET standard. Gli ultimi due vengono utilizzati solo dal [trigger HttpTrigger](#http-triggers-and-bindings).

Ogni parametro di associazione nelle funzioni deve essere uno di questi tipi.

### <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.

#### <a name="request-object"></a>Oggetto della richiesta

L'oggetto richiesta passato allo script è `HttpRequestContext`di tipo , che dispone delle seguenti proprietà:

| Proprietà  | Descrizione                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Oggetto che contiene il corpo della richiesta. `Body`viene serializzato nel tipo migliore in base ai dati. Ad esempio, se i dati sono JSON, vengono passati come tabella hash. Se i dati sono una stringa, vengono passati come stringa. | object |
| **`Headers`** | Dizionario che contiene le intestazioni della richiesta.                | Dizionario<stringa,>stringa<sup>*</sup> |
| **`Method`** | Metodo HTTP della richiesta.                                | string                    |
| **`Params`**  | Oggetto che contiene i parametri di routing della richiesta. | Dizionario<stringa,>stringa<sup>*</sup> |
| **`Query`** | Oggetto che contiene i parametri di query della richiesta.                  | Dizionario<stringa,>stringa<sup>*</sup> |
| **`Url`** | URL della richiesta.                                        | string                    |

<sup>*</sup>Per `Dictionary<string,string>` tutte le chiavi non viene fatta distinzione tra maiuscole e minuscole.

#### <a name="response-object"></a>Oggetto della risposta

L'oggetto risposta che è necessario `HttpResponseContext`restituire è del tipo , che dispone delle seguenti proprietà:

| Proprietà      | Descrizione                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Oggetto che contiene il corpo della risposta.           | object                    |
| **`ContentType`** | Breve stringa per l'impostazione del tipo di contenuto per la risposta. | string                    |
| **`Headers`** | Oggetto che contiene le intestazioni della risposta.               | Dizionario o Hashtable   |
| **`StatusCode`**  | Codice di stato HTTP della risposta.                       | stringa o numero intero             |

#### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta

Quando si utilizzano i trigger HTTP, è possibile accedere alla richiesta HTTP come si farebbe con qualsiasi altra associazione di input. È nell'isolato. `param`

Utilizzare `HttpResponseContext` un oggetto per restituire una risposta, come illustrato di seguito:Use an object to return a response, as shown in the following:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Il risultato della chiamata di questa funzione sarebbe:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Cast di tipo per trigger e associazioniType-casting for triggers and bindings

Per alcune associazioni, ad esempio l'associazione BLOB, è possibile specificare il tipo del parametro.

Ad esempio, per fare in modo che i dati dall'archiviazione `param` BLOB vengano forniti come stringa, aggiungere il cast del tipo seguente al blocco seguente:For example, to have data from Blob storage supplied as a string, add the following type cast to my block:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profilo di PowerShell

In PowerShell è disponibile il concetto di profilo di PowerShell.In PowerShell, there's the concept of a PowerShell profile. Se non si ha familiarità con i profili di PowerShell, vedere [Informazioni sui profili](/powershell/module/microsoft.powershell.core/about/about_profiles).

In Funzioni di PowerShell lo script del profilo viene eseguito all'avvio dell'app per le funzioni. Le app per le funzioni vengono avviate alla prima distribuzione e dopo essere state inattive ([avvio a freddo](#cold-start)).

Quando si crea un'app per le funzioni usando strumenti, ad `profile.ps1` esempio Visual Studio Code e Azure Functions Core Tools, viene creato automaticamente un'impostazione predefinita. Il profilo predefinito viene gestito [nel repository GitHub di Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contiene:

* Autenticazione MSI automatica in Azure.Automatic MSI authentication to Azure.
* La possibilità di attivare `AzureRM` gli alias di PowerShell di Azure, se lo si desidera.

## <a name="powershell-version"></a>Versione PowerShell

Nella tabella seguente viene illustrata la versione di PowerShell usata da ogni versione principale del runtime di Funzioni:

| Versione di Funzioni | Versione PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloccato dal runtime) |
| 2.x               | PowerShell Core 6                              |

È possibile visualizzare la `$PSVersionTable` versione corrente stampando da qualsiasi funzione.

## <a name="dependency-management"></a>Gestione delle dipendenze

Funzioni consente di sfruttare [la raccolta di PowerShell](https://www.powershellgallery.com) per la gestione delle dipendenze. Con la gestione delle dipendenze abilitata, il file requirements.psd1 viene utilizzato per scaricare automaticamente i moduli necessari. Per attivare questo `managedDependency` comportamento, `true` impostare la proprietà su nella radice del [file host.json](functions-host-json.md), come nell'esempio seguente:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando si crea un nuovo progetto di funzioni di PowerShell, la gestione delle dipendenze è abilitata per impostazione predefinita, con il [ `Az` modulo](/powershell/azure/new-azureps-module-az) di Azure incluso. Il numero massimo di moduli attualmente supportati è 10. La sintassi _`MajorNumber`_ `.*` supportata è o la versione esatta del modulo, come illustrato nell'esempio requirements.psd1 seguente:The supported syntax is or exact module version as shown in the following requirements.psd1 example:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando si aggiorna il file requirements.psd1, i moduli aggiornati vengono installati dopo il riavvio.

> [!NOTE]
> Le dipendenze gestite richiedono l'accesso a www.powershellgallery.com per scaricare i moduli. Quando si esegue localmente, assicurarsi che il runtime possa accedere a questo URL aggiungendo le regole del firewall necessarie. 

Le impostazioni dell'applicazione seguenti possono essere utilizzate per modificare la modalità di download e installazione delle dipendenze gestite. L'aggiornamento dell'app viene avviato entro `MDMaxBackgroundUpgradePeriod` `MDNewSnapshotCheckPeriod`e il processo di aggiornamento viene completato all'interno di circa il file .

| Impostazione dell'app per funzioni              | Valore predefinito             | Descrizione                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 giorni)     | Ogni processo di lavoro di PowerShell avvia il controllo degli aggiornamenti `MDMaxBackgroundUpgradePeriod` dei moduli in PowerShell Gallery all'avvio del processo e ogni altro. Quando una nuova versione del modulo è disponibile in PowerShell Gallery, viene installata nel file system e resa disponibile per i professionisti di PowerShell.When a new module version is available in the PowerShell Gallery, it's installed to the file system and made available to PowerShell workers. La riduzione di questo valore consente all'app per le funzioni di ottenere prima le versioni dei moduli più recenti, ma aumenta anche l'utilizzo delle risorse dell'app (I/O di rete, CPU, archiviazione). L'aumento di questo valore riduce l'utilizzo delle risorse dell'app, ma potrebbe anche ritardare la consegna di nuove versioni del modulo all'app. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 ora)       | Dopo aver installato nuove versioni del modulo nel file system, è necessario riavviare ogni processo di lavoro di PowerShell.After new module versions are installed to the file system, every PowerShell worker process must be restarted. Il riavvio dei ruoli di lavoro di PowerShell influisce sulla disponibilità dell'app in quanto può interrompere l'esecuzione della funzione corrente. Fino a quando tutti i processi di lavoro di PowerShell non vengono riavviati, le chiamate di funzione possono utilizzare le versioni precedenti o nuove del modulo. Riavvio di tutti i `MDNewSnapshotCheckPeriod`ruoli di lavoro di PowerShell completare all'interno di . Aumentando questo valore si riduce la frequenza delle interruzioni, ma può anche aumentare il periodo di tempo in cui le chiamate di funzione utilizzano le versioni del modulo precedente o nuovo in modo non deterministico. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 giorno)     | Per evitare aggiornamenti eccessivi dei moduli nei riavvii frequenti del worker, il controllo degli `MDMinBackgroundUpgradePeriod`aggiornamenti dei moduli non viene eseguito quando un worker ha già avviato tale controllo nell'ultimo . |

Sfruttando i propri moduli personalizzati è un po 'diverso da come si farebbe normalmente.

Sul computer locale, il modulo viene installato in una `$env:PSModulePath`delle cartelle disponibili a livello globale nel file . Quando si esegue in Azure, non si ha accesso ai moduli installati nel computer. Ciò significa `$env:PSModulePath` che l'app per `$env:PSModulePath` una funzione di PowerShell è diversa da in un normale script di PowerShell.This means that the for a PowerShell function app differs from in a regular PowerShell script.

In Funzioni, `PSModulePath` contiene due percorsi:

* Una `Modules` cartella presente nella radice dell'app per le funzioni.
* Percorso di `Modules` una cartella controllata dal language worker di PowerShell.

### <a name="function-app-level-modules-folder"></a>Cartella a `Modules` livello di app di funzione

Per utilizzare moduli personalizzati, è possibile inserire moduli `Modules` da cui dipendono le funzioni in una cartella. Da questa cartella, i moduli sono automaticamente disponibili per il runtime delle funzioni. Qualsiasi funzione nell'app per le funzioni può usare questi moduli. 

> [!NOTE]
> I moduli specificati nel file requirements.psd1 vengono scaricati automaticamente e inclusi nel percorso, pertanto non è necessario includerli nella cartella modules. Questi vengono archiviati `$env:LOCALAPPDATA/AzureFunctions` localmente `/data/ManagedDependencies` nella cartella e nella cartella quando vengono eseguiti nel cloud.

Per sfruttare la funzionalità del `Modules` modulo personalizzato, crea una cartella nella radice dell'app per le funzioni. Copiare i moduli che si desidera utilizzare nelle funzioni in questa posizione.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Con `Modules` una cartella, l'app per le funzioni deve avere la struttura di cartelle seguente:With a folder, your function app should have the following folder structure:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Quando si avvia l'app per le `Modules` funzioni, `$env:PSModulePath` il language worker di PowerShell aggiunge questa cartella al modo in cui è possibile fare affidamento sul caricamento automatico del modulo come in un normale script di PowerShell.When you start your function app, the PowerShell language worker adds this folder to the so that you can rely on module autoloading as you would in a regular PowerShell script.

### <a name="language-worker-level-modules-folder"></a>Cartella del `Modules` livello di lavoro della lingua

Diversi moduli vengono comunemente usati dal language worker di PowerShell.Several modules are commonly used by the PowerShell language worker. Questi moduli sono definiti nell'ultima posizione di `PSModulePath`. 

L'elenco corrente dei moduli è il seguente:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulo utilizzato per l'utilizzo di archivi, ad esempio `.zip`, `.nupkg`e altri.
* **ThreadJob**: implementazione basata su thread delle API del processo di PowerShell.ThreadJob : A thread-based implementation of the PowerShell job APIs.

Per impostazione predefinita, Funzioni utilizza la versione più recente di questi moduli. Per usare una versione specifica del modulo, inserisci quella `Modules` versione specifica nella cartella dell'app per le funzioni.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere `$env:NAME_OF_ENV_VAR`a queste impostazioni utilizzando , come illustrato nell'esempio seguente:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Per l'esecuzione in locale, le impostazioni dell'app vengono lette dal file di progetto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Concorrenza

Per impostazione predefinita, il runtime di PowerShell funzioni può elaborare solo una chiamata di una funzione alla volta. Tuttavia, questo livello di concorrenza potrebbe non essere sufficiente nelle situazioni seguenti:However, this concurrency level might not be sufficient in the following situations:

* Quando si tenta di gestire un numero elevato di chiamate contemporaneamente.
* Quando si dispone di funzioni che richiamano altre funzioni all'interno della stessa app per le funzioni.

È possibile modificare questo comportamento impostando la seguente variabile di ambiente su un valore intero:You can change this behavior by setting the following environment variable to an integer value:

```
PSWorkerInProcConcurrencyUpperBound
```

Impostare questa variabile di ambiente nelle [impostazioni dell'app](functions-app-settings.md) di funzione.

### <a name="considerations-for-using-concurrency"></a>Considerazioni sull'utilizzo della concorrenzaConsiderations for using concurrency

PowerShell è un _linguaggio_ di script a thread singolo per impostazione predefinita. Tuttavia, la concorrenza può essere aggiunta usando più spazi di esecuzione di PowerShell nello stesso processo. La quantità di spazi di esecuzione creati corrisponderà all'impostazione dell'applicazione PSWorkerInProcConcurrencyUpperBound. La velocità effettiva sarà influenzata dalla quantità di CPU e memoria disponibile nel piano selezionato.

Azure PowerShell usa alcuni contesti e lo stato a livello di _processo_ per risparmiare sulla digitazione in eccesso. Tuttavia, se attivi la concorrenza nell'app per le funzioni e richiami azioni che cambiano stato, potresti finire con le race condition. Queste race condition sono difficili da sottoporre a debug perché una chiamata si basa su un determinato stato e l'altra chiamata ha modificato lo stato.

C'è un valore immenso nella concorrenza con Azure PowerShell, poiché alcune operazioni possono richiedere una notevole quantità di tempo. Tuttavia, è necessario procedere con cautela. Se si sospetta che si stia verificando una race condition, impostare l'app PSWorkerInProcConcurrencyUpperBound `1` su e usare invece l'isolamento a livello di processo di lavoro della [lingua](functions-app-settings.md#functions_worker_process_count) per la concorrenza.

## <a name="configure-function-scriptfile"></a>Configura funzione`scriptFile`

Per impostazione predefinita, una funzione `run.ps1`di PowerShell viene eseguita da `function.json`, un file che condivide la stessa directory padre della corrispondente.

La `scriptFile` proprietà `function.json` in può essere utilizzata per ottenere una struttura di cartelle simile all'esempio seguente:The property in the can be used to get a folder structure that looks like the following example:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In questo caso, `myFunction` for `scriptFile` `function.json` include una proprietà che fa riferimento al file con la funzione esportata da eseguire.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usare i moduli di PowerShell configurando un entryPointUse PowerShell modules by configuring an entryPoint

Questo articolo ha illustrato le `run.ps1` funzioni di PowerShell nel file di script predefinito generato dai modelli.
Tuttavia, è anche possibile includere le funzioni nei moduli di PowerShell.However, you can also include your functions in PowerShell modules. È possibile fare riferimento al codice di `scriptFile` `entryPoint` funzione specifico nel modulo usando i campi e nel file di configurazione function.json'.

In questo `entryPoint` caso, è il nome di una funzione o `scriptFile`di un cmdlet nel modulo di PowerShell a cui si fa riferimento in .

Si consideri la seguente struttura di cartelle:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Dove `PSFunction.psm1` contiene:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

In questo esempio, `myFunction` la `scriptFile` configurazione per `PSFunction.psm1`include una proprietà che fa riferimento a , che è un modulo di PowerShell in un'altra cartella.  La `entryPoint` proprietà `Invoke-PSTestFunc` fa riferimento alla funzione, che è il punto di ingresso nel modulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Con questa configurazione, l'esecuzione `Invoke-PSTestFunc` `run.ps1` viene eseguita esattamente come un sarebbe.

## <a name="considerations-for-powershell-functions"></a>Considerazioni sulle funzioni di PowerShellConsiderations for PowerShell functions

Quando si utilizzano le funzioni di PowerShell, tenere presenti le considerazioni nelle sezioni seguenti.

### <a name="cold-start"></a>Avvio a freddo

Quando si sviluppaNo funzioni di Azure nel modello di [hosting senza server,](functions-scale.md#consumption-plan)le avvii a freddo sono una realtà. *Per l'avvio* di una richiesta da parte dell'app per le funzioni si fa riferimento al periodo di tempo necessario per avviare l'esecuzione dell'esecuzione per l'elaborazione di una richiesta. L'avvio a freddo si verifica più frequentemente nel piano di consumo perché l'app per le funzioni viene arrestata durante i periodi di inattività.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduli di bundle invece di utilizzare`Install-Module`

Lo script viene eseguito a ogni chiamata. Evitare `Install-Module` di utilizzare nello script. Utilizzare `Save-Module` invece prima della pubblicazione in modo che la funzione non debba perdere tempo a scaricare il modulo. Se gli avvii a freddo influiscono sulle funzioni, è consigliabile distribuire l'app per le funzioni in un [piano di servizio app](functions-scale.md#app-service-plan) impostato su Sempre in *on* o su un [piano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di AzureAzure Functions triggers and bindings](functions-triggers-bindings.md)

[Informazioni di riferimento su host.json]: functions-host-json.md
