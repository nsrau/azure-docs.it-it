---
title: 'Creare uno spazio di sviluppo Kubernetes: Visual Studio e .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure, devx-track-azurecli, devx-track-csharp
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Questa esercitazione illustra come usare Azure Dev Spaces e Visual Studio per eseguire il debug e l'iterazione rapida di un'applicazione .NET Core nel servizio Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: 78ad3c643b839d2a0901ac2c1d930d73b718cd8d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999258"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Creare uno spazio di sviluppo Kubernetes: Visual Studio e .NET Core con Azure Dev Spaces

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio.
- Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
- Sviluppare e testare il codice in modo produttivo in un ambiente di team.

> [!NOTE]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md).

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
Azure Dev Spaces richiede un'installazione minima nel computer locale. La maggior parte della configurazione dello spazio di sviluppo viene archiviata nel cloud ed è condivisibile con altri utenti. Per iniziare, scaricare ed eseguire l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure
Accedere ad Azure. Digitare il comando seguente in una finestra del terminale:

```azurecli
az login
```

> [!NOTE]
> Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Se sono disponibili più sottoscrizioni di Azure...
È possibile visualizzare le sottoscrizioni eseguendo: 

```azurecli
az account list --output table
```

Individuare la sottoscrizione in cui il valore di *IsDefault* è *True*.
Se non si tratta della sottoscrizione che si vuole usare, è possibile modificare la sottoscrizione predefinita:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Creare un cluster Kubernetes abilitato per Azure Dev Spaces

Al prompt dei comandi creare il gruppo di risorse in un'[area che supporti Azure Dev Spaces][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Usare il comando seguente per creare un cluster Kubernetes:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

La creazione del cluster richiede alcuni minuti.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurare il cluster servizio Azure Kubernetes per l'uso di Azure Dev Spaces

Immettere il comando seguente dell'interfaccia della riga di comando di Azure, usando il gruppo di risorse contenente il cluster servizio Azure Kubernetes e il nome del cluster servizio Azure Kubernetes. Il comando configura il cluster con il supporto per gli spazi Azure Dev Spaces.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Il processo di configurazione di Azure Dev Spaces rimuoverà lo spazio dei nomi `azds` nel cluster, se presente.

## <a name="get-the-visual-studio-tools"></a>Ottenere gli strumenti di Visual Studio
Installare l'ultima versione di [Visual Studio 2019](https://www.visualstudio.com/vs/) in Windows con il carico di lavoro Sviluppo di Azure.

## <a name="create-a-web-app-running-in-a-container"></a>Creare un'app Web in esecuzione in un contenitore

In questa sezione si creerà un'app Web ASP.NET Core e la si eseguirà in un contenitore in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

In Visual Studio creare un nuovo progetto. Attualmente deve trattarsi di un progetto **Applicazione Web ASP.NET Core**. Assegnare al progetto il nome "**webfrontend**".

