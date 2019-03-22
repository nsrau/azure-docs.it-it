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
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806735"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Esercitazione: Informazioni su come aggiornare un'applicazione Service Fabric tramite Visual Studio

Questa esercitazione è la quarta parte di una serie e mostra come aggiornare un'applicazione Azure Service Fabric Mesh direttamente da Visual Studio. L'aggiornamento includerà sia un aggiornamento del codice sia un aggiornamento della configurazione. Si noterà che i passaggi per l'aggiornamento e la pubblicazione da Visual Studio siano uguali.

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

Questo articolo illustra come aggiornare un microservizio all'interno di un'applicazione. In questo esempio, si modificherà il `WebFrontEnd` servizio per visualizzare una categoria di attività e aumentare la quantità di CPU viene assegnato. Verrà quindi eseguito l'aggiornamento del servizio distribuito.

## <a name="modify-the-config"></a>Modificare la configurazione

Quando si crea un'app di Service Fabric Mesh, Visual studio aggiunge un **parameters.yaml** file per ogni ambiente di distribuzione (cloud e locale). In questi file, è possibile definire i parametri e i relativi valori che è possibile fare riferimento dai tuoi file *.yaml Mesh, ad esempio service.yaml o network.yaml.  Visual Studio offre alcune variabili, ad esempio la quantità di CPU può usare il servizio.

Verrà aggiornato il `WebFrontEnd_cpu` parametro per aggiornare le risorse della cpu per `1.5` in previsione che le **WebFrontEnd** servizio verrà utilizzato più frequentemente.

1. Nel **todolistapp** del progetto, sotto **ambienti** > **Cloud**, aprire il **parameters.yaml** file. Modificare il `WebFrontEnd_cpu`, valore `1.5`. Il nome del parametro è preceduto dal nome del servizio `WebFrontEnd_` come procedura consigliata per distinguerlo dai parametri lo stesso nome che si applicano a servizi diversi.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Aprire il **WebFrontEnd** del progetto **service.yaml** file sotto **WebFrontEnd** > **le risorse del servizio**.

    Si noti che nelle `resources:` sezione `cpu:` è impostata su `"[parameters('WebFrontEnd_cpu')]"`. Se il progetto viene compilato per il cloud, il valore per `'WebFrontEnd_cpu` saranno presi dal **ambienti** > **Cloud** > **parameters.yaml** file e saranno `1.5`. Se il progetto viene compilato per l'esecuzione in locale, il valore verrà copiato il **ambienti** > **locale** > **parameters.yaml** file, e sarà '0,5'.

> [!Tip]
> Per impostazione predefinita, il file dei parametri che è un peer del file profile.yaml da utilizzare per fornire i valori per tale file profile.yaml.
> Ad esempio, ambienti > Cloud > parameters.yaml fornisce i valori dei parametri per gli ambienti > Cloud > profile.yaml.
>
> È possibile ignorare questa aggiungendo il codice seguente al file profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` Ad esempio,  o `parametersFilePath=”..\CommonParameters.yaml”`

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

Se si sta apportando un aggiornamento del codice o un aggiornamento della configurazione (in questo caso stiamo facendo entrambi), aggiornare l'app della rete di Service Fabric in Azure facendo clic su **todolistapp** in Visual Studio e quindi selezionare **pubblica...**

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

Usare la **profilo di destinazione** elenco a discesa per selezionare il file profile.yaml da usare per la distribuzione. Verrà aggiornata l'app nel cloud, sarà necessario selezionare la **cloud.yaml** nell'elenco a discesa, che userà il `WebFrontEnd_cpu` valore pari a 1,0 definita nel file.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Impostare il **posizione** al percorso usato quando è stato pubblicato originariamente le app di elenco attività in Azure. In questo articolo è stato usato **Stati Uniti orientali**.

Impostare **gruppo di risorse** al gruppo di risorse usato quando è stato pubblicato originariamente le app di elenco attività in Azure.

Impostare **registro contenitori di Azure** per il nome del registro contenitori di azure creato quando è stato pubblicato originariamente le app di elenco attività in Azure.

Nella finestra di dialogo per la pubblicazione fare clic sul pulsante **Pubblica** per aggiornare l'app to-do in Azure.

Monitorare lo stato di avanzamento dell'aggiornamento, selezionando il **strumenti di Service Fabric** riquadro di Visual Studio **Output** finestra. 

Dopo che l'immagine viene compilata ed eseguito il push in Registro contenitori di Azure, un **per lo stato** collegamento verrà visualizzato l'output indica che è possibile fare clic per monitorare la distribuzione nel portale di Azure.

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