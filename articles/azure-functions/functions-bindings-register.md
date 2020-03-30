---
title: Registrare le estensioni di associazione di Funzioni di AzureRegister Azure Functions binding extensions
description: Informazioni su come registrare un'estensione di associazione Funzioni di Azure in base all'ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277518"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni di associazione di Funzioni di AzureRegister Azure Functions binding extensions

In Funzioni di Azure versione 2.x [le associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti separati dal runtime delle funzioni. Mentre le funzioni .NET accedono alle associazioni tramite pacchetti NuGet, i pacchetti di estensione consentono ad altre funzioni di accedere a tutte le associazioni tramite un'impostazione di configurazione.

Considerare i seguenti elementi correlati alle estensioni di associazione:Consider the following items related to binding extensions:

- Le estensioni di associazione non vengono registrate in modo esplicito nelle funzioni 1.x, tranne quando si crea una libreria di classi [C.](#local-csharp)

- I trigger HTTP e timer sono supportati per impostazione predefinita e non richiedono un'estensione.

Nella tabella seguente viene indicato quando e come registrare le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico|
|Non-.NET di linguaggi o dello sviluppo locale di Azure Core Tools|Automatico|[Usare gli strumenti di base di Funzioni di Azure e i pacchetti di estensioneUse Azure Functions Core Tools and extension bundles](#extension-bundles)|
|Libreria di classi di C|[Usare gli strumenti di NuGet](#vs)|[Usare gli strumenti di NuGet](#vs)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Pacchetti di estensione per lo sviluppo locale

I pacchetti di estensione sono una tecnologia di distribuzione che consente di aggiungere un set compatibile di estensioni di associazione di Funzioni all'app per le funzioni. Un set predefinito di estensioni viene aggiunto quando si compila l'app. I pacchetti di estensione definiti in un pacchetto sono compatibili tra loro, il che consente di evitare conflitti tra i pacchetti. Si abilitano i pacchetti di estensione nel file host.json dell'app.  

È possibile usare i pacchetti di estensione con la versione 2.x e le versioni successive del runtime di Funzioni.You can use extension bundles with version 2.x and later versions of the Functions runtime. Quando si sviluppa in locale, assicurarsi di usare la versione più recente di Strumenti di base di Funzioni di [Azure.](functions-run-local.md#v2)

Usare i pacchetti di estensioni per lo sviluppo locale usando gli strumenti di base di Funzioni di Azure, il codice di Visual Studio e quando si compila in remoto.

Se non si usano pacchetti di estensioni, è necessario installare l'SDK di .NET Core 2.x nel computer locale prima di installare le estensioni di associazione. I pacchetti di estensione eliminano questo requisito per lo sviluppo locale. 

Per utilizzare i pacchetti di estensione, aggiornare il `extensionBundle`file *host.json* per includere la seguente voce per :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>Libreria\# di classi C con Visual Studio

In **Visual Studio**è possibile installare pacchetti dalla Console di gestione pacchetti utilizzando il comando [Install-Package,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per tale associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle singole pagine del pacchetto [NuGet.org](https://nuget.org). Le versioni principali che corrispondono a Funzioni runtime 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

Se si `Install-Package` utilizza per fare riferimento a un'associazione, non è necessario utilizzare pacchetti di [estensione](#extension-bundles). Questo approccio è specifico per le librerie di classi compilate in Visual Studio.This approach is specific for class libraries built in Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Libreria di classi C ' con Visual Studio Code

Nel codice di **Visual Studio**installare i pacchetti per un progetto di libreria di classi C, dal prompt dei comandi utilizzando il comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nella riga di comando di .NET Core. Nell'esempio seguente viene illustrato come aggiungere un'associazione:The following example demonstrates how you add a binding:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Sostituire `<BINDING_TYPE_NAME>` con il nome del pacchetto che contiene l'associazione necessaria. L'articolo di riferimento sull'associazione desiderato è presente [nell'elenco delle associazioni supportate.](./functions-triggers-bindings.md#supported-bindings)

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle singole pagine del pacchetto [NuGet.org](https://nuget.org). Le versioni principali che corrispondono a Funzioni runtime 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esempio di trigger e associazione di funzioni di AzureAzure Function trigger and binding example](./functions-bindings-example.md)
