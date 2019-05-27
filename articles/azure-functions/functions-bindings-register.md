---
title: Registrare le estensioni delle associazioni di funzioni di Azure
description: Informazioni su come registrare un'estensione di binding di funzioni di Azure nel proprio ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864554"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni delle associazioni di funzioni di Azure

In funzioni di Azure versione 2.x [associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti separati dal runtime di funzioni. Sebbene funzioni .NET accedono associazioni tramite pacchetti NuGet, pacchetti di estensione consentono l'accesso altre funzioni a tutte le associazioni tramite un'impostazione di configurazione.

Prendere in considerazione gli elementi seguenti correlati a estensioni di binding:

- Le estensioni delle associazioni non sono registrati in modo esplicito in funzioni 1.x tranne quando [creazione di un C# libreria di classi mediante Visual Studio 2019](#local-csharp).

- Trigger timer e HTTP sono supportati per impostazione predefinita e non richiedono un'estensione.

Nella tabella seguente indica come e quando si registra le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico|
|Linguaggi non .NET o lo sviluppo di strumenti di base di Azure locale|Automatico|[Usare Azure Functions Core Tools e aggregazioni di estensione](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#libreria di classi mediante Visual Studio 2019|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2019)|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2019)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Sviluppo locale con Azure Functions Core Tools e aggregazioni di estensione

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#libreria di classi con Visual Studio 2019

Nelle **Visual Studio 2019**, è possibile installare i pacchetti dalla Console di gestione pacchetti usando la [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) comando, come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="c-class-library-with-visual-studio-code"></a>Libreria di classi C# con Visual Studio Code

In **Visual Studio Code** è possibile installare i pacchetti dal prompt dei comandi usando il comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nell'interfaccia della riga di comando di .NET Core, come indicato nell'esempio seguente:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esempio di trigger e associazione di funzione Azure](./functions-bindings-example.md)

