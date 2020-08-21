---
title: Registrare le estensioni di binding di funzioni di Azure
description: Informazioni su come registrare un'estensione di binding di funzioni di Azure in base all'ambiente in uso.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689560"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni di binding di funzioni di Azure

A partire da funzioni di Azure versione 2. x, il runtime di funzioni include solo trigger HTTP e timer per impostazione predefinita. Altri [trigger e associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti separati.

Le app per le funzioni della libreria di classi .NET usano le associazioni installate nel progetto come pacchetti NuGet. I bundle di estensione consentono alle app per le funzioni non-.NET di usare le stesse associazioni senza dover gestire l'infrastruttura .NET.

La tabella seguente indica quando e come registrare le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in funzioni 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico<sup>*</sup>|
|Lingue Non-.NET|Automatico|Usare i [bundle di estensione](#extension-bundles) (scelta consigliata) o installare in [modo esplicito le estensioni](#explicitly-install-extensions)|
|Libreria di classi C# con Visual Studio|[Usare gli strumenti di NuGet](#vs)|[Usare gli strumenti di NuGet](#vs)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#vs-code)|

<sup>*</sup> Il portale USA bundle di estensione.

## <a name="access-extensions-in-non-net-languages"></a>Estensioni di accesso in lingue non-.NET

Per le app per le funzioni Java, JavaScript, PowerShell, Python e Custom handler è consigliabile usare bundle di estensione per accedere alle associazioni. Nei casi in cui non è possibile usare i bundle di estensione, è possibile installare in modo esplicito le estensioni di binding.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Bundle di estensione

Bundle di estensione è un modo per aggiungere un set compatibile di estensioni di associazione all'app per le funzioni. I bundle di estensione sono abilitati nell'host.jsdell'app * su* file.

È possibile usare i bundle di estensione con la versione 2. x e le versioni successive del runtime di funzioni.

Il controllo delle versioni dei bundle di estensione è. Ogni versione contiene un set specifico di estensioni di binding che vengono verificate per essere utilizzate insieme. Selezionare una versione del bundle in base alle estensioni necessarie nell'app.

Per aggiungere un bundle di estensione all'app per le funzioni, aggiungere la `extensionBundle` sezione *host.js*. In molti casi, Visual Studio Code e Azure Functions Core Tools lo aggiungeranno automaticamente.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

La tabella seguente elenca le versioni attualmente disponibili del bundle default *Microsoft. Azure. Functions. ExtensionBundle* e i collegamenti alle estensioni che includono.

| Versione bundle | Versione in host.json | Estensioni incluse |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Vedere [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usato per generare il bundle |
| 2.x | `[2.*, 3.0.0)` | Vedere [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usato per generare il bundle |

> [!NOTE]
> Sebbene sia possibile specificare un intervallo di versioni personalizzato in host.json, è consigliabile usare un valore di versione di questa tabella.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Installare le estensioni in modo esplicito

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Installare le estensioni da NuGet nei linguaggi .NET

Per un progetto di funzioni basate su libreria di classi C#, è necessario installare direttamente le estensioni. I bundle di estensione sono progettati in modo specifico per i progetti che non sono basati su libreria di classi C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Libreria di classi C con Visual Studio

In **Visual Studio**è possibile installare i pacchetti dalla console di gestione pacchetti usando il comando [Install-Package](/nuget/tools/ps-ref-install-package) , come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per l'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle singole pagine del pacchetto in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono alle funzioni runtime 1. x o 2. x sono specificate nell'articolo di riferimento per l'associazione.

Se si usa `Install-Package` per fare riferimento a un'associazione, non è necessario usare i [bundle di estensione](#extension-bundles). Questo approccio è specifico per le librerie di classi compilate in Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Libreria di classi C# con Visual Studio Code

In **Visual Studio Code**, installare i pacchetti per un progetto libreria di classi C# dal prompt dei comandi usando il comando [DotNet add Package](/dotnet/core/tools/dotnet-add-package) nel interfaccia della riga di comando di .NET Core. Nell'esempio seguente viene illustrato come aggiungere un'associazione:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Sostituire `<BINDING_TYPE_NAME>` con il nome del pacchetto che contiene l'associazione necessaria. È possibile trovare l'articolo di riferimento per l'associazione desiderato nell' [elenco di associazioni supportate](./functions-triggers-bindings.md#supported-bindings).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle singole pagine del pacchetto in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono alle funzioni runtime 1. x o 2. x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Trigger di funzione di Azure ed esempio di associazione](./functions-bindings-example.md)
