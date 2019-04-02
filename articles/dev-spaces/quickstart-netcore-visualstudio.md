---
title: Sviluppare con .NET Core nel servizio Azure Kubernetes con Azure Dev Spaces e Visual Studio 2017
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: jeconnoc
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 0ae2b264e689270743bc8e4aa5024a4b99eb6626
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418840"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-with-azure-dev-spaces-visual-studio-2017"></a>Guida introduttiva: Sviluppare con .NET Core in Kubernetes con Azure Dev Spaces (Visual Studio 2017)

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio 2017.
- Eseguire il debug del codice in esecuzione nel cluster con Visual Studio 2017.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free).
- Visual Studio 2017 in Windows con il carico di lavoro Sviluppo Web installato. Se non è installato, scaricarlo da [qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools) installato.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Per creare un cluster:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Selezionare *+Crea una risorsa > Servizio Kubernetes*. 
1. Compilare i campi _Sottoscrizione_, _Gruppo di risorse_, _Nome del cluster Kubernetes_, _Area_, _Versione di Kubernetes_ e _Prefisso nome DNS_.

    ![Creare il servizio Azure Kubernetes nel portale di Azure](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Fare clic su *Rivedi e crea*.
1. Fare clic su *Crea*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Passare al cluster del servizio Azure Kubernetes nel portale di Azure e fare clic su *Dev Spaces*. Impostare *Abilita Dev Spaces* su *Sì* e fare clic su *Salva*.

![Abilitare Dev Spaces nel portale di Azure](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Creare una nuova app Web ASP.NET

1. Aprire Visual Studio 2017.
1. Creare un nuovo progetto.
1. Scegliere *Applicazione Web ASP.NET Core* e assegnare al progetto il nome *webfrontend*.
1. Fare clic su *OK*.
1. Quando richiesto, scegliere *Applicazione Web (Model-View-Controller)* per il modello.
1. Selezionare *.NET Core* e *ASP.NET Core 2.0* nella parte superiore.
1. Fare clic su *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Connettere il progetto al proprio spazio di Dev Spaces

Nel progetto selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come mostrato di seguito.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Nella finestra di dialogo Azure Dev Spaces selezionare *Sottoscrizione* e *Cluster del servizio Azure Kubernetes*. Lasciare l'opzione *Spazio* impostata sul valore *predefinito*e abilitare la casella di controllo *Accessibile pubblicamente*. Fare clic su *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Questa procedura distribuisce il servizio nello spazio di Dev Spaces *predefinito* con un URL accessibile pubblicamente. Se si sceglie un cluster che non è stato configurato per l'uso di Azure Dev Spaces, verrà visualizzato un messaggio che chiede se si vuole eseguire la configurazione. Fare clic su *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

L'URL pubblico del servizio in esecuzione nello spazio *predefinito* è visualizzato nella finestra *Output*:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Nell'esempio precedente l'URL pubblico è http://webfrontend.1234567890abcdef1234.eus.azds.io/. Passare all'URL pubblico del servizio e interagire con il servizio in esecuzione nel proprio spazio.

## <a name="update-code"></a>Aggiornare il codice

Se Visual Studio 2017 è ancora connesso al proprio spazio, fare clic sul pulsante Arresta. Cambiare la riga 20 di `Controllers/HomeController.cs` in:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Salvare le modifiche e avviare il servizio scegliendo **Azure Dev Spaces** dal menu a discesa delle impostazioni di avvio. Aprire l'URL pubblico del servizio in un browser e fare clic su *Informazioni*. Notare che viene visualizzato il messaggio aggiornato.

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, Azure Dev Spaces ricompila in modo incrementale il codice nel contenitore esistente in modo da velocizzare il ciclo di modifica/debug.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Impostazione e uso di punti di interruzione per il debug

Se Visual Studio 2017 è ancora connesso al proprio spazio, fare clic sul pulsante Arresta. Aprire `Controllers/HomeController.cs` e fare clic in un punto della riga 20 per posizionarvi il cursore. Per impostare un punto di interruzione premere *F9* oppure fare clic su *Debug* e quindi su *Attiva/Disattiva punto di interruzione*. Per avviare il servizio in modalità debug nello spazio di Dev Spaces, premere *F5* oppure fare clic su *Debug* e quindi su *Avvia debug*.

Aprire il servizio in un browser e notare che non vengono visualizzati messaggi. Tornare in Visual Studio 2017 e notare che la riga 20 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio in corrispondenza della riga 20. Per riprendere il servizio, premere *F5* oppure fare clic su *Debug* e quindi su *Continua*. Tornare nel browser e notare che ora il messaggio è visualizzato.

Durante l'esecuzione del servizio in Kubernetes con un debugger collegato, si ha accesso completo alle informazioni di debug, ad esempio su stack di chiamate, variabili locali ed eccezioni.

Rimuovere il punto di interruzione posizionando il cursore sulla riga 20 di `Controllers/HomeController.cs` e premendo *F9*.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

Passare al gruppo di risorse nel portale di Azure e fare clic su *Elimina gruppo di risorse*. In alternativa, è possibile usare il comando [az aks delete](/cli/azure/aks#az-aks-delete):

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](multi-service-netcore-visualstudio.md)
