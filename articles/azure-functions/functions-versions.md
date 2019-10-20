---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: glenga
ms.openlocfilehash: 9ca7006bb842cbe235d2e982e611613e1fd74ed9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597413"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

Le versioni principali del runtime di funzioni di Azure sono correlate alla versione di .NET su cui si basa il Runtime. La tabella seguente indica la versione corrente del runtime, il livello di rilascio e la versione .NET correlata. 

| Versione del runtime | Livello di versione<sup>1</sup> | Versione di .NET | 
| --------------- | ------------- | ------------ |
| 3.x  | anteprima | .NET Core 3. x | 
| 2.x | Disponibilità a livello generale | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> Le versioni di GA sono supportate per gli scenari di produzione.   
<sup>2</sup> La versione 1. x è in modalità di manutenzione. I miglioramenti sono disponibili solo nelle versioni successive.   
<sup>3</sup> Supporta solo lo sviluppo nel portale di Azure o localmente nei computer Windows.

>[!NOTE]  
> La versione 3. x del runtime di funzioni è in anteprima e non è supportata per gli ambienti di produzione. Per ulteriori informazioni su come provare la versione 3. x, vedere [questo annuncio](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

Questo articolo descrive in dettaglio alcune delle differenze tra le diverse versioni, come è possibile creare ogni versione e come modificare le versioni.

## <a name="languages"></a>Linguaggi

A partire dalla versione 2. x, il runtime usa un modello di estendibilità del linguaggio e tutte le funzioni in un'app per le funzioni devono condividere la stessa lingua. Il linguaggio delle funzioni in un'app per le funzioni viene scelto quando si crea l'app e viene mantenuto nell'impostazione [funzioni \_WORKER \_RUNTIME](functions-app-settings.md#functions_worker_runtime) . 

I linguaggi sperimentali 1. x di funzioni di Azure non possono usare il nuovo modello, quindi non sono supportati in 2. x. Nella tabella seguente sono indicati i linguaggi di programmazione attualmente supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="creating-1x-apps"></a>Esegui in una versione specifica

Per impostazione predefinita, le app per le funzioni create nel portale di Azure e dall'interfaccia della riga di comando di Azure sono impostate sulla versione 2. x. Quando possibile, è consigliabile usare questa versione di Runtime. Se necessario, è comunque possibile eseguire un'app per le funzioni sulla versione 1.x del runtime. È possibile modificare la versione del runtime solo dopo aver creato l'app per le funzioni, ma prima di aggiungere qualsiasi funzione. Per informazioni su come aggiungere la versione del runtime 1.x, vedere [Visualizzare la versione corrente del runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

È anche possibile eseguire l'aggiornamento alla versione 3. x del runtime, disponibile in anteprima. Eseguire questa operazione se è necessario essere in grado di eseguire le funzioni in .NET Core 3. x. Per informazioni su come eseguire l'aggiornamento alla versione 3. x, vedere [visualizzare e aggiornare la versione corrente del runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrazione da 1. x a versioni successive

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

A partire dalla versione 2. x, il runtime usa un nuovo [modello di estendibilità dell'associazione](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) che offre i vantaggi seguenti:

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
