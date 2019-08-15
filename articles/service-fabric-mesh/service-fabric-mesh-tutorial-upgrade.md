---
title: "Esercitazione: Aggiornare un'applicazione Azure Service Fabric Mesh | Microsoft Docs"
description: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 20aa65f0a8e47485e71fd03d73ff144f5290bcb7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036086"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Esercitazione: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio

Questa esercitazione è la quarta parte di una serie e mostra come aggiornare un'applicazione Azure Service Fabric Mesh direttamente da Visual Studio. L'aggiornamento includerà sia un aggiornamento del codice sia un aggiornamento della configurazione. Si noterà che i passaggi per l'aggiornamento e la pubblicazione da Visual Studio sono gli stessi.

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

Questo articolo illustra come aggiornare un microservizio all'interno di un'applicazione. In questo esempio, si modificherà il `WebFrontEnd` servizio per visualizzare una categoria di attività e si aumenterà la quantità di CPU fornita. Verrà quindi eseguito l'aggiornamento del servizio distribuito.

## <a name="modify-the-config"></a>Modificare la configurazione

Quando si crea un'app mesh Service Fabric, Visual Studio aggiunge un file **Parameters. YAML** per ogni ambiente di distribuzione (cloud e locale). In questi file è possibile definire i parametri e i relativi valori a cui è possibile fare riferimento dai file mesh *. YAML, ad esempio Service. YAML o Network. yaml.  Visual Studio fornisce alcune variabili, ad esempio la quantità di CPU che il servizio può usare.

Il `WebFrontEnd_cpu` parametro verrà aggiornato in modo da aggiornare le `1.5` risorse della CPU in anticipo che il servizio WebFrontEnd verrà usato più frequentemente.

1. Nel progetto **todolistapp** , in **ambienti** > **cloud**, aprire il file **Parameters. YAML** . Modificare il `WebFrontEnd_cpu`valore di in `1.5`. Il nome del parametro è preceduto dal nome `WebFrontEnd_` del servizio come procedura consigliata per distinguerlo dai parametri con lo stesso nome che si applicano a servizi diversi.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Aprire il file **Service. YAML** del progetto WebFrontEnd nelle**risorse del servizio**WebFrontEnd. > 

    Si noti che la `resources:` `cpu:` sezione in è impostata su `"[parameters('WebFrontEnd_cpu')]"`. Se il progetto viene compilato per il cloud `'WebFrontEnd_cpu` , il valore per verrà ricavato dal file dei parametri del**cloud** >  **environments** >  **. YAML** e sarà `1.5`. Se è in corso la compilazione del progetto per l'esecuzione in locale, il valore viene ricavato dal file**local** > **Parameters. YAML** degli **ambienti** > e sarà "0,5".

> [!Tip]
> Per impostazione predefinita, il file dei parametri che è un peer del file profile. YAML verrà usato per fornire i valori per il file profile. yaml.
> Ad esempio, environments > Cloud > Parameters. YAML fornisce i valori dei parametri per gli ambienti > Cloud > profile. yaml.
>
> Per eseguire l'override, è possibile aggiungere il codice seguente al file profile. YAML:`parametersFilePath=”relative or full path to the parameters file”` Ad esempio,  o `parametersFilePath=”..\CommonParameters.yaml”`

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

Se si esegue un aggiornamento del codice o un aggiornamento della configurazione (in questo caso si sta eseguendo entrambi), aggiornare l'app Service Fabric mesh in Azure facendo clic con il pulsante destro del mouse su **todolistapp** in Visual Studio e selezionando **pubblica...**

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

Usare l'elenco a discesa **profilo di destinazione** per selezionare il file profile. YAML da usare per questa distribuzione. Si sta aggiornando l'app nel cloud, quindi si seleziona **cloud. YAML** nell'elenco a discesa, che userà il `WebFrontEnd_cpu` valore 1,0 definito in tale file.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Impostare il **percorso** sul percorso usato quando è stata originariamente pubblicata l'app to-do in Azure. In questo articolo è stato usato **Stati Uniti orientali**.

Impostare **gruppo di risorse** sul gruppo di risorse usato quando è stata originariamente pubblicata l'app to-do in Azure.

Impostare **azure container Registry** sul nome del registro contenitori di Azure creato quando è stata originariamente pubblicata l'app to-do in Azure.

Nella finestra di dialogo per la pubblicazione fare clic sul pulsante **Pubblica** per aggiornare l'app to-do in Azure.

Monitorare lo stato di avanzamento dell'aggiornamento selezionando il riquadro **strumenti di Service Fabric** nella finestra **output** di Visual Studio. 

Dopo che l'immagine è stata compilata e ne è stato effettuato il push nel Container Registry di Azure, nell'output verrà visualizzato un collegamento **per lo stato** che è possibile fare clic per monitorare la distribuzione nel portale di Azure.

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