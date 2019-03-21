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
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740275"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrare le estensioni delle associazioni di funzioni di Azure

Funzioni di Azure supporta HTTP e timer predefiniti. Per usare altri servizi, è necessario installare o *registrare* una [associazione](./functions-triggers-bindings.md) estensione. Le estensioni di associazione vengono fornite tramite pacchetti NuGet o strumenti di base di Azure. 

Nella tabella seguente indica come e quando si registra le associazioni.

| Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portale di Azure|Automatico|[Automatico con prompt](#azure-portal-development)|
|Linguaggi non .NET o lo sviluppo di strumenti di base di Azure locale|Automatico|[Usare i comandi CLI degli strumenti di base](#local-development-azure-functions-core-tools)|
|Libreria di classi C# usando Visual Studio 2017|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2017)|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2017)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#c-class-library-with-visual-studio-code)|

I tipi di associazione HTTP e timer sono eccezioni che non richiedono la registrazione esplicita, perché vengono registrati automaticamente in tutte le versioni e in tutti gli ambienti.

> [!IMPORTANT]
> Questo contenuto per il resto di questo articolo si applica solo a funzioni 2.x. Le estensioni delle associazioni non sono registrati in modo esplicito in funzioni 1.x tranne quando [creazione di un C# libreria di classi mediante Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Sviluppo con il portale di Azure

Quando si crea una funzione o si aggiunge un'associazione, compare un avviso quando è necessario registrare l'estensione dell'associazione o del trigger. Rispondere all'avviso facendo clic **Installa** per registrare l'estensione. L'installazione può richiedere fino a 10 minuti in un piano a consumo.

È necessario installare ogni estensione una sola volta per una determinata app per le funzioni. Per le associazioni supportate non disponibili nel portale o per aggiornare un'estensione installata, è anche possibile [installare o aggiornare manualmente le estensioni delle associazioni di Funzioni di Azure dal portale](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Sviluppo a livello locale con gli strumenti di base di Funzioni di Azure

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Libreria di classi C# con Visual Studio 2017

In **Visual Studio 2017** è possibile installare pacchetti dalla Console di Gestione pacchetti usando il comando [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<target_version>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="c-class-library-with-visual-studio-code"></a>Libreria di classi C# con Visual Studio Code

In **Visual Studio Code** è possibile installare i pacchetti dal prompt dei comandi usando il comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nell'interfaccia della riga di comando di .NET Core, come indicato nell'esempio seguente:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<target_version>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esempio di trigger e associazione di funzione Azure](./functions-bindings-example.md)

