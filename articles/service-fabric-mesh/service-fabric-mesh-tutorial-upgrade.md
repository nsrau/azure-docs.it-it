---
title: Esercitazione- Aggiornare un'applicazione mesh di Azure Service FabricTutorial- Upgrade an Azure Service Fabric Mesh application
description: Questa esercitazione è la quarta parte di una serie e mostra come aggiornare un'applicazione Azure Service Fabric Mesh direttamente da Visual Studio.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351708"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Esercitazione: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio

Questa esercitazione è la quarta parte di una serie e mostra come aggiornare un'applicazione Azure Service Fabric Mesh direttamente da Visual Studio. L'aggiornamento includerà sia un aggiornamento del codice sia un aggiornamento della configurazione. Si noterà che i passaggi per l'aggiornamento e la pubblicazione da All'interno di Visual Studio sono gli stessi.

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

Questo articolo illustra come aggiornare un microservizio all'interno di un'applicazione. In questo esempio, modificheremo `WebFrontEnd` il servizio per visualizzare una categoria di attività e aumentare la quantità di CPU specificata. Verrà quindi eseguito l'aggiornamento del servizio distribuito.

## <a name="modify-the-config"></a>Modificare la configurazione

Quando si crea un'app Mesh di Service Fabric, Visual Studio aggiunge un file **parameters.yaml** per ogni ambiente di distribuzione (cloud e locale). In questi file, è possibile definire i parametri e i relativi valori a cui è possibile fare riferimento dai file Mesh con estensione yaml, ad esempio service.yaml o network.yaml.  Visual Studio fornisce alcune variabili, ad esempio la quantità di CPU che il servizio può utilizzare.

Aggiorneremo il `WebFrontEnd_cpu` parametro per aggiornare `1.5` le risorse cpu in previsione che il servizio **WebFrontEnd** verrà utilizzato più pesantemente.

1. Nel progetto **todolistapp,** in **Environments** > **Cloud**, aprire il file **parameters.yaml.** Modificare `WebFrontEnd_cpu`il valore `1.5`di , in . Il nome del parametro è `WebFrontEnd_` preceduto dal nome del servizio come procedura consigliata per distinguerlo dai parametri con lo stesso nome che si applicano a servizi diversi.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Aprire il file **service.yaml** del progetto **WebFrontEnd** in **WebFrontEnd** > **Service Resources**.

    Si noti `resources:` `cpu:` che la `"[parameters('WebFrontEnd_cpu')]"`sezione in è impostata su . Se il progetto viene compilato per il `'WebFrontEnd_cpu` cloud, il valore per verrà ricavato dal `1.5`file **Environments** > **Cloud** > **parameters.yaml** e sarà . Se il progetto viene compilato per essere eseguito in locale, il valore verrà ricavato dal file **Environments** > **Local** > **parameters.yaml** e sarà '0.5'.

> [!Tip]
> Per impostazione predefinita, il file dei parametri che è un peer del file profile.yaml verrà utilizzato per fornire i valori per tale file profile.yaml.
> Ad esempio, Environments > Cloud > parameters.yaml fornisce i valori dei parametri per Environments > Cloud > profile.yaml.
>
> È possibile eseguire l'override aggiungendo quanto segue al`parametersFilePath=”relative or full path to the parameters file”` file `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` profile.yaml: Ad esempio,`parametersFilePath=”..\CommonParameters.yaml”`

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

Se si sta effettuando un aggiornamento del codice o un aggiornamento di configurazione (in questo caso stiamo facendo entrambe le cose), aggiornare l'app Service Fabric Mesh in Azure facendo clic con il pulsante destro del mouse su **todolistapp** in Visual Studio e quindi selezionare **Pubblica...**

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

Utilizzare l'elenco a discesa **Profilo** di destinazione per selezionare il file profile.yaml da utilizzare per questa distribuzione. Stiamo aggiornando l'app nel cloud in modo da selezionare il **cloud.yaml** nell'elenco a discesa, che utilizzerà il `WebFrontEnd_cpu` valore di 1.0 definito in quel file.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Impostare il percorso sul percorso usato quando è stata originariamente pubblicata l'app To-Do su Azure.Set the **Location** to the location that you used when you originally published the to-do app to Azure. In questo articolo è stato usato **Stati Uniti orientali**.

Impostare **Gruppo di risorse** sul gruppo di risorse usato al momento della pubblicazione iniziale dell'app To-Do in Azure.Set Resource group to the resource group that you used when you originally published the To-do app to Azure.

Impostare **Il Registro** di sistema contenitori di Azure sul nome del Registro di sistema del contenitore di Azure creato al momento della pubblicazione originale dell'app To-Do in Azure.Set Azure Container Registry to the azure container registry name that you created when you originally published the to-do app to-do to Azure.

Nella finestra di dialogo per la pubblicazione fare clic sul pulsante **Pubblica** per aggiornare l'app to-do in Azure.

Monitorare lo stato dell'aggiornamento selezionando il riquadro **Strumenti di Service Fabric** nella finestra **Output** di Visual Studio. 

Dopo che l'immagine è stata compilata ed inviata al Registro di sistema del contenitore di Azure, nell'output verrà visualizzato un collegamento Per sullo stato su cui è possibile fare clic per monitorare la distribuzione nel portale di Azure.After the image is built and pushed to the Azure Container Registry, a **For status** link will appear in the output that you can click to monitor the deployment in the Azure portal.

Al termine dell'aggiornamento, nell'output di **Strumenti di Microsoft Azure Service Fabric per Visual Studio** verranno visualizzati l'indirizzo IP e la porta dell'applicazione sotto forma di URL.

```json
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