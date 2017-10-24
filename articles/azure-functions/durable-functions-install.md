---
title: Installare l'estensione Funzioni permanenti e i relativi esempi - Azure
description: Informazioni su come installare l'estensione Funzioni permanenti per Funzioni di Azure, per lo sviluppo nel portale o in Visual Studio.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installare l'estensione Funzioni permanenti e i relativi esempi (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) di Funzioni di Azure è inclusa nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Questo articolo illustra come installare il pacchetto e un set di esempi per gli ambienti di sviluppo seguenti:

* Visual Studio 2017 (scelta consigliata) 
* Portale di Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio offre al momento l'esperienza ottimale per lo sviluppo di applicazioni che usano Funzioni permanenti.  Le funzioni possono essere eseguite in locale ed essere pubblicate anche in Azure. È possibile iniziare con un progetto vuoto o con un set di funzioni di esempio.

### <a name="prerequisites"></a>Prerequisiti

* Installare la versione più recente di [Visual Studio](https://www.visualstudio.com/downloads/) (versione 15.3 o superiore). Includere gli strumenti di Azure nelle opzioni di installazione.

### <a name="start-with-sample-functions"></a>Iniziare con funzioni di esempio

1. Scaricare il file [Sample App con estensione zip per Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Non è necessario aggiungere il riferimento di NuGet perché già incluso nel progetto di esempio.
2. Installare ed eseguire [Emulatore di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) versione 5.2 o successive. In alternativa, è possibile aggiornare il file *local.appsettings.json* con le stringhe di connessione di Archiviazione di Azure autentiche.
3. Aprire il progetto in Visual Studio 2017. 
4. Per istruzioni su come eseguire l'esempio, iniziare con l'esempio relativo al [Concatenamento delle funzioni: Sequenza Hello di esempio](durable-functions-sequence.md). L'esempio può essere eseguito in locale o pubblicato in Azure.

### <a name="start-with-an-empty-project"></a>Iniziare con un progetto vuoto
 
Seguire le stesse istruzioni fornite per l'avvio con l'esempio, ma eseguire i passaggi seguenti invece di scaricare il file con estensione *zip*:

1. Creare un progetto di app per le funzioni.
2. Aggiungere il riferimento al pacchetto NuGet seguente al file *csproj*:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Portale di Azure

È possibile usare il portale di Azure per lo sviluppo di Funzioni permanenti.

### <a name="create-an-orchestrator-function"></a>Creare una funzione di agente di orchestrazione

1. Creare una nuova app per le funzioni in [functions.azure.com](https://functions.azure.com/signin).
2. Configurare l'app per le funzioni affinché usi la [versione di runtime 2.0](functions-versions.md).
3. Creare una nuova funzione e selezionare il modello dell'**agente di orchestrazione di Funzioni permanenti - C#**.
4. In **Estensioni non installate**, fare clic su **Installa** per scaricare l'estensione da NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Copiare il codice di esempio nell'app per le funzioni

1. Scaricare il file [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip).
2. Decomprimere il file di esempio in `D:\home\site\wwwroot` nell'app per le funzioni, usando Kudu o FTP.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la funzione di concatenamento di esempio](durable-functions-sequence.md)
