---
title: Registrare le estensioni di binding di funzioni di Azure
description: Informazioni su come registrare un'estensione di binding di funzioni di Azure in base all'ambiente in uso.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086474"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni di binding di funzioni di Azure

In funzioni di Azure versione 2. x, le [associazioni](./functions-triggers-bindings.md) sono disponibili come pacchetti distinti dal runtime di funzioni. Mentre le funzioni .NET accedono ai binding tramite pacchetti NuGet, i bundle di estensione consentono ad altre funzioni di accedere a tutte le associazioni tramite un'impostazione di configurazione.

Considerare i seguenti elementi correlati alle estensioni di binding:

- Le estensioni di binding non vengono registrate in modo esplicito in funzioni 1. x tranne quando si [Crea una C# libreria di classi con Visual Studio](#local-csharp).

- I trigger HTTP e timer sono supportati per impostazione predefinita e non richiedono un'estensione.

La tabella seguente indica quando e come registrare le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|Automatico|
|Lingue Non-.NET o sviluppo locale di strumenti di Azure Core|Automatico|[Usare i bundle di Azure Functions Core Tools ed estensione](#extension-bundles)|
|C#libreria di classi con Visual Studio|[Usare gli strumenti di NuGet](#vs)|[Usare gli strumenti di NuGet](#vs)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#vs-code)|

## <a name="extension-bundles"></a>Bundle di estensione per lo sviluppo locale

Bundle di estensione è una tecnologia di sviluppo locale per il runtime della versione 2. x che consente di aggiungere un set compatibile di estensioni di associazione di funzioni al progetto dell'app per le funzioni. Questi pacchetti di estensione vengono quindi inclusi nel pacchetto di distribuzione quando si esegue la distribuzione in Azure. Bundle rende disponibili tutte le associazioni pubblicate da Microsoft tramite un'impostazione nel file *host. JSON* . I pacchetti di estensione definiti in un bundle sono compatibili tra loro, consentendo di evitare conflitti tra i pacchetti. Quando si sviluppa localmente, assicurarsi di usare la versione più recente di [Azure Functions Core Tools](functions-run-local.md#v2).

Usare i bundle di estensione per lo sviluppo locale usando Azure Functions Core Tools o Visual Studio Code.

Se non si usano i bundle di estensione, è necessario installare .NET Core 2. x SDK nel computer locale prima di installare le estensioni di binding. Bundles rimuove questo requisito per lo sviluppo locale. 

Per usare i bundle di estensione, aggiornare il file *host. JSON* in modo da includere la `extensionBundle`voce seguente per:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

In `extensionBundle`sono disponibili le proprietà seguenti:

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| **`id`** | Spazio dei nomi per i bundle di estensioni di funzioni Microsoft Azure. |
| **`version`** | Versione del bundle da installare. Il runtime di funzioni sceglie sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versioni. Il valore della versione precedente consente a tutte le versioni del bundle da 1.0.0 fino a, ma non incluso 2.0.0. Per ulteriori informazioni, vedere la [notazione intervallo per specificare gli intervalli di versione](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Le versioni del bundle vengono incrementate come pacchetti nella modifica del bundle. Le modifiche alla versione principale si verificano quando i pacchetti nel bundle vengono incrementati di una versione principale, che in genere coincide con una modifica nella versione principale del runtime di funzioni.  

Il set corrente di estensioni installate dal bundle predefinito viene enumerato in questo [file Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="vs"></a>Libreria\# di classi C con Visual Studio

In **Visual Studio**è possibile installare i pacchetti dalla console di gestione pacchetti usando il comando [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Il nome del pacchetto utilizzato per una determinata associazione viene fornito nell'articolo di riferimento per l'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

Se si utilizza `Install-Package` per fare riferimento a un'associazione, non è necessario utilizzare i [bundle di estensione](#extension-bundles). Questo approccio è specifico per le librerie di classi compilate in Visual Studio.

## <a name="vs-code"></a>C# libreria di classi con Visual Studio Code

> [!NOTE]
> Si consiglia di usare [bundle di estensione](#extension-bundles) per installare automaticamente un set compatibile di pacchetti di estensioni di binding.

In **Visual Studio Code**, installare i pacchetti per C# un progetto di libreria di classi dal prompt dei comandi usando il comando [DotNet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nel interfaccia della riga di comando di .NET Core. Nell'esempio seguente viene illustrato come aggiungere un'associazione:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Sostituire `<BINDING_TYPE_NAME>` con il nome del pacchetto fornito nell'articolo di riferimento per l'associazione desiderata. È possibile trovare l'articolo di riferimento per l'associazione desiderato nell' [elenco di associazioni supportate](./functions-triggers-bindings.md#supported-bindings).

Sostituire `<TARGET_VERSION>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Trigger di funzione di Azure ed esempio di associazione](./functions-bindings-example.md)
