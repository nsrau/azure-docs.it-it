---
title: Creare uno spazio di sviluppo Kubernetes nel cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824639"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Guida introduttiva: Creare uno spazio di sviluppo Kubernetes con Azure Dev Spaces (.NET Core e Visual Studio)

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio.
- Eseguire il debug del codice in esecuzione nel cluster.

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md) o inserire un commento in questa pagina. È anche possibile provare l'[esercitazione](get-started-netcore-visualstudio.md) più dettagliata.

## <a name="prerequisites"></a>Prerequisiti

- Un cluster Kubernetes che esegue Kubernetes 1.9.6 nell'area EastUS, WestEurope o CanadaEast con il routing HTTP dell'applicazione abilitato.

  ![Assicurarsi di abilitare il routing HTTP dell'applicazione.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio 2017 con il carico di lavoro Sviluppo Web installato. Se non è installato, scaricarlo da [qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Configurare Azure Dev Spaces

Installare l'[estensione di Visual Studio per Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster

A questo punto, creare e configurare un progetto per Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

In Visual Studio 2017 creare un nuovo progetto. Attualmente deve trattarsi di un progetto **Applicazione Web ASP.NET Core**. Assegnare al progetto il nome **webfrontend**.

Selezionare il modello **Applicazione Web (MVC)** e assicurarsi che siano selezionati **.NET Core** e **ASP.NET Core 2.0**.

### <a name="create-a-dev-space-in-azure"></a>Creare uno spazio di sviluppo in Azure

Con il progetto appena creato aperto, selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come illustrato di seguito.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Nella finestra di dialogo che viene quindi visualizzata verificare di aver effettuato l'accesso con l'account appropriato e quindi selezionare un cluster esistente.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Per il momento, lasciare l'elenco a discesa **Spazio** impostato su `default`. Selezionare la casella di controllo **Publicly Accessible** (Accessibile pubblicamente) affinché l'app Web sia accessibile tramite un endpoint pubblico.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Fare clic su **OK** per selezionare o creare il cluster.

Se si sceglie un cluster che non è stato configurato per l'uso di Azure Dev Spaces, verrà visualizzato un messaggio che chiede se si vuole eseguire la configurazione.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Scegliere **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Esaminare i file aggiunti al progetto
Mentre si attende che venga creato lo spazio di sviluppo, esaminare i file che sono stati aggiunti al progetto quando si è scelto di usare Azure Dev Spaces.

- È stata aggiunta una cartella denominata `charts` e in tale cartella è stato eseguito lo scaffolding di un [chart Helm](https://docs.helm.sh) per l'applicazione. Questi file vengono usati per distribuire l'applicazione nello spazio di sviluppo.
- `Dockerfile` contiene le informazioni necessarie per creare un pacchetto dell'applicazione nel formato Docker standard.
- `azds.yaml` contiene le informazioni di configurazione necessarie allo spazio di sviluppo, ad esempio per determinare se l'applicazione dovrà essere accessibile tramite un endpoint pubblico.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes
Al termine della creazione dello spazio di sviluppo, è possibile eseguire il debug dell'applicazione. Impostare un punto di interruzione nel codice, ad esempio alla riga 20 del file `HomeController.cs` in cui viene impostata la variabile `Message`. Premere **F5** per avviare il debug. 

Visual Studio comunicherà con lo spazio di sviluppo per compilare e distribuire l'applicazione e quindi aprire un browser con l'app Web in esecuzione. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure. L'indirizzo localhost viene usato perché Azure Dev Spaces crea un tunnel SSH temporaneo per il contenitore eseguito in Azure.

Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione. Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](get-started-netcore-visualstudio.md#call-another-container)