![La finestra di dialogo "Nuovo progetto" mostra la creazione di un'applicazione Web C sharp denominata "webfrontend" nella posizione C:\Source\Repos. L'elenco a discesa "Soluzione" è impostato su "Crea nuova soluzione". La casella di controllo "Crea directory per soluzione" è selezionata.](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selezionare il modello **Applicazione Web (MVC)** e assicurarsi che nei due elenchi a discesa nella parte superiore della finestra di dialogo siano selezionati **.NET Core** e **ASP.NET Core 2.0**. Fare clic su **OK** per creare il progetto.

![Nella finestra di dialogo "Nuova Applicazione Web ASP.NET Core" due elenchi a discesa mostrano ".NET Core" e "ASP.NET Core 2.0". Sotto gli elenchi a discesa è riportata una matrice di pulsanti per il modello di progetto in cui è selezionato il modello "Applicazione Web (MVC)". La casella di controllo "Abilita supporto Docker" non è selezionata.](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Abilitare Dev Spaces per un cluster servizio Azure Kubernetes

Con il progetto appena creato, selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come mostrato di seguito.

![L'elenco a discesa è disponibile nella parte superiore di una finestra contrassegnata con l'etichetta Microsoft Visual Studio Int Preview. L'opzione "Azure Dev Spaces" è selezionata.](media/get-started-netcore-visualstudio/LaunchSettings.png)

Nella finestra di dialogo che viene quindi visualizzata verificare di aver effettuato l'accesso con l'account appropriato e quindi selezionare un cluster Kubernetes esistente.

![La finestra di dialogo Azure Dev Spaces contiene le tre caselle "Sottoscrizione" "Cluster servizio Azure Kubernetes" e "Spazio".](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Per il momento, nell'elenco a discesa **Spazio** lasciare l'impostazione predefinita `default`. Questa opzione verrà approfondita in seguito. Selezionare la casella di controllo **Publicly Accessible** (Accessibile pubblicamente) affinché l'app Web sia accessibile tramite un endpoint pubblico. Questa impostazione non è obbligatoria, ma sarà utile per illustrare alcuni concetti più avanti in questa procedura dettagliata. In ogni caso sarà comunque possibile eseguire il debug del sito Web con Visual Studio.

![La casella di controllo Accessibile pubblicamente è selezionata.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Fare clic su **OK** per selezionare o creare il cluster.

Se si sceglie un cluster che non è stato abilitato per l'uso di Azure Dev Spaces, verrà visualizzato un messaggio che chiede se si vuole eseguire la configurazione.

![Il testo del messaggio sarà simile a questo: "Aggiungi risorsa di Azure Dev Spaces. Per usare Azure Dev Spaces è necessario configurare il cluster AKS selezionato. Continuare?" Saranno presenti i pulsanti "OK" e "Annulla".](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Scegliere **OK**.

> [!IMPORTANT]
> Il processo di configurazione di Azure Dev Spaces rimuoverà lo spazio dei nomi `azds` nel cluster, se presente.

 Verrà avviata un'attività in background a tale scopo. Il completamento dell'attività richiederà alcuni minuti. Per verificare se è ancora in corso la creazione, passare il puntatore sull'icona **Attività in background** nell'angolo inferiore sinistro della barra di stato, come illustrato nell'immagine seguente.

![La finestra popup che viene visualizzata al passaggio del mouse indica che è in corso la creazione dell'AKS nel grippo di risorse.](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!NOTE]
> Finché non viene completata la creazione dello spazio di sviluppo, non è possibile eseguire il debug dell'applicazione.

### <a name="look-at-the-files-added-to-project"></a>Esaminare i file aggiunti al progetto
Mentre si attende che venga creato lo spazio di sviluppo, esaminare i file che sono stati aggiunti al progetto quando si è scelto di usare uno spazio di sviluppo.

Per prima cosa, è possibile osservare che è stata aggiunta una cartella denominata `charts` e che in tale cartella è stato eseguito lo scaffolding di un [chart Helm](https://docs.helm.sh) per l'applicazione. Questi file vengono usati per distribuire l'applicazione nello spazio di sviluppo.

Si noterà che è stato aggiunto un file denominato `Dockerfile`, che contiene le informazioni necessarie per creare un pacchetto dell'applicazione nel formato Docker standard.

Si noterà infine in file denominato `azds.yaml` che contiene la configurazione in fase di sviluppo necessaria per lo spazio di sviluppo.

![Nell'ambito della finestra Esplora soluzioni, nella soluzione "webfrontend" viene visualizzato il file "azds.yaml".](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes
Al termine della creazione dello spazio di sviluppo, è possibile eseguire il debug dell'applicazione. Impostare un punto di interruzione nel codice, ad esempio alla riga 20 del file `HomeController.cs` in cui viene impostata la variabile `Message`. Premere **F5** per avviare il debug. 

Visual Studio comunicherà con lo spazio di sviluppo per compilare e distribuire l'applicazione e quindi aprire un browser con l'app Web in esecuzione. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure. L'indirizzo localhost viene usato perché Azure Dev Spaces crea un tunnel SSH temporaneo per il contenitore in esecuzione in servizio Azure Kubernetes.

Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione. Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

## <a name="iteratively-develop-code"></a>Sviluppare in modo iterativo il codice

Azure Dev Spaces consente non solo di eseguire codice in Kubernetes, ma anche di visualizzare in modo rapido e iterativo l'applicazione delle modifiche apportate al codice in un ambiente Kubernetes nel cloud.

### <a name="update-a-content-file"></a>Aggiornare un file di contenuto


1. Individuare il file `./Views/Home/Index.cshtml` e apportare una modifica al codice HTML. Ad esempio, modificare la [riga 73 contenente `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) come segue: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Salvare il file.
3. Passare al browser e aggiornare la pagina. Nella pagina Web verrà visualizzato il codice HTML aggiornato.

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

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
