---
title: Registrare le estensioni di binding di funzioni di Azure
description: Informazioni su come registrare un'estensione di binding di funzioni di Azure in base all'ambiente in uso.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: a045ef0fea70347f168e8ae0cc93e0c359f31dfa
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031124"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni di binding di funzioni di Azure

A partire da funzioni di Azure versione 2. x, le [associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti distinti dal runtime di funzioni. Mentre le funzioni .NET accedono ai binding tramite pacchetti NuGet, i bundle di estensione consentono ad altre funzioni di accedere a tutte le associazioni tramite un'impostazione di configurazione.

Considerare i seguenti elementi correlati alle estensioni di binding:

- Le estensioni di binding non vengono registrate in modo esplicito nelle funzioni 1. x tranne quando si [Crea una libreria di classi C# con Visual Studio](#local-csharp).

- I trigger HTTP e timer sono supportati per impostazione predefinita e non richiedono un'estensione.

La tabella seguente indica quando e come registrare le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in funzioni 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico<sup>*</sup>|
|Lingue Non-.NET o sviluppo locale di strumenti di Azure Core|Automatico|[Usare i bundle di Azure Functions Core Tools ed estensione](#extension-bundles)|
|Libreria di classi C# con Visual Studio|[Usare gli strumenti di NuGet](#vs)|[Usare gli strumenti di NuGet](#vs)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#vs-code)|

<sup>*</sup>Il portale USA bundle di estensione.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Bundle di estensione

Bundle di estensione consente di aggiungere un set compatibile di estensioni di associazione di funzioni all'app per le funzioni. Quando si usano i bundle, quando si compila l'app viene aggiunto un set predefinito di estensioni. I pacchetti di estensione definiti in un bundle sono verificati per essere compatibili tra loro, consentendo di evitare conflitti tra i pacchetti. I bundle di estensione consentono di evitare di dover pubblicare codice del progetto .NET con un progetto di funzioni non-.NET. I bundle di estensione sono abilitati nell'host.jsdell'app su file.  

È possibile usare i bundle di estensione con la versione 2. x e le versioni successive del runtime di funzioni. 

Usare i bundle di estensione per lo sviluppo locale usando Azure Functions Core Tools, Visual Studio Code e quando si compila in modalità remota. Quando si sviluppa localmente, assicurarsi di usare la versione più recente di [Azure Functions Core Tools](functions-run-local.md#v2). I bundle di estensione vengono usati anche durante lo sviluppo di funzioni nella portale di Azure. 

Se non si usano i bundle di estensione, è necessario installare .NET Core 2. x SDK nel computer locale prima di [installare in modo esplicito le estensioni di binding](#explicitly-install-extensions). Un file Extensions. csproj, che definisce in modo esplicito le estensioni richieste, viene aggiunto al progetto. I bundle di estensione rimuovono questi requisiti per lo sviluppo locale. 

Per usare i bundle di estensione, aggiornare il *host.jssu* file in modo da includere la voce seguente per `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Installare le estensioni in modo esplicito

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>Pacchetti NuGet

Per un progetto di funzioni basate su libreria di classi C#, è necessario installare bundle di estensioni appositamente progettati per progetti che non sono di classe 

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Libreria di classi C con Visual Studio

In **Visual Studio**è possibile installare i pacchetti dalla console di gestione pacchetti usando il comando [Install-Package](/nuget/tools/ps-ref-install-package) , come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per l'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle singole pagine del pacchetto in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono alle funzioni runtime 1. x o 2. x sono specificate nell'articolo di riferimento per l'associazione.

Se si usa `Install-Package` per fare riferimento a un'associazione, non è necessario usare i [bundle di estensione](#extension-bundles). Questo approccio è specifico per le librerie di classi compilate in Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Libreria di classi C# con Visual Studio Code

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
