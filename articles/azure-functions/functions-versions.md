---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226544"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

The major versions of the Azure Functions runtime are related to the version of .NET on which the runtime is based. The following table indicates the current version of the runtime, the release level, and the related .NET version. 

| Versione del runtime | Release level<sup>1</sup> | Versione di .NET | 
| --------------- | ------------- | ------------ |
| 3.x  | anteprima | .NET Core 3.x | 
| 2.x | Disponibilità a livello generale | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup>GA releases are supported for production scenarios.   
<sup>2</sup>Version 1.x is in maintenance mode. Enhancements are provided only in later versions.   
<sup>3</sup>Only supports development in the Azure portal or locally on Windows computers.

>[!NOTE]  
> Version 3.x of the Functions runtime is in preview and isn't supported for production environments. For more information about trying out version 3.x, see [this announcement](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

This article details some of the differences between the various versions, how you can create each version, and how to change versions.

## <a name="languages"></a>Linguaggi

Starting with version 2.x, the runtime uses a language extensibility model, and all functions in a function app must share the same language. The language of functions in a function app is chosen when creating the app and is maintained in the [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime) setting. 

Azure Functions 1.x experimental languages can't use the new model, so they aren't supported in 2.x. Nella tabella seguente sono indicati i linguaggi di programmazione attualmente supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="creating-1x-apps"></a>Run on a specific version

