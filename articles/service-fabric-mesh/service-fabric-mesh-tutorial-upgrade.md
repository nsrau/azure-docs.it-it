---
title: "Esercitazione: Aggiornare un'applicazione Azure Service Fabric Mesh | Microsoft Docs"
description: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: c23646bca6109d27e57b2f928363e65c83c634eb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031153"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Esercitazione: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio

Questa esercitazione è la quarta parte di una serie e mostra come aggiornare un'applicazione Azure Service Fabric Mesh direttamente da Visual Studio. L'aggiornamento includerà sia un aggiornamento del codice sia un aggiornamento della configurazione. Si noti che i passaggi per l'aggiornamento e la pubblicazione da Visual Studio sono gli stessi.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Aggiornare un servizio Service Fabric Mesh tramite Visual Studio

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'app Service Fabric Mesh in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Eseguire il debug di un'app Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuire un'app Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Aggiornare un'app Service Fabric Mesh
> * [Pulire le risorse di Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non è stata distribuita l'app to-do, seguire le istruzioni in [Pubblicare un'app Web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Aggiornare un servizio Service Fabric Mesh tramite Visual Studio

Questo articolo illustra come eseguire l'aggiornamento di un microservizio all'interno di un'applicazione in modo indipendente.  In questo esempio si modificherà il servizio `WebFrontEnd` per visualizzare una categoria di attività. Verrà quindi eseguito l'aggiornamento del servizio distribuito.

## <a name="modify-the-config"></a>Modificare la configurazione

Gli aggiornamenti possono essere dovuti a modifiche al codice, alla configurazione o ad entrambi.  Per introdurre una modifica alla configurazione, aprire il file `WebFrontEnd` del progetto `service.yaml`, presente nel nodo **Risorse del servizio**.

Nella sezione `resources:` modificare `cpu:` da 0.5 a 1.0, in previsione di un ampio utilizzo del front-end Web. L'aspetto dovrebbe risultare simile al seguente:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Modificare il modello

Per introdurre una modifica al codice, aggiungere la proprietà `Category` alla classe `ToDoItem` nel file `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Aggiornare quindi il metodo `Load()`, nello stesso file, per impostare la categoria su una stringa predefinita:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modificare il servizio

Il progetto `WebFrontEnd` è un'applicazione ASP.NET Core con una pagina Web che mostra gli elementi elenco attività da eseguire. Nel progetto `WebFrontEnd` aprire `Index.cshtml` e aggiungere le due righe, indicate di seguito, per visualizzare la categoria dell'attività:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Compilare ed eseguire l'app per verificare che sia presente una colonna per la nuova categoria nella pagina Web in cui sono elencate le attività.

## <a name="upgrade-the-app-from-visual-studio"></a>Aggiornare l'app da Visual Studio

Indipendentemente dal fatto che si stia eseguendo un aggiornamento del codice o della configurazione (in questo caso entrambe le operazioni), per eseguire l'aggiornamento dell'app Service Fabric Mesh in Azure fare clic con il pulsante destro del mouse su **todolistapp** in Visual Studio e selezionare **Pubblica...**

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Assicurarsi che **Località** sia impostata sulla località usata quando è stata pubblicata originariamente l'app to-do in Azure. In questo articolo è stato usato **Stati Uniti orientali**.

Assicurarsi che **Gruppo di risorse** sia impostato sul gruppo di risorse usato quando è stata pubblicata originariamente l'app to-do in Azure.

Assicurarsi che **Registro contenitori di Azure** sia impostato sul nome del registro contenitori di Azure creato quando è stata pubblicata originariamente l'app to-do in Azure.

Nella finestra di dialogo per la pubblicazione fare clic sul pulsante **Pubblica** per aggiornare l'app to-do in Azure.

È possibile monitorare lo stato di avanzamento dell'aggiornamento selezionando il riquadro **Strumenti di Service Fabric** nella finestra **Output** di Visual Studio. Al termine dell'aggiornamento, nell'output di **Strumenti di Microsoft Azure Service Fabric per Visual Studio** verranno visualizzati l'indirizzo IP e la porta dell'applicazione sotto forma di URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Aprire un Web browser e passare all'URL visualizzato per visitare il sito Web in esecuzione in Azure. Dovrebbe essere visualizzata una pagina Web contenente una colonna della categoria.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso:
> [!div class="checklist"]
> * Come aggiornare un'app Service Fabric Mesh tramite Visual Studio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Pulire le risorse di Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)