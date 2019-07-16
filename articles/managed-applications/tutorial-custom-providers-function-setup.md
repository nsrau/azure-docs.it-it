---
title: Configurare Funzioni di Azure per i provider personalizzati di Azure
description: Questa esercitazione illustra come creare una funzione di Azure e configurarla per l'uso con provider personalizzati di Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799120"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurare Funzioni di Azure per i provider personalizzati di Azure

I provider personalizzati consentono di personalizzare i flussi di lavoro in Azure. Un provider personalizzato è un contratto tra Azure e un `endpoint`. Questa esercitazione illustra il processo di configurazione di una funzione di Azure come `endpoint` di un provider personalizzato.

Questa esercitazione è suddivisa nei passaggi seguenti:

- Creazione della funzione di Azure
- Installare le associazioni di tabelle di Azure
- Aggiornare i metodi HTTP RESTful
- Aggiungere i pacchetti NuGet di Azure Resource Manager

Questa esercitazione si basa sulle esercitazioni seguenti:

- [Creazione della prima funzione di Azure tramite il portale di Azure](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Creazione della funzione di Azure

> [!NOTE]
> In questa esercitazione verrà creato un endpoint servizio semplice con una funzione di Azure, ma un provider personalizzato può usare qualsiasi `endpoint` accessibile pubblicamente. In alternativa, è possibile usare App per la logica di Azure, Gestione API di Azure e App Web di Azure.

Per iniziare questa esercitazione, è necessario seguire prima l'esercitazione sulla [creazione della prima funzione di Azure nel portale di Azure](../azure-functions/functions-create-first-azure-function.md). L'esercitazione consente di creare una funzione webhook di .NET Core modificabile nel portale di Azure.

## <a name="install-azure-table-bindings"></a>Installare le associazioni di tabelle di Azure

In questa sezione verranno illustrati i passaggi per installare rapidamente le associazioni di Archiviazione tabelle di Azure.

1. Passare alla scheda `Integrate` per HttpTrigger.
2. Fare clic su `+ New Input`.
3. Selezionare `Azure Table Storage`.
4. Installare `Microsoft.Azure.WebJobs.Extensions.Storage` se non è già installato.
5. Aggiornare `Table parameter name` specificando "tableStorage" e `Table name` specificando "myCustomResources".
6. Salvare il parametro di input aggiornato.

![Panoramica sui provider personalizzati](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aggiornare i metodi HTTP RESTful

In questa sezione verranno illustrati i passaggi per configurare rapidamente la funzione di Azure in modo da includere i metodi di richiesta RESTful per i provider personalizzati.

1. Passare alla scheda `Integrate` per HttpTrigger.
2. Aggiornare `Selected HTTP methods` in: GET, POST, DELETE e PUT.

![Panoramica sui provider personalizzati](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Modifica del file csproj

> [!NOTE]
> Se il file csproj non è presente nella directory, è possibile aggiungerlo manualmente oppure attendere che venga visualizzato in seguito all'installazione dell'estensione `Microsoft.Azure.WebJobs.Extensions.Storage` nella funzione.

In seguito, il file csproj verrà aggiornato in modo da includere utili librerie NuGet per semplificare l'analisi delle richieste in arrivo da provider personalizzati. Seguire la procedura per [aggiungere estensioni dal portale](../azure-functions/install-update-binding-extensions-manual.md) e aggiornare il file csproj in modo che includa i riferimenti a pacchetti seguenti:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

File csproj di esempio:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata configurata una funzione di Azure per l'uso come `endpoint` di un provider personalizzato di Azure. Passare all'articolo successivo per informazioni su come creare l'`endpoint` di un provider personalizzato RESTful.

- [Esercitazione: Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md)