By default, function apps created in the Azure portal and by the Azure CLI are set to version 2.x. When possible, you should use this runtime version. Se necessario, è comunque possibile eseguire un'app per le funzioni sulla versione 1.x del runtime. È possibile modificare la versione del runtime solo dopo aver creato l'app per le funzioni, ma prima di aggiungere qualsiasi funzione. Per informazioni su come aggiungere la versione del runtime 1.x, vedere [Visualizzare la versione corrente del runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

You can also upgrade to version 3.x of the runtime, which is in preview. Do this if you need to be able to run your functions on .NET Core 3.x. To learn how to upgrade to 3.x, see [View and update the current runtime version](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrating from 1.x to later versions

È possibile scegliere di eseguire la migrazione di un'app esistente scritta per usare il runtime versione 1.x anziché la versione 2.x. La maggior parte delle modifiche da apportare sono correlate alle modifiche nel runtime del linguaggio, ad esempio modifiche delle API C# tra .NET Framework 4.7 e .NET Core 2. È anche necessario verificare che il codice e le librerie siano compatibili con il runtime del linguaggio scelto. Tenere infine presenti le modifiche apportate a trigger, binding e funzionalità evidenziate di seguito. Per ottenere risultati di migrazione ottimali, è opportuno creare una nuova app per le funzioni per la versione 2.x e trasferire il codice della funzione versione 1.x esistente alla nuova app.  

### <a name="changes-in-triggers-and-bindings"></a>Modifiche apportate a trigger e binding

La versione 2.x richiede di installare le estensioni per specifici trigger e binding usati dalle funzioni nell'app. L'unica eccezione sono i trigger HTTP e Timer, che non richiedono un'estensione.  Per altre informazioni, vedere [Registrare le estensioni delle associazioni](./functions-bindings-register.md).

Sono state apportate anche alcune modifiche in `function.json` o negli attributi della funzione tra le versioni. Ad esempio, la proprietà `path` dell'hub eventi è ora `eventHubName`. Vedere la [tabella di binding esistente](#bindings) per collegamenti alla documentazione per ogni binding.

### <a name="changes-in-features-and-functionality"></a>Modifiche nelle funzioni e nella funzionalità

Anche alcune funzionalità sono state rimosse, aggiornate o sostituite nella nuova versione. Questa sezione illustra nel dettaglio le modifiche riscontrabili nella versione 2.x dopo aver usato la versione 1.x.

Nella versione 2.x sono state apportate le modifiche seguenti:

* Le chiavi per chiamare gli endpoint HTTP vengono sempre archiviate con crittografia in Archiviazione BLOB di Azure. Nella versione 1.x le chiavi vengono archiviate nell'archiviazione file di Azure per impostazione predefinita. Quando si aggiorna un'app dalla versione 1.x alla versione 2.x, i segreti esistenti nell'archiviazione file vengono reimpostati.

* La versione 2.x del runtime non include supporto incorporato per i provider di webhook. Questa modifica è stata apportata per migliorare le prestazioni. È comunque possibile usare i trigger HTTP come endpoint per i webhook.

* Il file di configurazione host (host.json) deve essere vuoto o avere la stringa `"version": "2.0"`.

* Per migliorare il monitoraggio, il dashboard di Processi Web nel portale, che usa l'impostazione [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard), è stato sostituito con Azure Application Insights, che usa l'impostazione [ `APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey). Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

* Tutte le funzioni in un'app per le funzioni devono condividere lo stesso linguaggio. Quando si crea un'app per le funzioni, è necessario scegliere uno stack di runtime per l'app. Lo stack di runtime viene specificato dal valore [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) nelle impostazioni dell'applicazione. Questo requisito è stato aggiunto per migliorare tempi di avvio e footprint. In caso di sviluppo in locale, è necessario includere anche questa impostazione nel [file local.settings.json](functions-run-local.md#local-settings-file).

* Il timeout predefinito per le funzioni in un piano di servizio app è ora di 30 minuti. È possibile riportare manualmente il timeout al valore illimitato mediante l'impostazione [functionTimeout](functions-host-json.md#functiontimeout) in host.json.

* Per impostazione predefinita vengono implementate limitazioni di concorrenza HTTP per le funzioni di piano a consumo, con un valore predefinito di 100 richieste simultanee per istanza. È possibile modificare questa condizione nell'impostazione [`maxConcurrentRequests`](functions-host-json.md#http) del file host.json.

* Per via delle [limitazioni di .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), il supporto per le funzioni script F# (fsx) è stato rimosso. Le funzioni F# compilate (.fs) sono ancora supportate.

* Il formato dell'URL del trigger Griglia di eventi è stato modificato in `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrazione di un'applicazione sviluppata in locale

Potrebbero essere presenti progetti di app per le funzioni sviluppati in locale con il runtime versione 1.x. Per passare alla versione 2.x, è consigliabile creare un progetto di app per le funzioni locale con la versione 2.x e trasferire il codice esistente nella nuova app. È possibile aggiornare manualmente il progetto e il codice esistente, con una sorta di aggiornamento sul posto. Potrebbe comunque essere necessario apportare una serie di altri miglioramenti tra la versione 1.x e la versione 2.x. In C#, ad esempio, l'oggetto di debug è stato modificato da `TraceWriter` in `ILogger`. Creando un nuovo progetto versione 2.x, si può iniziare con funzioni aggiornate basate sui modelli più recenti della versione 2.x.

#### <a name="visual-studio-runtime-versions"></a>Versioni del runtime di Visual Studio

In Visual Studio è possibile selezionare la versione del runtime quando si crea un progetto. Gli strumenti di Funzioni di Azure per Visual Studio supportano entrambe le versioni principali del runtime. Durante il debugging e la pubblicazione viene usata la versione corretta in base alle impostazioni del progetto. Le impostazioni della versione vengono definite nel file `.csproj` nelle proprietà seguenti:

##### <a name="version-1x"></a>Versione 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versione 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Durante il debug o la pubblicazione del progetto viene usata la versione corretta del runtime.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code e Strumenti di base di Funzioni di Azure

[Azure Functions Core Tools](functions-run-local.md) viene usato per lo sviluppo da riga di comando e anche dall'[estensione di Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. Per lo sviluppo con la versione 2.x, installare la versione 2.x di Core Tools. Lo sviluppo della versione 1.x richiede la versione 1.x di Core Tools. Per altre informazioni, vedere [Installare gli strumenti di base per Funzioni di Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Per lo sviluppo con Visual Studio Code può anche essere necessario aggiornare l'impostazione utente per `azureFunctions.projectRuntime` in base alla versione degli strumenti installata.  Questa impostazione aggiorna anche i modelli e i linguaggi usati durante la creazione di un'app per le funzioni.

### <a name="changing-version-of-apps-in-azure"></a>Modifica della versione delle app in Azure

La versione del runtime di Funzioni usata dalle app pubblicate in Azure è determinata dall'impostazione dell'applicazione [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version). Il valore `~2` si riferisce alla versione 2.x del runtime e il valore `~1` alla versione 1.x del runtime. Non modificare arbitrariamente questa impostazione, poiché sono probabilmente necessarie altre modifiche alle impostazioni dell'app e al codice nelle funzioni. Per individuare la procedura consigliata per la migrazione dell'app per le funzioni a una versione diversa del runtime, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

## <a name="bindings"></a>Associazioni

Starting with version 2.x, the runtime uses a new [binding extensibility model](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) that offers these advantages:

* Supporto per le estensioni di associazione di terze parti.

* Separazione di runtime e associazioni. Questa modifica consente di applicare il controllo delle versioni alle estensioni di binding e di rilasciarle in modo indipendente. È ad esempio possibile scegliere di eseguire l'aggiornamento a una versione di un'estensione basata su una nuova versione di un SDK sottostante.

* Ambiente di esecuzione più leggero, in cui solo le associazioni in uso sono note e vengono caricate dal runtime.

Ad eccezione dei trigger HTTP e Timer, tutti i binding devono essere esplicitamente aggiunti al progetto dell'app per le funzioni o registrati nel portale. Per altre informazioni, vedere [Registrare le estensioni delle associazioni](./functions-bindings-expressions-patterns.md).

Nella tabella seguente sono indicati i binding supportati in ogni versione del runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
* [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md)
* [Note sulla versione](https://github.com/Azure/azure-functions-host/releases)
