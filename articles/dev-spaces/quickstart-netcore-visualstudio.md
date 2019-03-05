---
title: Creare uno spazio di sviluppo Kubernetes nel cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 89fd64be0ea7b5b02ed446f2ee6d4c6a9c69dd0e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819685"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Guida introduttiva: Creare uno spazio di sviluppo Kubernetes con Azure Dev Spaces (.NET Core e Visual Studio)

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio.
- Eseguire il debug del codice in esecuzione nel cluster.

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md) o inserire un commento in questa pagina. È anche possibile provare l'[esercitazione](get-started-netcore-visualstudio.md) più dettagliata.

## <a name="prerequisites"></a>Prerequisiti

- Un cluster Kubernetes che esegue Kubernetes 1.9.6 o versione successiva nell'area Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti occidentali 2, Europa occidentale, Asia sud-orientale, Canada centrale o Canada orientale.

- Visual Studio 2017 con il carico di lavoro Sviluppo Web installato. Se non è installato, scaricarlo da [qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Configurare Azure Dev Spaces

Installare [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="connect-to-a-cluster"></a>Connettersi a un cluster

A questo punto, creare e configurare un progetto per Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

In Visual Studio 2017 creare un nuovo progetto. Attualmente deve trattarsi di un progetto **Applicazione Web ASP.NET Core**. Assegnare al progetto il nome **webfrontend**.

Selezionare il modello **Applicazione Web (MVC)** e assicurarsi che siano selezionati **.NET Core** e **ASP.NET Core 2.0**.

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Abilitare Dev Spaces per un cluster servizio Azure Kubernetes

Con il progetto appena creato, selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come mostrato di seguito.

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
- `azds.yaml` contiene la configurazione in fase di sviluppo necessaria per lo spazio di sviluppo.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes
Al termine della creazione dello spazio di sviluppo, è possibile eseguire il debug dell'applicazione. Impostare un punto di interruzione nel codice, ad esempio alla riga 20 del file `HomeController.cs` in cui viene impostata la variabile `Message`. Premere **F5** per avviare il debug. 

Visual Studio comunicherà con lo spazio di sviluppo per compilare e distribuire l'applicazione e quindi aprire un browser con l'app Web in esecuzione. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure. L'indirizzo localhost viene usato perché Azure Dev Spaces crea un tunnel SSH temporaneo per il contenitore in esecuzione in servizio Azure Kubernetes.

Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione. Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.


## <a name="iteratively-develop-code"></a>Sviluppare in modo iterativo il codice

Azure Dev Spaces consente non solo di eseguire codice in Kubernetes, ma anche di visualizzare in modo rapido e iterativo l'applicazione delle modifiche apportate al codice in un ambiente Kubernetes nel cloud.

### <a name="update-a-content-file"></a>Aggiornare un file di contenuto
1. Individuare il file `./Views/Home/Index.cshtml` e apportare una modifica al codice HTML. Ad esempio, modificare la riga 70 contenente `<h2>Application uses</h2>` come segue: `<h2>Hello k8s in Azure!</h2>`
1. Salvare il file.
1. Passare al browser e aggiornare la pagina. Nella pagina Web verrà visualizzato il codice HTML aggiornato.

Che cosa è successo? Poiché le modifiche apportate ai file di contenuto, come HTML e CSS, non richiedono la ricompilazione in un'app Web .NET Core, una sessione F5 attiva sincronizza automaticamente tutti i file di contenuto modificati nel contenitore in esecuzione in servizio Azure Kubernetes, permettendo di visualizzare le modifiche di contenuto immediatamente.

### <a name="update-a-code-file"></a>Aggiornare un file di codice
L'aggiornamento di file di codice richiede alcune operazioni aggiuntive, perché un'app .NET Core deve essere ricompilata e deve generare i file binari dell'applicazione aggiornati.

1. Arrestare il debugger in Visual Studio.
1. Aprire il file di codice denominato `Controllers/HomeController.cs` e modificare il messaggio che verrà visualizzato nella pagina About: `ViewData["Message"] = "Your application description page.";`
1. Salvare il file.
1. Premere **F5** per avviare di nuovo il debug. 

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, operazione che spesso richiede una notevole quantità di tempo, Azure Dev Spaces ricompilerà in modo incrementale il codice nel contenitore esistente in modo da velocizzare il ciclo di modifica/debug.

Aggiornare l'app Web nel browser e passare alla pagina delle informazioni. Nell'interfaccia utente verrà visualizzato il messaggio personalizzato.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](multi-service-netcore-visualstudio.md)
