---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: e90752e89be7e381b06f8a87f76f123f0e4a8e3a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422489"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

Le versioni principali del runtime di Funzioni di Azure sono correlate alla versione di .NET su cui si basa il runtime. La tabella seguente indica la versione corrente del runtime, il livello di rilascio e la versione .NET correlata. 

| Versione del runtime | Livello di rilascio<sup>1</sup> | Versione di .NET | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Core 3.1 | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.7.2<sup>3</sup> |

<sup>1</sup> Le versioni GA sono supportate per gli scenari di produzione.   
<sup>2</sup> La versione 1.x è in modalità di manutenzione. I miglioramenti vengono forniti solo nelle versioni successive.   
<sup>3</sup> Supporta solo lo sviluppo nel portale di Azure o in locale in computer Windows.3 Only supports development in the Azure portal or locally on Windows computers.

In questo articolo vengono descritte in dettaglio alcune delle differenze tra le varie versioni, come è possibile creare ogni versione e come modificare le versioni.

## <a name="languages"></a>Languages

A partire dalla versione 2.x, il runtime usa un modello di estensibilità del linguaggio e tutte le funzioni in un'app per le funzioni devono condividere lo stesso linguaggio. La lingua delle funzioni in un'app per le funzioni viene scelta durante la creazione dell'app e viene mantenuta nell'impostazione [RUNTIME\_FUNZIONI WORKER.\_](functions-app-settings.md#functions_worker_runtime) 

I linguaggi sperimentali di Funzioni di Azure 1.x non possono usare il nuovo modello, pertanto non sono supportati in 2.x.Azure Functions 1.x experimental languages can't use the new model, so they aren't supported in 2.x. Nella tabella seguente sono indicati i linguaggi di programmazione attualmente supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Eseguire su una versione specifica

Per impostazione predefinita, le app per le funzioni create nel portale di Azure e dall'interfaccia della riga di comando di Azure sono impostate sulla versione 3.x.By default, function apps created in the Azure portal and by the Azure CLI are set to version 3.x. È possibile modificare questa versione in base alle esigenze. È possibile modificare la versione di runtime in 1.x solo dopo aver creato l'app per le funzioni, ma prima di aggiungere qualsiasi funzione.  Lo spostamento tra 2.x e 3.x è consentito anche con le app con funzioni, ma è comunque consigliabile eseguire prima il test in una nuova app.

## <a name="migrating-from-1x-to-later-versions"></a>Migrazione da 1.x a versioni successive

Puoi scegliere di eseguire la migrazione di un'app esistente scritta per usare il runtime versione 1.x per usare invece una versione più recente. La maggior parte delle modifiche che è necessario apportare sono correlate alle modifiche nel runtime del linguaggio, ad esempio le modifiche dell'API di C, tra .NET Framework 4.7 e .NET Core. È anche necessario verificare che il codice e le librerie siano compatibili con il runtime del linguaggio scelto. Tenere infine presenti le modifiche apportate a trigger, binding e funzionalità evidenziate di seguito. Per ottenere risultati di migrazione ottimali, è necessario creare una nuova app per le funzioni in una nuova versione e eseguire il porting del codice della funzione della versione 1.x esistente nella nuova app.  

Mentre è possibile eseguire un aggiornamento "sul posto" aggiornando manualmente la configurazione dell'app, passare da 1.x a una versione superiore include alcune modifiche di rilievo. Ad esempio, nel linguaggio C, `TraceWriter` l'oggetto di debug viene modificato da a `ILogger`. Creando una nuova versione 3.x progetto, si inizia con funzioni aggiornate in base alla versione più recente 3.x modelli.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Modifiche nei trigger e nelle associazioni successive alla versione 1.xChanges in triggers and bindings after version 1.x

A partire dalla versione 2.x, è necessario installare le estensioni per trigger e associazioni specifici usati dalle funzioni nell'app. L'unica eccezione sono i trigger HTTP e Timer, che non richiedono un'estensione.  Per altre informazioni, vedere [Registrare le estensioni delle associazioni](./functions-bindings-register.md).

Ci sono anche alcune modifiche nel *file function.json* o negli attributi della funzione tra le versioni. Ad esempio, la proprietà `path` dell'hub eventi è ora `eventHubName`. Vedere la [tabella di binding esistente](#bindings) per collegamenti alla documentazione per ogni binding.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Modifiche nelle caratteristiche e funzionalità dopo la versione 1.x

Alcune funzionalità sono state rimosse, aggiornate o sostituite dopo la versione 1.x. In questa sezione vengono descritte in dettaglio le modifiche visualizzate nelle versioni successive dopo aver utilizzato la versione 1.x.

Nella versione 2.x sono state apportate le modifiche seguenti:

* Le chiavi per chiamare gli endpoint HTTP vengono sempre archiviate con crittografia in Archiviazione BLOB di Azure. Nella versione 1.x le chiavi venivano archiviate nell'archivio file di Azure per impostazione predefinita. Quando si aggiorna un'app dalla versione 1.x alla versione 2.x, i segreti esistenti nell'archiviazione file vengono reimpostati.

* La versione 2.x del runtime non include supporto incorporato per i provider di webhook. Questa modifica è stata apportata per migliorare le prestazioni. È comunque possibile usare i trigger HTTP come endpoint per i webhook.

* Il file di configurazione host (host.json) deve essere vuoto o avere la stringa `"version": "2.0"`.

* Per migliorare il monitoraggio, il dashboard WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) nel portale, che ha usato [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) l'impostazione, viene sostituito con Azure Application Insights, che usa l'impostazione. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

