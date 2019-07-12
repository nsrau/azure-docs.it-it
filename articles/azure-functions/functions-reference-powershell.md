---
title: Informazioni di riferimento di PowerShell per gli sviluppatori per le funzioni di Azure
description: Informazioni su come sviluppare funzioni tramite PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a75bdaf0e26193a5b2792b52923c085eff89b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706406"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guida per sviluppatori di Azure PowerShell di funzioni

Questo articolo fornisce informazioni dettagliate sulle modalità di scrittura delle funzioni di Azure usando PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Una funzione di PowerShell Azure (funzione) è rappresentata come uno script di PowerShell che viene eseguita quando attivati. Ogni script di funzione è correlata una `function.json` file che definisce come il comportamento della funzione, ad esempio la modalità viene attivata e i relativi parametri di input e outpui. Per altre informazioni, vedere la [trigger e binding articolo](functions-triggers-bindings.md). 

Come per altri tipi di funzioni, funzioni di script di PowerShell accettano parametri che corrispondono ai nomi di tutte le associazioni di input definiti nel `function.json` file. Oggetto `TriggerMetadata` che contiene informazioni aggiuntive sul trigger che ha avviato la funzione viene anche passato nel parametro.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È necessario avere completato anche il [avvio rapido di funzioni di PowerShell](functions-create-first-function-powershell.md) per creare la prima funzione di PowerShell.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle necessarie per un progetto di PowerShell simile al seguente. Questa impostazione predefinita non può essere modificata. Per altre informazioni, vedere la sezione [scriptFile](#configure-function-scriptfile) di seguito.

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

Nella radice del progetto, vi è condivisa [ `host.json` ](functions-host-json.md) file che può essere usato per configurare l'app per le funzioni. Ogni funzione dispone di una cartella con il proprio file di codice (con estensione ps1) e file di configurazione di associazione (`function.json`). Il nome della directory padre del file Function. JSON è sempre il nome della funzione.

Alcune associazioni richiedono la presenza di un `extensions.csproj` file. Associazione di estensioni, obbligatorio in [versione 2.x](functions-versions.md) del runtime di funzioni, definite nel `extensions.csproj` file, con i file di libreria effettivo nel `bin` cartella. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](functions-bindings-register.md#extension-bundles). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

Nelle app di funzione di PowerShell, potrebbe essere facoltativamente un `profile.ps1` che viene eseguita all'avvio di un'app per le funzioni per l'esecuzione (in caso contrario, conosciuto come un  *[avvio a freddo](#cold-start)* . Per altre informazioni, vedere [profilo PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definizione di uno script di PowerShell come una funzione

Per impostazione predefinita, il runtime di Funzioni cerca la funzione in `run.ps1`, dove `run.ps1` condivide la stessa directory padre del file `function.json` corrispondente.

Lo script viene passato un numero di argomenti sull'esecuzione. Per gestire questi parametri, aggiungere un `param` blocco all'inizio dello script come nell'esempio seguente:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametro TriggerMetadata

Il `TriggerMetadata` parametro viene utilizzato per fornire informazioni aggiuntive relative al trigger. I metadati aggiuntivi variano da un binding per associazione ma contengono tutti un `sys` proprietà che contiene i dati seguenti:

```powershell
$TriggerMetadata.sys
```

| Proprietà   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| utcNow     | Quando, in formato UTC, la funzione è stata attivata        | DateTime |
| MethodName | Il nome della funzione che è stata attivata     | string   |
| RandGuid   | un guid univoco per l'esecuzione della funzione | string   |

Ogni tipo di trigger ha un set diverso di metadati. Ad esempio, il `$TriggerMetadata` per `QueueTrigger` contiene il `InsertionTime`, `Id`, `DequeueCount`, tra le altre cose. Per altre informazioni sui metadati del trigger della coda, vedere la [documentazione ufficiale per i trigger della coda](functions-bindings-storage-queue.md#trigger---message-metadata). Consultare la documentazione sul [trigger](functions-triggers-bindings.md) si lavora con per vedere ciò che viene fornito all'interno di metadati del trigger.

## <a name="bindings"></a>Associazioni

In PowerShell [associazioni](functions-triggers-bindings.md) configurate e definito in Function. JSON di una funzione. Le funzioni interagiscono con le associazioni in molti modi.

### <a name="reading-trigger-and-input-data"></a>Lettura dei dati di trigger e di input

Trigger e associazioni di input vengono lette come parametri passati alla funzione. Le associazioni di input hanno una `direction` impostato su `in` in Function. JSON. Il `name` definita nella proprietà `function.json` è il nome del parametro, nel `param` blocco. Poiché PowerShell Usa parametri denominati per l'associazione, non è importante l'ordine dei parametri. Tuttavia, è consigliabile seguire l'ordine delle associazioni definite nel `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>La scrittura dei dati di output

Nelle funzioni, un'associazione di output ha un `direction` impostato su `out` nel Function. JSON. È possibile scrivere in un'associazione di output usando il `Push-OutputBinding` cmdlet, che è disponibile per il runtime di funzioni. In tutti i casi, il `name` proprietà dell'associazione come definito in `function.json` corrisponde alla `Name` parametro del `Push-OutputBinding` cmdlet.

Di seguito viene illustrato come chiamare `Push-OutputBinding` nello script di funzione:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

È anche possibile passare un valore per uno specifico binding tramite la pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` si comporta in modo diverso in base al valore specificato per `-Name`:

* Quando il nome specificato non può essere risolto in un'associazione di output valido, viene generato un errore.

* Quando l'associazione di output accetta una raccolta di valori, è possibile chiamare `Push-OutputBinding` ripetutamente per eseguire il push più valori.

* Se l'associazione di output accetta solo un valore singleton, la chiamata `Push-OutputBinding` una seconda volta genera un errore.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Sintassi

Di seguito sono i parametri validi per la chiamata `Push-OutputBinding`:

| Name | Type | Posizione | Descrizione |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Il nome dell'associazione di output a cui si desidera impostare. |
| **`-Value`** | Object | 2 | Il valore dell'associazione di output si desidera impostare, che è accettato dalla pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | denominata | (Facoltativo) Quando specificato, se ne determina il valore da impostare per un'associazione di output specificato. | 

Sono supportati anche i parametri comuni seguenti: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Per altre informazioni, vedere [CommonParameters sulle](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Esempio di push OutputBinding: Risposte HTTP

Un trigger HTTP restituisce una risposta utilizzando un'associazione di output denominata `response`. Nell'esempio seguente, l'associazione di output di `response` ha il valore di "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Poiché l'output è su HTTP, che accetta solo un valore singleton, viene generato un errore quando `Push-OutputBinding` viene chiamato una seconda volta.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Per gli output solo di accettare i valori singleton, è possibile usare il `-Clobber` parametro per sostituire il valore precedente invece di tentare di aggiungere a una raccolta. Nell'esempio seguente si presuppone che sia già stato aggiunto un valore. Usando `-Clobber`, la risposta dell'esempio seguente sostituisce il valore esistente per restituire un valore di "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Esempio di push OutputBinding: Associazione di output della coda

`Push-OutputBinding` viene usato per inviare i dati per le associazioni di output, ad esempio un [associazione di output archiviazione code di Azure](functions-bindings-storage-queue.md#output). Nell'esempio seguente, il messaggio scritto nella coda ha un valore di "output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

L'associazione di output per una coda di archiviazione accetta più valori di output. In questo caso, dopo il primo scrive un elenco con due elementi nella coda, la chiamata nell'esempio seguente: "output di #1" e "#2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

L'esempio seguente, quando viene chiamato dopo che i due precedenti, aggiunge altri due valori alla raccolta di output:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando scritto nella coda, il messaggio contiene questi quattro valori: "output #1", "output #2", "output #3" e "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet

È possibile usare il `Get-OutputBinding` cmdlet per recuperare i valori attualmente impostati per le associazioni di output. Questo cmdlet recupera una tabella hash che contiene i nomi delle associazioni di output con i rispettivi valori. 

Di seguito è riportato un esempio d'uso `Get-OutputBinding` per restituire i valori di associazione corrente:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` contiene anche un parametro denominato `-Name`, che possono essere usati per filtrare l'associazione restituito, come nell'esempio seguente:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

I caratteri jolly (*) sono supportati in `Get-OutputBinding`.

## <a name="logging"></a>Registrazione

La registrazione in funzioni di PowerShell funziona come la registrazione di PowerShell regolare. È possibile usare i cmdlet di registrazione per scrivere ogni flusso di output. Ogni cmdlet viene eseguito il mapping a un livello di log usato dalle funzioni.

| Funzioni di livello di registrazione | Cmdlet di registrazione |
| ------------- | -------------- |
| Errore | **`Write-Error`** |
| Avviso | **`Write-Warning`**  | 
| Informazioni | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informazioni | Scrive _informazioni_ la registrazione a livello. |
| Debug | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oltre a questi cmdlet, qualsiasi elemento scritto la pipeline viene reindirizzata al `Information` log a livello e visualizzato con la formattazione di PowerShell predefinito.

> [!IMPORTANT]
> Usando il `Write-Verbose` o `Write-Debug` cmdlet non sono sufficiente per vedere dettagliata e la registrazione a livello di debug. È inoltre necessario configurare la soglia del livello di log, che dichiara il livello di log si è effettivamente interessati. Per altre informazioni, vedere [configurare il livello di registrazione app di funzione](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurare il livello di registrazione di app (funzione)

Funzioni di Azure consente di definire il livello di soglia per renderlo più semplice controllare le funzioni modo scrive i log. Per impostare la soglia per tutte le tracce scritte nella console, usare il `logging.logLevel.default` proprietà di [ `host.json` file][riferimento su host.json]. Questa impostazione si applica a tutte le funzioni dell'app per le funzioni.

Nell'esempio seguente imposta la soglia per abilitare la registrazione dettagliata per tutte le funzioni, ma imposta la soglia per abilitare la registrazione di debug per una funzione denominata `MyFunction`:

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

### <a name="viewing-the-logs"></a>Visualizzazione dei log di

Se l'App per le funzioni è in esecuzione in Azure, è possibile usare Application Insights per monitorarlo. Vedere [Monitoraggio di Funzioni di Azure](functions-monitoring.md) per altre informazioni sulla visualizzazione e sull'esecuzione di query su log di funzioni.

Se si esegue l'App per le funzioni in locale per lo sviluppo, registra il file system predefinito. Per visualizzare i log della console, impostare il `AZURE_FUNCTIONS_ENVIRONMENT` variabile di ambiente `Development` prima di avviare l'App per le funzioni.

## <a name="triggers-and-bindings-types"></a>Trigger e tipi di associazioni

Sono disponibili numerosi trigger e associazioni è possibile utilizzare con l'app per le funzioni. L'elenco completo dei trigger e associazioni [sono disponibili qui](functions-triggers-bindings.md#supported-bindings).

Tutti i trigger e associazioni sono rappresentate nel codice come alcuni tipi di dati reali:

* Tabella hash
* string
* byte[]
* int
* Double
* HttpRequestContext
* HttpResponseContext

I primi cinque tipi in questo elenco sono tipi .NET standard. Gli ultimi due vengono usati solo per i [HttpTrigger trigger](#http-triggers-and-bindings).

Ogni parametro di associazione nelle funzioni deve essere uno di questi tipi.

### <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.

#### <a name="request-object"></a>Oggetto della richiesta

L'oggetto richiesta che viene passato allo script è di tipo `HttpRequestContext`, che presenta le proprietà seguenti:

| Proprietà  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Oggetto che contiene il corpo della richiesta. `Body` viene serializzato nel tipo migliore in base ai dati. Ad esempio, se i dati JSON, viene passato come una tabella hash. Se i dati sono una stringa, viene passato sotto forma di stringa. | object |
| **`Headers`** | Un dizionario che contiene le intestazioni della richiesta.                | Dizionario < stringa, stringa ><sup>*</sup> |
| **`Method`** | Metodo HTTP della richiesta.                                | string                    |
| **`Params`**  | Oggetto che contiene i parametri di routing della richiesta. | Dizionario < stringa, stringa ><sup>*</sup> |
| **`Query`** | Oggetto che contiene i parametri di query della richiesta.                  | Dizionario < stringa, stringa ><sup>*</sup> |
| **`Url`** | URL della richiesta.                                        | string                    |

<sup>*</sup> Tutti i `Dictionary<string,string>` le chiavi sono tra maiuscole e minuscole.

#### <a name="response-object"></a>Oggetto della risposta

L'oggetto risposta che deve inviare nuovamente è del tipo `HttpResponseContext`, che presenta le proprietà seguenti:

| Proprietà      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Oggetto che contiene il corpo della risposta.           | object                    |
| **`ContentType`** | Una mano breve per impostare il tipo di contenuto per la risposta. | string                    |
| **`Headers`** | Oggetto che contiene le intestazioni della risposta.               | Dizionario o una tabella hash   |
| **`StatusCode`**  | Codice di stato HTTP della risposta.                       | stringa o numero intero             |

#### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta

Quando si lavora con i trigger HTTP, è possibile accedere alla richiesta HTTP si farebbe con qualsiasi altra associazione di input. È nel `param` blocco.

Usare un `HttpResponseContext` oggetto per restituire una risposta, come illustrato nell'esempio seguente:

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

Il risultato ottenuto richiamando la funzione sarà:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Cast dei tipi di trigger e associazioni

Per determinate associazioni, ad esempio l'associazione di blob, in grado di specificare il tipo del parametro.

Ad esempio, per ottenere dati dall'archiviazione Blob fornito sotto forma di stringa, aggiungere il seguente cast di tipo a mio `param` blocco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profilo di PowerShell

In PowerShell, è presente il concetto di un profilo di PowerShell. Se non si ha familiarità con i profili di PowerShell, vedere [sui profili](/powershell/module/microsoft.powershell.core/about/about_profiles).

Quando si avvia l'app per le funzioni in funzioni di PowerShell, viene eseguito lo script del profilo. App per le funzioni iniziare quando distribuita prima e dopo inattivo da ([avvio a freddo](#cold-start)).

Quando si crea un'app per le funzioni usando strumenti come Visual Studio Code e Azure Functions Core Tools, predefinito `profile.ps1` viene creato automaticamente. Il profilo predefinito viene mantenuto [nel repository Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contiene:

* Autenticazione del servizio gestito automatica in Azure.
* La possibilità di attivare Azure PowerShell `AzureRM` alias PowerShell se si desidera.

## <a name="powershell-version"></a>Versione di PowerShell

La tabella seguente illustra la versione di PowerShell usata da ogni versione principale del runtime di funzioni:

| Versione di Funzioni | Versione di PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloccata dal runtime) |
| 2.x               | PowerShell Core 6                              |

È possibile visualizzare la versione corrente mediante la stampa `$PSVersionTable` da qualsiasi funzione.

## <a name="dependency-management"></a>Gestione delle dipendenze

Le funzioni di PowerShell supportano la gestione di moduli di Azure dal servizio. Modifica l'host. JSON e impostando la proprietà managedDependency attivata su true, il file requirements.psd1 verrà elaborato. I moduli di Azure più recenti verranno automaticamente scaricati e resi disponibili per la funzione.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Sfruttando personalizzate moduli o dal [PowerShell Gallery](https://powershellgallery.com) è leggermente diverso rispetto a come si farebbe normalmente.

Quando si installa il modulo nel computer locale, entrerà in una delle cartelle disponibili a livello globale nel `$env:PSModulePath`. Poiché la funzione viene eseguita in Azure, si avrà accesso ai moduli installati nel computer. Ciò richiede che il `$env:PSModulePath` per una funzione di PowerShell app differisce da `$env:PSModulePath` in uno script di PowerShell regolare.

Nelle funzioni `PSModulePath` contiene due percorsi:

* Oggetto `Modules` cartella esistente alla radice dell'App per le funzioni.
* Un percorso a un `Modules` cartella in cui si trova all'interno di processo di lavoro PowerShell.

### <a name="function-app-level-modules-folder"></a>App a livello di funzione `Modules` cartella

Per usare i moduli personalizzati o i moduli di PowerShell da PowerShell Gallery, è possibile inserire i moduli da cui dipendono le funzioni in un `Modules` cartella. Da questa cartella, i moduli sono automaticamente disponibili per il runtime di funzioni. Qualsiasi funzione nell'app per le funzioni possa usare questi moduli.

Per sfruttare i vantaggi di questa funzionalità, creare un `Modules` cartella nella radice dell'app per le funzioni. Salvare i moduli da usare nelle funzioni in questa posizione.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Usare `Save-Module` per salvare tutti i moduli usano le funzioni oppure copiare moduli personalizzati per il `Modules` cartella. Con una cartella di moduli, app per le funzioni debba avere la struttura di cartelle seguente:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Quando si avvia l'app per le funzioni, il processo di lavoro PowerShell aggiunge ciò `Modules` cartella in cui il `$env:PSModulePath` in modo che è possibile basarsi su modulo di caricamento automatico esattamente come farebbe in un normale script di PowerShell.

### <a name="language-worker-level-modules-folder"></a>Il livello di ruolo di lavoro del linguaggio `Modules` cartella

Diversi moduli vengono comunemente usati per il ruolo di lavoro del linguaggio di PowerShell. Questi moduli sono definiti in ultima posizione della `PSModulePath`. 

L'elenco corrente dei moduli è come segue:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulo utilizzato per l'utilizzo di archivi, ad esempio `.zip`, `.nupkg`e così via.
* **ThreadJob**: Un'implementazione basata su thread del processo di PowerShell, API.

La versione più recente di questi moduli è utilizzata da funzioni. Per usare una versione specifica di questi moduli, è possibile inserire la versione specifica `Modules` cartella dell'app per le funzioni.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando `$env:NAME_OF_ENV_VAR`, come illustrato nell'esempio seguente:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Per l'esecuzione in locale, le impostazioni dell'app vengono lette dal file di progetto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Concorrenza

Per impostazione predefinita, il runtime di funzioni PowerShell può elaborare solo una singola chiamata di una funzione in una fase. Tuttavia, questo livello di concorrenza potrebbe non essere sufficiente nelle situazioni seguenti:

* Quando si sta tentando di gestire un numero elevato di chiamate nello stesso momento.
* Quando si dispongono di funzioni che chiamano altre funzioni all'interno della stessa app di funzione.

È possibile modificare questo comportamento impostando la variabile di ambiente seguente in un valore integer:

```
PSWorkerInProcConcurrencyUpperBound
```

Si imposta questa variabile di ambiente [le impostazioni dell'app](functions-app-settings.md) dell'App per le funzioni.

### <a name="considerations-for-using-concurrency"></a>Considerazioni sull'utilizzo di concorrenza

PowerShell è un _thread singolo_ linguaggio di scripting per impostazione predefinita. Tuttavia, è possibile aggiungere la concorrenza con più spazi di esecuzione PowerShell nello stesso processo. Questa funzionalità è funzionamento del runtime di funzioni di Azure PowerShell.

Esistono alcuni inconvenienti di questo approccio.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>La concorrenza è solo fintanto che la macchina di che cui è in esecuzione

Se l'app per le funzioni viene eseguita in un [piano di servizio App](functions-scale.md#app-service-plan) che supporta solo un singolo core, quindi la concorrenza non aiuta a gran parte. Ciò avviene perché non esistono Nessun core aggiuntivi per bilanciare il carico. In questo caso, le prestazioni possono variare quando il singolo core ha al cambio di contesto tra gli spazi di esecuzione.

Il [piano a consumo](functions-scale.md#consumption-plan) viene eseguita tramite un solo core, pertanto è possibile sfruttare la concorrenza. Se vuoi sfruttare tutti i vantaggi della concorrenza, distribuire invece le funzioni in un'app per le funzioni in esecuzione in un piano di servizio App dedicato con core sufficienti.

#### <a name="azure-powershell-state"></a>Stato di Azure PowerShell

Azure PowerShell Usa alcuni _a livello di processo_ contesti e stato per consentire di risparmiare da digitare in eccesso. Tuttavia, se si attiva la concorrenza nell'app per le funzioni e richiamare le azioni che modificano lo stato, può arrivare race condition. Race condition sono difficili da eseguire il debug perché una singola chiamata si basa su un determinato stato e le altre chiamate ha modificato lo stato.

È disponibile un'enorme valore in concorrenza con Azure PowerShell, poiché alcune operazioni possono richiedere una quantità considerevole di tempo. Tuttavia, è necessario procedere con cautela. Se si ritiene che si è verificato una race condition, impostare la concorrenza a `1` e riprovare la richiesta.

## <a name="configure-function-scriptfile"></a>Configurare (funzione) `scriptFile`

Per impostazione predefinita, una funzione di PowerShell viene eseguita dal `run.ps1`, un file che condivide la stessa directory padre corrispondente `function.json`.

Il `scriptFile` proprietà nel `function.json` utilizzabile per ottenere una struttura di cartelle simile al seguente:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In questo caso, il `function.json` per `myFunction` include un `scriptFile` proprietà fa riferimento al file con la funzione esportata da eseguire.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usare i moduli di PowerShell tramite la configurazione di un punto di ingresso

Questo articolo ha illustrato le funzioni di PowerShell nel predefinito `run.ps1` generata dai modelli di file di script.
Tuttavia, è possibile includere anche le funzioni in moduli di PowerShell. È possibile fare riferimento il codice di funzione specifica del modulo usando il `scriptFile` e `entryPoint` campi il Function. JSON ' file di configurazione.

In questo caso `entryPoint` è il nome di una funzione o un cmdlet nel modulo di PowerShell a cui fa riferimento `scriptFile`.

Si consideri la struttura di cartelle seguente:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

In cui `PSFunction.psm1` contiene:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

In questo esempio, la configurazione per `myFunction` include un `scriptFile` proprietà che fa riferimento `PSFunction.psm1`, ovvero un modulo di PowerShell in un'altra cartella.  Il `entryPoint` riferimenti a proprietà di `Invoke-PSTestFunc` funzione, ovvero il punto di ingresso nel modulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Con questa configurazione, il `Invoke-PSTestFunc` viene eseguita esattamente come un `run.ps1` sarebbe.

## <a name="considerations-for-powershell-functions"></a>Considerazioni per le funzioni di PowerShell

Quando si lavora con le funzioni di PowerShell, tenere presente le considerazioni nelle sezioni seguenti.

### <a name="cold-start"></a>Avvio a freddo

Durante lo sviluppo di funzioni di Azure i [modello di hosting senza server](functions-scale.md#consumption-plan), ad accesso sporadico inizia è una realtà. *Avvio a freddo* si riferisce al periodo di tempo è necessario per l'app per le funzioni avviare l'esecuzione per elaborare una richiesta. Avvio a freddo si verifica più spesso del consumo piano perché l'app per le funzioni viene arrestato durante i periodi di inattività.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduli di bundle invece di usare `Install-Module`

Lo script viene eseguito a ogni chiamata. Evitare di usare `Install-Module` nello script. Usare invece `Save-Module` prima di pubblicare in modo che la funzione non deve sprecare tempo scaricare il modulo. Se avvii a freddo con effetti sulle funzioni, è consigliabile distribuire l'app per le funzioni a un [piano di servizio App](functions-scale.md#app-service-plan) impostata su *AlwaysOn* o a una [piano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

[riferimento su host.json]: functions-host-json.md
