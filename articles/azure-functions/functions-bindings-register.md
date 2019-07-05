---
title: Registrare le estensioni delle associazioni di funzioni di Azure
description: Informazioni su come registrare un'estensione di binding di funzioni di Azure nel proprio ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480341"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni delle associazioni di funzioni di Azure

In funzioni di Azure versione 2.x [associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti separati dal runtime di funzioni. Sebbene funzioni .NET accedono associazioni tramite pacchetti NuGet, pacchetti di estensione consentono l'accesso altre funzioni a tutte le associazioni tramite un'impostazione di configurazione.

Prendere in considerazione gli elementi seguenti correlati a estensioni di binding:

- Le estensioni delle associazioni non sono registrati in modo esplicito in funzioni 1.x tranne quando [creazione di un C# libreria di classi mediante Visual Studio](#local-csharp).

- Trigger timer e HTTP sono supportati per impostazione predefinita e non richiedono un'estensione.

Nella tabella seguente indica come e quando si registra le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico|
|Linguaggi non .NET o lo sviluppo di strumenti di base di Azure locale|Automatico|[Usare Azure Functions Core Tools e aggregazioni di estensione](#extension-bundles)|
|C#libreria di classi mediante Visual Studio 2019|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2019)|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2019)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>Pacchetti di estensione per lo sviluppo locale

Bundle di estensione è una tecnologia di sviluppo locale per la versione 2.x del runtime che consente di aggiungere un insieme compatibile di funzioni di associazione delle estensioni per il progetto dell'app funzioni. Questi pacchetti di estensione sono quindi incluse nel pacchetto di distribuzione quando si distribuiscono in Azure. Bundle rende pubblicati da Microsoft disponibili tramite un'impostazione in tutti i binding la *host. JSON* file. I pacchetti di estensione definiti in un bundle sono compatibili tra loro, in modo da evitare conflitti tra pacchetti. Quando lo sviluppo in locale, assicurarsi che si usa la versione più recente di [Azure Functions Core Tools](functions-run-local.md#v2).

Usare pacchetti di estensione per i progetti di sviluppo locale usando Azure Functions Core Tools oppure Visual Studio Code.

Se non si usano pacchetti di estensione, è necessario installare .NET Core 2.x SDK nel computer locale prima di installare le estensioni di associazione. Bundle rimuove questo requisito per lo sviluppo locale. 

Per usare pacchetti di estensione, aggiornare il *host. JSON* file da includere la voce seguente per `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Le proprietà seguenti sono disponibili in `extensionBundle`:

| Proprietà | Descrizione |
| -------- | ----------- |
| **`id`** | Lo spazio dei nomi per il bundle di estensione per funzioni di Microsoft Azure. |
| **`version`** | La versione del bundle da installare. Il runtime di funzioni sceglie sempre la versione massima consentita definita per l'intervallo e intervallo di versioni. Il valore della versione precedente consente a tutte le versioni di bundle da 1.0.0 fino a ma escludendo 2.0.0. Per altre informazioni, vedere la [notazione di intervallo per la specifica di intervalli di versione](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Creare un bundle incremento delle versioni dei pacchetti nella modifica bundle. Modifiche di versione principale si verificano quando i pacchetti del bundle incremento di una versione principale, che in genere coincide con una modifica nella versione principale del runtime di funzioni.  

Il set di estensioni installate per l'aggregazione predefinita corrente sono enumerate nel [extensions.json file](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# libreria di classi con Visual Studio 2019

Nelle **Visual Studio 2019**, è possibile installare i pacchetti dalla Console di gestione pacchetti usando la [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) comando, come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="c-class-library-with-visual-studio-code"></a>Libreria di classi C# con Visual Studio Code

> [!NOTE]
> È consigliabile usare [bundle estensione](#extension-bundles) affinché funzioni installare automaticamente un insieme compatibile di pacchetti di estensione di associazione.

Nella **Visual Studio Code**, installare i pacchetti per un C# progetto di libreria di classi dal prompt dei comandi usando il [dotnet Aggiungi pacchetto](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando della riga di comando .NET Core, come illustrato nell'esempio seguente:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esempio di trigger e associazione di funzione Azure](./functions-bindings-example.md)