* Tutte le funzioni in un'app per le funzioni devono condividere lo stesso linguaggio. Quando si crea un'app per le funzioni, è necessario scegliere uno stack di runtime per l'app. Lo stack di runtime [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) è specificato dal valore nelle impostazioni dell'applicazione. Questo requisito è stato aggiunto per migliorare tempi di avvio e footprint. In caso di sviluppo in locale, è necessario includere anche questa impostazione nel [file local.settings.json](functions-run-local.md#local-settings-file).

* Il timeout predefinito per le funzioni in un piano di servizio app è ora di 30 minuti. È possibile riportare manualmente il timeout al valore illimitato mediante l'impostazione [functionTimeout](functions-host-json.md#functiontimeout) in host.json.

* Le limitazioni della concorrenza HTTP vengono implementate per impostazione predefinita per le funzioni del piano di consumo, con un valore predefinito di 100 richieste simultanee per istanza. È possibile modificare [`maxConcurrentRequests`](functions-host-json.md#http) questa impostazione nell'impostazione nel file host.json.

* A causa delle limitazioni di [.NET Core](https://github.com/Azure/azure-functions-host/issues/3414), è stato rimosso il supporto per le funzioni di script (fsx) F . Le funzioni F# compilate (.fs) sono ancora supportate.

* Il formato dell'URL del trigger Griglia di eventi è stato modificato in `https://{app}/runtime/webhooks/{triggerName}`.

## <a name="migrating-from-2x-to-3x"></a>Migrazione da 2.x a 3.x

Funzioni di Azure versione 3.x è altamente compatibile con le versioni 2.x.Azure Functions version 3.x is highly backwards compatible to version 2.x.  Molte app devono essere in grado di eseguire l'aggiornamento sicuro a 3.x senza alcuna modifica del codice.  Durante il passaggio a 3.x è consigliato, assicurarsi di eseguire test estesi prima di modificare la versione principale nelle app di produzione.

### <a name="breaking-changes-between-2x-and-3x"></a>Modifiche di rilievo tra 2.x e 3.x

Di seguito sono riportate le modifiche da tenere presenti prima di aggiornare un'app 2.x a 3.x.

#### <a name="javascript"></a>JavaScript

* Le associazioni di `context.done` output assegnate tramite valori `context.bindings`restituiti ora si comportano come l'impostazione in .

* Timer trigger object is camelCase instead of PascalCase

* Le funzioni attivate `dataType` da Hub eventi `binary` con `string`binario riceveranno una matrice di anziché .

* Non è più possibile accedere `context.bindingData.req`al payload della richiesta HTTP tramite .  È comunque possibile accedervi `context.req`come parametro di input, , e in `context.bindings`.

* Node.js 8 non è più supportato e non verrà eseguito nelle funzioni 3.x.

#### <a name="net"></a>.NET

* [Le operazioni sincrone del server sono disabilitate per impostazione predefinita.](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)

### <a name="changing-version-of-apps-in-azure"></a>Modifica della versione delle app in Azure

La versione del runtime di Funzioni usata dalle [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) app pubblicate in Azure è dettata dall'impostazione dell'applicazione. Sono supportati i seguenti valori di versione di runtime principali:

| valore | Destinazione runtime |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Non modificare arbitrariamente questa impostazione, perché potrebbero essere necessarie altre modifiche alle impostazioni dell'app e modifiche al codice della funzione.

### <a name="locally-developed-application-versions"></a>Versioni di applicazioni sviluppate localmente

È possibile apportare i seguenti aggiornamenti alle app per le funzioni per modificare localmente le versioni di destinazione.

#### <a name="visual-studio-runtime-versions"></a>Versioni del runtime di Visual Studio

In Visual Studio è possibile selezionare la versione del runtime quando si crea un progetto. Gli strumenti Funzioni di Azure per Visual Studio supportano le tre versioni di runtime principali. Durante il debugging e la pubblicazione viene usata la versione corretta in base alle impostazioni del progetto. Le impostazioni della versione vengono definite nel file `.csproj` nelle proprietà seguenti:

##### <a name="version-1x"></a>Versione 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versione 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versione 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Funzioni di Azure 3.x `Microsoft.NET.Sdk.Functions` e .NET `3.0.0`richiede che l'estensione sia almeno .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aggiornamento delle app 2.x alla 3.x in Visual StudioUpdating 2.x apps to 3.x in Visual Studio

È possibile aprire una funzione esistente destinata a 2.x `.csproj` e passare a 3.x modificando il file e aggiornando i valori precedenti.  Visual Studio gestisce automaticamente le versioni di runtime in base ai metadati del progetto.  Tuttavia, è possibile se non è mai stata creata un'app 3.x prima che Visual Studio non dispone ancora dei modelli e del runtime per 3.x nel computer.  Questo può presentarsi con un errore del tipo "nessun runtime di Funzioni disponibile che corrisponde alla versione specificata nel progetto".  Per recuperare i modelli e il runtime più recenti, provare l'esperienza per creare un nuovo progetto di funzione.  Quando si arriva alla schermata di selezione della versione e del modello, attendere che Visual Studio completi il recupero dei modelli più recenti.  Una volta che i modelli più recenti di .NET Core 3 sono disponibili e visualizzati si dovrebbe essere in grado di eseguire ed eseguire il debug di qualsiasi progetto configurato per la versione 3.x.

> [!IMPORTANT]
> Le funzioni della versione 3.x possono essere sviluppate in Visual Studio solo se si usa Visual Studio versione 16.4 o successiva.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code e Strumenti di base di Funzioni di Azure

[Azure Functions Core Tools](functions-run-local.md) viene usato per lo sviluppo da riga di comando e anche dall'[estensione di Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. Per sviluppare rispetto alla versione 3.x, installare la versione 3.x degli strumenti di base. Lo sviluppo della versione 2.x richiede la versione 2.x degli strumenti di base e così via. Per altre informazioni, vedere [Installare gli strumenti di base per Funzioni di Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Per lo sviluppo con Visual Studio Code può anche essere necessario aggiornare l'impostazione utente per `azureFunctions.projectRuntime` in base alla versione degli strumenti installata.  Questa impostazione aggiorna anche i modelli e i linguaggi usati durante la creazione di un'app per le funzioni.  Per creare `~3` app in `azureFunctions.projectRuntime` è necessario `~3`aggiornare l'impostazione utente a .

![Impostazione di runtime dell'estensione Funzioni di AzureAzure Functions extension runtime setting](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>App Maven e Java

È possibile eseguire la migrazione delle app Java dalla versione 2.x alla versione 3.x [installando la versione 3.x degli strumenti di base](functions-run-local.md#install-the-azure-functions-core-tools) necessari per l'esecuzione in locale.  Dopo aver verificato il corretto funzionamento dell'esecuzione locale dell'app `POM.xml` nella versione `FUNCTIONS_EXTENSION_VERSION` 3.x, aggiorna il file dell'app per modificare l'impostazione su `~3`, come nell'esempio seguente:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Associazioni

A partire dalla versione 2.x, il runtime usa un nuovo modello di [estendibilità dell'associazione](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) che offre questi vantaggi:Starting with version 2.x, the runtime uses a new binding extensibility model that offers these advantages:

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
