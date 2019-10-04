---
title: Configurare Funzioni di Azure per i provider personalizzati di Azure
description: Questa esercitazione illustra come creare un'app per le funzioni di Azure e configurarla per l'uso con i provider personalizzati di Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173021"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurare Funzioni di Azure per i provider personalizzati di Azure

Un provider personalizzato è un contratto tra Azure e un endpoint. Con i provider personalizzati, è possibile cambiare i flussi di lavoro in Azure. Questa esercitazione illustra come configurare un'app per le funzioni di Azure da usare come endpoint per i provider personalizzati.

## <a name="create-the-azure-function-app"></a>Creare l'app per le funzioni di Azure

> [!NOTE]
> In questa esercitazione viene creato un semplice endpoint di servizio che usa un'app per le funzioni di Azure. Tuttavia, un provider personalizzato può usare qualsiasi endpoint accessibile pubblicamente. Le alternative includono App per la logica di Azure, Gestione API di Azure e la funzionalità App Web del Servizio app di Azure.

Prima di avviare questa esercitazione, è necessario seguire l'esercitazione per [creare la prima app per le funzioni di Azure nel portale di Azure](../azure-functions/functions-create-first-azure-function.md). Questa esercitazione consente di creare una funzione webhook di .NET Core modificabile nel portale di Azure. Rappresenta anche la base per l'esercitazione corrente.

## <a name="install-azure-table-storage-bindings"></a>Installare i binding di archiviazione tabelle di Azure

Per istallare i binding di archiviazione tabelle di Azure:

1. Passare alla scheda **Integrazione** per Trigger HTTP.
1. Selezionare **+ Nuovo input**.
1. Selezionare **Archiviazione tabelle di Azure**.
1. Installare l'estensione Microsoft.Azure.WebJobs.Extensions.Storage se non è già installata.
1. Nella casella **Nome del parametro della tabella** immettere **tableStorage**.
1. Nella casella **Nome tabella** immettere **myCustomResources**.
1. Selezionare **Salva** per salvare il parametro di input aggiornato.

![Panoramica del provider personalizzato che mostra i binding delle tabelle](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aggiornare i metodi HTTP RESTful

Per configurare la funzione di Azure in modo da includere i metodi di richiesta RESTful del provider personalizzato:

1. Passare alla scheda **Integrazione** per Trigger HTTP.
1. In **Metodi HTTP selezionati** selezionare **GET**, **POST**, **DELETE** e **PUT**.

![Panoramica del provider personalizzato che mostra i metodi HTTP](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Aggiungere i pacchetti NuGet di Azure Resource Manager

> [!NOTE]
> Se il file di progetto C# non è presente nella directory di progetto, è possibile aggiungerlo manualmente. Oppure comparirà dopo l'installazione dell'estensione Microsoft.Azure.WebJobs.Extensions.Storage nell'app per le funzioni.

Aggiornare quindi il file di progetto C# per includere alcune utili librerie NuGet. Queste librerie facilitano l'analisi delle richieste in ingresso provenienti dai provider personalizzati. Seguire la procedura per [aggiungere estensioni dal portale](../azure-functions/install-update-binding-extensions-manual.md) e aggiornare il file di progetto C# per includere i riferimenti ai pacchetti seguenti:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

L'elemento XML seguente è un esempio di file di progetto C#:

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

In questa esercitazione è stata configurata un'app per le funzioni di Azure da usare come endpoint di provider personalizzati di Azure.

Per informazioni su come creare un endpoint RESTful per provider personalizzati, vedere [Esercitazione: Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md).

