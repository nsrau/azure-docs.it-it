---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978625"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

 Esistono due versioni principali del runtime di Funzioni di Azure: 1.x e 2.x. La versione corrente in cui vengono apportati le nuove funzionalità e i miglioramenti è la 2.x, anche se entrambe sono supportate per gli scenari di produzione.  Di seguito sono illustrate in dettaglio alcune delle differenze tra le due versioni e viene descritto come creare ogni versione e come eseguire l'aggiornamento dalla versione 1.x alla versione 2.x.

> [!NOTE] 
> Questo articolo si riferisce al servizio cloud di Funzioni di Azure. Per informazioni sul prodotto in anteprima che consente di eseguire Funzioni di Azure in locale, vedere [Panoramica sul runtime di Funzioni di Azure](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Creazione di app 1.x

Le nuove app create nel portale di Azure sono impostate su 2.x per impostazione predefinita, poiché questa è la versione più recente e in cui vengono effettuati gli investimenti per le nuove funzionalità.  È comunque possibile creare app 1.x eseguendo le operazioni seguenti.

1. Creare un'app per le funzioni di Azure dal portale di Azure
1. Aprire l'app creata e mentre è vuota aprire **Impostazioni funzione**
1. Modificare la versione da ~2 a ~1.  *Questo interruttore sarà disabilitato se sono presenti funzioni nell'app*.
1. Fare clic su Salva e riavviare l'app.  Tutti i modelli verranno creati ed eseguiti nella versione 1.x.

## <a name="cross-platform-development"></a>Sviluppo multipiattaforma

Il runtime 1.x supporta lo sviluppo e l'hosting solo nel portale o in Windows. Il runtime 2.x viene eseguito in .NET Core 2 e quindi può essere eseguito in tutte le piattaforme supportate da .NET Core, tra cui macOS e Linux. Questo consente scenari di sviluppo e hosting multipiattaforma.

## <a name="languages"></a>Lingue

Il runtime 2.x usa un nuovo modello di estendibilità del linguaggio. Inoltre, per migliorare la gestione e le prestazioni, nella versione 2.x ogni app può contenere solo funzioni in un unico linguaggio. I linguaggi attualmente supportati nella versione 2.x sono C#, F#, JavaScript e Java. I linguaggi sperimentali di Funzioni di Azure 1.x non sono stati aggiornati per usare il nuovo modello, quindi non sono supportati nella versione 2.x. Nella tabella seguente sono indicati i linguaggi di programmazione supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Migrazione da 1.x a 2.x

È possibile eseguire la migrazione di un'app esistente scritta in 1.x alla versione 2.x.  La maggior parte delle considerazioni necessarie per la migrazione tra le versioni riguarda le modifiche del runtime del linguaggio elencate in precedenza (ad esempio, il passaggio di C# da .NET Framework 4.7 a .NET Core 2).  È necessario verificare che il codice e le librerie siano compatibili con i runtime del linguaggio in uso.  È anche importante tenere presenti le modifiche apportate a trigger, binding e funzionalità evidenziate di seguito.

### <a name="changes-in-triggers-and-bindings"></a>Modifiche apportate a trigger e binding

Mentre la maggior parte delle proprietà e configurazioni di trigger e binding rimane invariata tra le versioni, nella versione 2.x è necessario installare eventuali trigger o binding per l'app. L'unica eccezione sono i trigger HTTP e Timer.  Vedere [Registrare e installare le estensioni di binding](./functions-triggers-bindings.md#register-binding-extensions).  Si noti che potrebbero anche essere state apportate modifiche in `function.json` o negli attributi di funzione tra le versioni (ad esempio, la proprietà `connection` di CosmosDB ora è `ConnectionStringSetting`).  Fare riferimento alla [tabella di binding esistente](#bindings) per collegamenti alla documentazione per ogni binding.

### <a name="changes-in-features-available"></a>Modifiche delle funzionalità disponibili

Oltre alle modifiche apportate a linguaggi e binding, alcune funzionalità sono state rimosse, aggiornate o sostituite tra le versioni.  Di seguito sono riportate alcune delle considerazioni principali relative all'uso della versione 2.x dopo la versione 1.x.  Nella versione 2.x sono state introdotte le modifiche seguenti:

* Le chiavi per chiamare una funzione verranno sempre archiviate nell'archiviazione BLOB crittografata. Nella versione 1.x per impostazione predefinita erano nell'archiviazione file e potevano essere spostate in un BLOB abilitando funzionalità come gli slot.  Se si esegue l'aggiornamento di un'app 1.x alla versione 2.x e sono presenti segreti nell'archiviazione file, tali segreti verranno reimpostati.
* Per migliorare le prestazioni, i trigger di tipo "webhook" sono stati rimossi e sostituiti con i trigger "HTTP".
* La configurazione dell'host (`host.json`) deve essere vuota o contenere `version` pari a `2.0` per una delle proprietà.
* Per migliorare il monitoraggio e la visibilità, il dashboard WebJobs (`AzureWebJobsDashboard`) è stato sostituito da [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Le impostazioni dell'applicazione (`local.settings.json`) richiedono un valore per la proprietà `FUNCTIONS_WORKER_RUNTIME` che esegue il mapping al linguaggio dell'app `dotnet | node | java | python`.
    * Per migliorare il footprint e i tempi di avvio, le app sono limitate a un singolo linguaggio. È possibile pubblicare più app in modo da avere funzioni scritte in linguaggi diversi per la stessa soluzione.
* Il timeout predefinito per le funzioni in un piano di servizio app è di 30 minuti.  Può comunque essere impostato manualmente come illimitato.
* [A causa delle limitazioni di .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), `.fsx` sono stati rimossi gli script per le funzioni F#. Le funzioni F# compilate sono ancora supportate.
* Il formato dei trigger basati su webhook (ad esempio, Griglia di eventi) è cambiato in `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Aggiornamento di un'applicazione sviluppata in locale

Se l'app 1.x è stata sviluppata in locale, è possibile apportare modifiche all'app o al progetto per renderli compatibili con la versione 2.  È consigliabile creare una nuova app ed eseguire il porting del codice alla nuova app.  Anche se si potrebbero apportate modifiche a un'app esistente per eseguire un aggiornamento sul posto, vi sono molti altri miglioramenti fra la versione 1 e la versione 2 che il codice legacy non è in grado di sfruttare (ad esempio, la modifica in C# da `TraceWriter` a `ILogger`).  

Il percorso consigliato è iniziare da uno dei modelli v2 e trasferire il codice in un nuovo progetto o app.

#### <a name="visual-studio-runtime-versions"></a>Versioni del runtime di Visual Studio

In Visual Studio è possibile selezionare la versione del runtime quando si crea un progetto.  Visual Studio dispone di entrambe le versioni principali e può usare in modo dinamico quella più adatta per il progetto.  Queste impostazioni sono derivate dal file `.csproj`.  Per le app 1.x il progetto contiene le proprietà

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

Nella versione 2 le proprietà del progetto sono

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Facendo clic su Debug o Pubblica, verrà impostata la versione corretta per le impostazioni del progetto.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code e Strumenti di base di Funzioni di Azure

Altri strumenti locali si basano su Strumenti di base di Funzioni di Azure.  Questi strumenti vengono installati nel computer e in genere è installata una sola versione in un computer di sviluppo alla volta.  Vedere le [istruzioni sull'installazione di versioni specifiche degli strumenti di base](./functions-run-local.md).

Per Visual Studio Code è anche necessario aggiornare l'impostazione utente per `azureFunctions.projectRuntime` in base alla versione degli strumenti installata.  In tal modo, verranno aggiornati anche i modelli e i linguaggi presentati durante la creazione di nuove app.

### <a name="changing-version-of-apps-in-azure"></a>Modifica della versione delle app in Azure

Le versioni delle app pubblicate vengono impostate tramite l'impostazione `FUNCTIONS_RUNTIME_VERSION` dell'applicazione.  È impostata su `~2` per le app v2 e su `~1` per le app v1.  È fortemente sconsigliato modificare la versione del runtime di un'app con funzioni esistenti pubblicate senza modificare anche il codice di tali funzioni.  Il percorso consigliato è creare una nuova app per le funzioni e impostare la versione appropriata, testare le modifiche e quindi disabilitare o eliminare l'app precedente.

## <a name="bindings"></a>Associazioni 

Il runtime 2.x usa un nuovo [modello di estendibilità di associazione](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) che offre questi vantaggi:

* Supporto per le estensioni di associazione di terze parti.
* Separazione di runtime e associazioni. Questo consente di applicare il controllo delle versioni alle estensioni di associazione e di rilasciarle in modo indipendente. È ad esempio possibile scegliere di eseguire l'aggiornamento a una versione di un'estensione basata su una nuova versione di un SDK sottostante.
* Ambiente di esecuzione più leggero, in cui solo le associazioni in uso sono note e vengono caricate dal runtime.

Tutte le associazioni di Funzioni di Azure predefinite hanno adottato questo modello e non sono più incluse per impostazione predefinita, tranne il trigger Timer e il trigger HTTP. Tali estensioni vengono installate automaticamente quando si creano funzioni con strumenti come Visual Studio o tramite il portale.

Nella tabella seguente sono indicate le associazioni supportate in ogni versione del runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
* [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md)
* [Note sulla versione](https://github.com/Azure/azure-functions-host/releases)
