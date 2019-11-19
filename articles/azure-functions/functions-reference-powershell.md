---
title: Guida di riferimento per gli sviluppatori di PowerShell per funzioni di Azure
description: Informazioni su come sviluppare funzioni usando PowerShell.
author: eamonoreilly
manager: gwallace
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: glenga
ms.openlocfilehash: ae3b8294c7bd91bcd6a2e0e533f5903f44e8aaea
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173672"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guida per gli sviluppatori di PowerShell per funzioni di Azure

Questo articolo fornisce informazioni dettagliate su come scrivere funzioni di Azure con PowerShell.

Una funzione di Azure di PowerShell (funzione) viene rappresentata come uno script di PowerShell che viene eseguito quando viene attivato. Ogni script di funzione dispone di un file di `function.json` correlato che definisce il comportamento della funzione, ad esempio il modo in cui viene attivata e i relativi parametri di input e output. Per altre informazioni, vedere l' [articolo trigger e binding](functions-triggers-bindings.md). 

Analogamente ad altri tipi di funzioni, le funzioni di script di PowerShell accettano parametri che corrispondono ai nomi di tutte le associazioni di input definite nel file di `function.json`. Viene inoltre passato un parametro `TriggerMetadata` che contiene informazioni aggiuntive sul trigger che ha avviato la funzione.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È necessario avere completato anche la [Guida introduttiva di funzioni per PowerShell](functions-create-first-function-powershell.md) per creare la prima funzione di PowerShell.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle necessaria per un progetto PowerShell è simile alla seguente. Questa impostazione predefinita non può essere modificata. Per altre informazioni, vedere la sezione [scriptFile](#configure-function-scriptfile) di seguito.

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

Alla radice del progetto è presente un file di [`host.json`](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione dispone di una cartella con il proprio file di codice (con estensione ps1) e il file di configurazione dell'associazione (`function.json`). Il nome della directory padre del file function. JSON è sempre il nome della funzione.

Determinate associazioni richiedono la presenza di un file di `extensions.csproj`. Le estensioni di binding, necessarie nella [versione 2. x](functions-versions.md) del runtime di funzioni, sono definite nel file di `extensions.csproj` con i file di libreria effettivi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](functions-bindings-register.md#extension-bundles). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

Nelle app per le funzioni di PowerShell, è possibile avere un `profile.ps1` che viene eseguito all'avvio dell'esecuzione di un'app per le funzioni. in caso contrario, è noto come *[avvio a freddo](#cold-start)* . Per altre informazioni, vedere [profilo di PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definizione di uno script di PowerShell come funzione

Per impostazione predefinita, il runtime di Funzioni cerca la funzione in `run.ps1`, dove `run.ps1` condivide la stessa directory padre del file `function.json` corrispondente.

All'esecuzione dello script vengono passati diversi argomenti. Per gestire questi parametri, aggiungere un blocco `param` all'inizio dello script, come nell'esempio seguente:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametro TriggerMetadata

Il parametro `TriggerMetadata` viene usato per fornire informazioni aggiuntive sul trigger. I metadati aggiuntivi variano dall'associazione al binding, ma contengono tutti una proprietà `sys` che contiene i dati seguenti:

```powershell
$TriggerMetadata.sys
```

| Proprietà   | DESCRIZIONE                                     | digitare     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, in UTC, la funzione è stata attivata        | DateTime |
| MethodName | Nome della funzione attivata     | stringa   |
| RandGuid   | GUID univoco dell'esecuzione della funzione | stringa   |

Ogni tipo di trigger ha un set di metadati diverso. Ad esempio, il `$TriggerMetadata` per `QueueTrigger` contiene le `InsertionTime`, `Id``DequeueCount`, tra le altre cose. Per ulteriori informazioni sui metadati del trigger della coda, vedere la [documentazione ufficiale relativa ai trigger della coda](functions-bindings-storage-queue.md#trigger---message-metadata). Controllare la documentazione sui [trigger](functions-triggers-bindings.md) che si sta utilizzando per vedere cosa avviene all'interno dei metadati del trigger.

## <a name="bindings"></a>Associazioni

In PowerShell le [associazioni](functions-triggers-bindings.md) vengono configurate e definite in function. JSON di una funzione. Le funzioni interagiscono con le associazioni in molti modi.

### <a name="reading-trigger-and-input-data"></a>Lettura dei dati di trigger e di input

I trigger e le associazioni di input vengono letti come parametri passati alla funzione. Le associazioni di input hanno un `direction` impostato su `in` in function. JSON. Il `name` proprietà definito in `function.json` è il nome del parametro, nel blocco `param`. Poiché PowerShell usa parametri denominati per l'associazione, l'ordine dei parametri non è rilevante. Tuttavia, è consigliabile seguire l'ordine dei binding definiti nel `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Scrittura dei dati di output

In funzioni, un'associazione di output ha un `direction` impostato su `out` in function. JSON. È possibile scrivere in un'associazione di output usando il cmdlet `Push-OutputBinding`, disponibile per il runtime di funzioni. In tutti i casi, la proprietà `name` dell'associazione come definito in `function.json` corrisponde al parametro `Name` del cmdlet `Push-OutputBinding`.

Di seguito viene illustrato come chiamare `Push-OutputBinding` nello script della funzione:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

È anche possibile passare un valore per un'associazione specifica tramite la pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` si comporta in modo diverso in base al valore specificato per `-Name`:

* Quando il nome specificato non può essere risolto in un'associazione di output valida, viene generato un errore.

* Quando l'associazione di output accetta una raccolta di valori, è possibile chiamare ripetutamente `Push-OutputBinding` per eseguire il push di più valori.

* Quando l'associazione di output accetta solo un valore singleton, la chiamata di `Push-OutputBinding` una seconda volta genera un errore.

#### <a name="push-outputbinding-syntax"></a>sintassi `Push-OutputBinding`

Di seguito sono riportati i parametri validi per chiamare `Push-OutputBinding`:

| Nome | digitare | Posizione | DESCRIZIONE |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Nome dell'associazione di output che si desidera impostare. |
| **`-Value`** | Object | 2 | Valore dell'associazione di output che si vuole impostare, che viene accettato dalla pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | denominata | Opzionale Quando specificato, impone l'impostazione del valore per un'associazione di output specificata. | 

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

Per ulteriori informazioni, vedere [informazioni su parametricomuni](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Esempio di push-output: risposte HTTP

Un trigger HTTP restituisce una risposta usando un'associazione di output denominata `response`. Nell'esempio seguente, l'associazione di output di `response` ha il valore "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Poiché l'output è HTTP, che accetta solo un valore singleton, viene generato un errore quando `Push-OutputBinding` viene chiamato una seconda volta.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Per gli output che accettano solo valori singleton, è possibile usare il parametro `-Clobber` per eseguire l'override del valore precedente anziché tentare di aggiungerlo a una raccolta. Nell'esempio seguente si presuppone che sia già stato aggiunto un valore. Utilizzando `-Clobber`, la risposta dell'esempio seguente sostituisce il valore esistente per restituire un valore "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Esempio di push-output: associazione di output della coda

`Push-OutputBinding` viene usato per inviare dati alle associazioni di output, ad esempio un' [associazione di output di archiviazione code di Azure](functions-bindings-storage-queue.md#output). Nell'esempio seguente, il messaggio scritto nella coda ha il valore "output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

L'associazione di output per una coda di archiviazione accetta più valori di output. In questo caso, chiamando l'esempio seguente dopo la prima scrittura nella coda, un elenco con due elementi: "output #1" e "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Nell'esempio seguente, quando viene chiamato dopo i due precedenti, aggiunge altri due valori alla raccolta di output:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando viene scritto nella coda, il messaggio contiene i quattro valori seguenti: "output #1", "output #2", "output #3" e "output #4".

#### <a name="get-outputbinding-cmdlet"></a>cmdlet `Get-OutputBinding`

È possibile utilizzare il cmdlet `Get-OutputBinding` per recuperare i valori attualmente impostati per le associazioni di output. Questo cmdlet recupera una tabella hash che contiene i nomi delle associazioni di output con i rispettivi valori. 

Di seguito è riportato un esempio di utilizzo di `Get-OutputBinding` per restituire i valori di binding correnti:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` contiene anche un parametro denominato `-Name`, che può essere usato per filtrare l'associazione restituita, come nell'esempio seguente:

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

La registrazione nelle funzioni di PowerShell funziona come la normale registrazione di PowerShell. È possibile usare i cmdlet di registrazione per scrivere in ogni flusso di output. Ogni cmdlet esegue il mapping a un livello di log usato dalle funzioni.

| Livello di registrazione delle funzioni | Cmdlet di registrazione |
| ------------- | -------------- |
| Tipi di errore | **`Write-Error`** |
| Avviso | **`Write-Warning`**  | 
| Informazioni | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informazioni | Scrive nella registrazione a livello di _informazioni_ . |
| Debug | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oltre a questi cmdlet, tutto ciò che viene scritto nella pipeline viene reindirizzato al livello `Information` log e visualizzato con la formattazione predefinita di PowerShell.

> [!IMPORTANT]
> L'uso dei cmdlet `Write-Verbose` o `Write-Debug` non è sufficiente per visualizzare la registrazione a livello dettagliato e di debug. È inoltre necessario configurare la soglia del livello di registrazione, che dichiara il livello di log effettivamente interessato. Per altre informazioni, vedere [configurare il livello di registrazione dell'app per le funzioni](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurare il livello di registrazione dell'app per le funzioni

Funzioni di Azure consente di definire il livello di soglia per facilitare il controllo del modo in cui le funzioni scrivono nei log. Per impostare la soglia per tutte le tracce scritte nella console, usare la `logging.logLevel.default` proprietà [`host.json`nel][informazioni di riferimento su host. json]. Questa impostazione si applica a tutte le funzioni dell'app per le funzioni.

Nell'esempio seguente viene impostata la soglia per abilitare la registrazione dettagliata per tutte le funzioni, ma viene impostata la soglia per abilitare la registrazione di debug per una funzione denominata `MyFunction`:

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

Per altre informazioni, vedere il [informazioni di riferimento su host. JSON].

### <a name="viewing-the-logs"></a>Visualizzazione dei log

Se il app per le funzioni è in esecuzione in Azure, è possibile usare Application Insights per monitorarlo. Vedere [Monitoraggio di Funzioni di Azure](functions-monitoring.md) per altre informazioni sulla visualizzazione e sull'esecuzione di query su log di funzioni.

Se si esegue la app per le funzioni localmente per lo sviluppo, i log vengono predefiniti per l'file system. Per visualizzare i log nella console, impostare la variabile di ambiente `AZURE_FUNCTIONS_ENVIRONMENT` su `Development` prima di avviare il app per le funzioni.

## <a name="triggers-and-bindings-types"></a>Tipi di trigger e associazioni

Sono disponibili diversi trigger e associazioni da usare con l'app per le funzioni. L'elenco completo dei trigger e delle associazioni [è disponibile qui](functions-triggers-bindings.md#supported-bindings).

Tutti i trigger e le associazioni sono rappresentati nel codice come pochi tipi di dati reali:

* Hashtable
* stringa
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

I primi cinque tipi in questo elenco sono tipi .NET standard. Gli ultimi due vengono usati solo dal [trigger HttpTrigger](#http-triggers-and-bindings).

Ogni parametro di associazione nelle funzioni deve essere uno di questi tipi.

### <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.

#### <a name="request-object"></a>Oggetto della richiesta

L'oggetto Request passato nello script è del tipo `HttpRequestContext`, che presenta le proprietà seguenti:

| Proprietà  | DESCRIZIONE                                                    | digitare                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Oggetto che contiene il corpo della richiesta. `Body` viene serializzato nel tipo migliore in base ai dati. Se, ad esempio, i dati sono JSON, vengono passati come Hashtable. Se i dati sono una stringa, viene passata come stringa. | oggetto |
| **`Headers`** | Dizionario che contiene le intestazioni della richiesta.                | Dictionary < String, String ><sup>*</sup> |
| **`Method`** | Metodo HTTP della richiesta.                                | stringa                    |
| **`Params`**  | Oggetto che contiene i parametri di routing della richiesta. | Dictionary < String, String ><sup>*</sup> |
| **`Query`** | Oggetto che contiene i parametri di query della richiesta.                  | Dictionary < String, String ><sup>*</sup> |
| **`Url`** | URL della richiesta.                                        | stringa                    |

<sup>*</sup> Tutte le chiavi di `Dictionary<string,string>` non fanno distinzione tra maiuscole e minuscole.

#### <a name="response-object"></a>Oggetto della risposta

L'oggetto Response da restituire è del tipo `HttpResponseContext`, che presenta le proprietà seguenti:

| Proprietà      | DESCRIZIONE                                                 | digitare                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Oggetto che contiene il corpo della risposta.           | oggetto                    |
| **`ContentType`** | Una breve opzione per l'impostazione del tipo di contenuto per la risposta. | stringa                    |
| **`Headers`** | Oggetto che contiene le intestazioni della risposta.               | Dictionary o Hashtable   |
| **`StatusCode`**  | Codice di stato HTTP della risposta.                       | stringa o int             |

#### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta

Quando si lavora con i trigger HTTP, è possibile accedere alla richiesta HTTP in modo analogo a qualsiasi altra associazione di input. Si trova nel blocco `param`.

Usare un oggetto `HttpResponseContext` per restituire una risposta, come illustrato nell'esempio seguente:

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

Il risultato della chiamata a questa funzione è:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Cast di tipo per trigger e associazioni

Per determinati binding, ad esempio l'associazione BLOB, è possibile specificare il tipo di parametro.

Ad esempio, per avere dati da un archivio BLOB fornito come stringa, aggiungere il cast del tipo seguente al blocco `param`:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profilo di PowerShell

In PowerShell è presente il concetto di profilo di PowerShell. Se non si ha familiarità con i profili di PowerShell, vedere [About Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

Nelle funzioni di PowerShell, lo script del profilo viene eseguito all'avvio dell'app per le funzioni. Le app per le funzioni vengono avviate alla prima distribuzione e dopo essere state inattive ([avvio a freddo](#cold-start)).

Quando si crea un'app per le funzioni usando strumenti, ad esempio Visual Studio Code e Azure Functions Core Tools, viene creata automaticamente una `profile.ps1` predefinita. Il profilo predefinito viene mantenuto [nel repository GitHub degli strumenti di base](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contiene:

* Autenticazione MSI automatica in Azure.
* La possibilità di attivare il Azure PowerShell `AzureRM` alias di PowerShell, se lo si desidera.

## <a name="powershell-version"></a>Versione di PowerShell

La tabella seguente illustra la versione di PowerShell usata da ogni versione principale del runtime di funzioni:

| Versione di Funzioni | Versione di PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (bloccato dal Runtime) |
| 2.x               | PowerShell Core 6                              |

È possibile visualizzare la versione corrente stampando `$PSVersionTable` da qualsiasi funzione.

## <a name="dependency-management"></a>Gestione delle dipendenze

Funzioni consente di sfruttare [PowerShell Gallery](https://www.powershellgallery.com) per la gestione delle dipendenze. Con la gestione delle dipendenze abilitata, il file requirements. psd1 viene usato per scaricare automaticamente i moduli richiesti. Per abilitare questo comportamento, impostare la proprietà `managedDependency` su `true` nella radice del [file host. JSON](functions-host-json.md), come nell'esempio seguente:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando si crea un nuovo progetto di funzioni di PowerShell, la gestione delle dipendenze è abilitata per impostazione predefinita con il [modulo`Az`](/powershell/azure/new-azureps-module-az) di Azure incluso. Il numero massimo di moduli attualmente supportati è 10. La sintassi supportata è _`MajorNumber`_ `.*` o la versione esatta del modulo, come illustrato nei requisiti seguenti. esempio psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando si aggiorna il file requirements. psd1, i moduli aggiornati vengono installati dopo il riavvio.

> [!NOTE]
> Le dipendenze gestite richiedono l'accesso a www.powershellgallery.com per scaricare i moduli. Quando si esegue localmente, verificare che il runtime possa accedere a questo URL aggiungendo le regole del firewall necessarie. 

Le seguenti impostazioni dell'applicazione possono essere usate per modificare il modo in cui le dipendenze gestite vengono scaricate e installate. L'aggiornamento dell'app viene avviato entro `MDMaxBackgroundUpgradePeriod`e il processo di aggiornamento viene completato entro approssimativamente l'`MDNewSnapshotCheckPeriod`.

| Impostazione app per le funzioni              | Default value             | DESCRIZIONE                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 giorni)     | Ogni processo di lavoro di PowerShell avvia il controllo degli aggiornamenti del modulo sul PowerShell Gallery all'avvio del processo e ogni `MDMaxBackgroundUpgradePeriod` successivamente. Quando una nuova versione del modulo è disponibile nel PowerShell Gallery, viene installata nel file system e resa disponibile per i ruoli di lavoro di PowerShell. La riduzione di questo valore consente all'app per le funzioni di ottenere prima le versioni più recenti del modulo, ma aumenta anche l'utilizzo delle risorse dell'app (I/O di rete, CPU, archiviazione). L'aumento di questo valore comporta una riduzione dell'utilizzo delle risorse dell'app, ma può anche ritardare la distribuzione di nuove versioni dei moduli nell'app. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 ora)       | Dopo aver installato le nuove versioni del modulo nella file system, è necessario riavviare tutti i processi di lavoro di PowerShell. Il riavvio dei ruoli di lavoro di PowerShell influiscono sulla disponibilità dell'app perché può interrompere l'esecuzione della funzione corrente. Fino a quando non vengono riavviati tutti i processi di lavoro di PowerShell, le chiamate di funzione possono usare le versioni dei moduli precedenti o nuove. Il riavvio di tutti i ruoli di lavoro di PowerShell viene completato in `MDNewSnapshotCheckPeriod`. L'aumento di questo valore comporta una riduzione della frequenza delle interruzioni, ma può anche aumentare il periodo di tempo durante il quale le chiamate di funzione utilizzano le versioni precedenti o nuove del modulo in modo non deterministico. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 giorno)     | Per evitare aggiornamenti eccessivi dei moduli nei riavvii di lavoro frequenti, il controllo degli aggiornamenti del modulo non viene eseguito quando un thread di lavoro ha già avviato il controllo nell'ultimo `MDMinBackgroundUpgradePeriod`. |

Usare i moduli personalizzati è leggermente diverso da quello che si farebbe normalmente.

Nel computer locale il modulo viene installato in una delle cartelle disponibili a livello globale nel `$env:PSModulePath`. Quando è in esecuzione in Azure, non è possibile accedere ai moduli installati nel computer. Ciò significa che il `$env:PSModulePath` per un'app per le funzioni di PowerShell differisce da `$env:PSModulePath` in uno script di PowerShell normale.

In funzioni `PSModulePath` contiene due percorsi:

* Una cartella `Modules` esistente alla radice dell'app per le funzioni.
* Percorso di una cartella `Modules` controllata dal ruolo di lavoro del linguaggio di PowerShell.

### <a name="function-app-level-modules-folder"></a>Cartella `Modules` a livello di app per le funzioni

Per usare i moduli personalizzati, è possibile inserire i moduli da cui dipendono le funzioni in una `Modules` cartella. Da questa cartella, i moduli sono automaticamente disponibili per il runtime di funzioni. Qualsiasi funzione nell'app per le funzioni può usare questi moduli. 

> [!NOTE]
> I moduli specificati nel file requirements. psd1 vengono scaricati e inclusi automaticamente nel percorso, quindi non è necessario includerli nella cartella modules. Questi vengono archiviati localmente nella cartella `$env:LOCALAPPDATA/AzureFunctions` e nella cartella `/data/ManagedDependencies` quando vengono eseguiti nel cloud.

Per sfruttare i vantaggi della funzionalità del modulo personalizzato, creare una cartella `Modules` nella radice dell'app per le funzioni. Copiare i moduli che si vuole usare nelle funzioni in questa posizione.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Con una cartella `Modules`, l'app per le funzioni deve avere la struttura di cartelle seguente:

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

Quando si avvia l'app per le funzioni, il ruolo di lavoro del linguaggio di PowerShell aggiunge questa `Modules` cartella al `$env:PSModulePath` in modo che sia possibile basarsi sul caricamento automatico dei moduli esattamente come in uno script di PowerShell normale.

### <a name="language-worker-level-modules-folder"></a>Cartella `Modules` a livello di lavoro lingua

Molti moduli sono comunemente usati dal ruolo di lavoro del linguaggio di PowerShell. Questi moduli sono definiti nell'ultima posizione del `PSModulePath`. 

L'elenco corrente dei moduli è il seguente:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulo usato per lavorare con gli archivi, ad esempio `.zip`, `.nupkg`e altri.
* **ThreadJob**: implementazione basata su thread delle API del processo di PowerShell.

Per impostazione predefinita, funzioni utilizza la versione più recente di questi moduli. Per usare una versione specifica del modulo, inserire la versione specifica nella cartella `Modules` dell'app per le funzioni.

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

Per impostazione predefinita, il runtime di PowerShell di funzioni può elaborare solo una chiamata di una funzione alla volta. Tuttavia, questo livello di concorrenza potrebbe non essere sufficiente nelle situazioni seguenti:

* Quando si sta tentando di gestire un numero elevato di chiamate allo stesso tempo.
* Quando sono presenti funzioni che richiamano altre funzioni all'interno della stessa app per le funzioni.

È possibile modificare questo comportamento impostando la variabile di ambiente seguente su un valore integer:

```
PSWorkerInProcConcurrencyUpperBound
```

Questa variabile di ambiente viene impostata nelle [impostazioni dell'app](functions-app-settings.md) del app per le funzioni.

### <a name="considerations-for-using-concurrency"></a>Considerazioni sull'utilizzo della concorrenza

PowerShell è un linguaggio di scripting a _thread singolo_ per impostazione predefinita. Tuttavia, è possibile aggiungere la concorrenza usando più Runspaces di PowerShell nello stesso processo. La quantità di Runspaces creata corrisponderà all'impostazione dell'applicazione PSWorkerInProcConcurrencyUpperBound. La velocità effettiva avrà un effetto sulla quantità di CPU e memoria disponibile nel piano selezionato.

Azure PowerShell usa alcuni contesti a _livello di processo_ e lo stato per facilitare il salvataggio dalla digitazione in eccesso. Tuttavia, se si attiva la concorrenza nell'app per le funzioni e si richiamano azioni che modificano lo stato, è possibile che si verifichino race condition. Queste race condition sono difficili da sottomettere a debug perché una chiamata si basa su un determinato stato e l'altra chiamata ha modificato lo stato.

La concorrenza con Azure PowerShell ha un notevole valore, perché alcune operazioni possono richiedere una notevole quantità di tempo. Tuttavia, è necessario procedere con cautela. Se si ritiene che si stia verificando una race condition, impostare l'impostazione dell'app PSWorkerInProcConcurrencyUpperBound su `1` e usare invece l' [isolamento a livello di processo di lavoro del linguaggio](functions-app-settings.md#functions_worker_process_count) per la concorrenza.

## <a name="configure-function-scriptfile"></a>Configurare `scriptFile` di funzione

Per impostazione predefinita, una funzione PowerShell viene eseguita da `run.ps1`, un file che condivide la stessa directory padre del `function.json`corrispondente.

La proprietà `scriptFile` nel `function.json` può essere usata per ottenere una struttura di cartelle simile all'esempio seguente:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In questo caso, il `function.json` per `myFunction` include una proprietà `scriptFile` che fa riferimento al file con la funzione esportata da eseguire.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usare i moduli di PowerShell configurando un entryPoint

Questo articolo ha illustrato le funzioni di PowerShell nel file di script predefinito `run.ps1` generato dai modelli.
Tuttavia, è anche possibile includere le funzioni nei moduli di PowerShell. È possibile fare riferimento al codice di funzione specifico nel modulo usando i campi `scriptFile` e `entryPoint` nel file di configurazione function. JSON.

In questo caso, `entryPoint` è il nome di una funzione o di un cmdlet nel modulo di PowerShell a cui viene fatto riferimento nel `scriptFile`.

Si consideri la struttura di cartelle seguente:

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

In questo esempio, la configurazione per `myFunction` include una proprietà `scriptFile` che fa riferimento `PSFunction.psm1`, che è un modulo di PowerShell in un'altra cartella.  La proprietà `entryPoint` fa riferimento alla funzione `Invoke-PSTestFunc`, che rappresenta il punto di ingresso nel modulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Con questa configurazione, il `Invoke-PSTestFunc` viene eseguito esattamente come `run.ps1`.

## <a name="considerations-for-powershell-functions"></a>Considerazioni sulle funzioni di PowerShell

Quando si lavora con le funzioni di PowerShell, tenere presenti le considerazioni riportate nelle sezioni seguenti.

### <a name="cold-start"></a>Avvio a freddo

Quando si sviluppano funzioni di Azure nel [modello di hosting senza server](functions-scale.md#consumption-plan), le partenze a freddo sono realtà. L' *avvio a freddo* si riferisce al periodo di tempo necessario per l'avvio dell'esecuzione dell'app per le funzioni per l'elaborazione di una richiesta. L'avvio a freddo si verifica più frequentemente nel piano a consumo perché l'app per le funzioni viene arrestata durante i periodi di inattività.

### <a name="bundle-modules-instead-of-using-install-module"></a>Aggregare i moduli invece di usare `Install-Module`

Lo script viene eseguito a ogni chiamata. Evitare di usare `Install-Module` nello script. Usare invece `Save-Module` prima della pubblicazione in modo che la funzione non debba sprecare tempo per scaricare il modulo. Se l'avvio a freddo influisca sulle funzioni, provare a distribuire l'app per le funzioni in un [piano di servizio app](functions-scale.md#app-service-plan) impostato su *Always on* o su un [piano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

[informazioni di riferimento su host. JSON]: functions-host-json.md
