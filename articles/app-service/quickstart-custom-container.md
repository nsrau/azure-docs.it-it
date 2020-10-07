---
title: 'Avvio rapido: Eseguire un contenitore personalizzato nel servizio app'
description: Introduzione all'uso dei contenitori nel servizio app di Azure distribuendo il primo contenitore personalizzato.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 4c95e345255b28ba43e474087cdb80fcab493394
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356418"
---
# <a name="run-a-custom-container-in-azure"></a>Eseguire un contenitore personalizzato in Azure

::: zone pivot="container-windows"
Il [servizio app di Azure](overview.md) offre stack di applicazioni predefiniti in Windows, ad esempio ASP.NET o Node.js, eseguiti in IIS. L'ambiente del contenitore Windows (anteprima) preconfigurato blocca il sistema operativo impedendo l'accesso amministrativo, le installazioni di software, le modifiche alla Global Assembly Cache e così via. Per altre informazioni, vedere [Funzionalità del sistema operativo in Servizio app di Azure](operating-system-functionality.md). Se l'applicazione richiede un accesso superiore a quello consentito dall'ambiente preconfigurato, è possibile distribuire un contenitore Windows personalizzato.

Questa guida di avvio rapido illustra come distribuire un'app ASP.NET in un'immagine Windows in [Docker Hub](https://hub.docker.com/) da Visual Studio ed eseguirla in un contenitore personalizzato in Servizio app di Azure.

> [!NOTE]
> Il servizio app nei contenitori Windows è in anteprima.
>

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- <a href="https://hub.docker.com/" target="_blank">Iscriversi per ottenere un account Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installare Docker per Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Impostare Docker per eseguire contenitori Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installare Visual Studio 2019</a> con i carichi di lavoro **Sviluppo ASP.NET e Web** e **Sviluppo di Azure**. Se Visual Studio 2019 è già installato:

    - Installare gli aggiornamenti più recenti in Visual Studio selezionando **?**  > **Controlla aggiornamenti**.
    - Aggiungere i carichi di lavoro in Visual Studio selezionando **Strumenti** > **Ottieni strumenti e funzionalità**.

## <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

Creare un'app Web ASP.NET seguendo questa procedura:

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto** trovare e selezionare **Applicazione Web ASP.NET (.NET Framework)** per C#, quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare all'applicazione il nome _myfirstazurewebapp_ e quindi selezionare **Crea**.

   ![Configurare il progetto di app Web](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. È possibile distribuire qualsiasi tipo di app Web ASP.NET in Azure. Per questa guida di avvio rapido scegliere il modello **MVC**.

1. Selezionare **Supporto Docker** e verificare che l'autenticazione sia impostata su **Nessuna autenticazione**. Selezionare **Crea**.

   ![Creare un'applicazione Web ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Se il file _Dockerfile_ non viene aperto automaticamente, aprirlo da **Esplora soluzioni**.

1. È necessaria un'[immagine padre supportata](configure-custom-container.md#supported-parent-images). Modificare l'immagine padre sostituendo la riga `FROM` con il codice seguente e salvare il file:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   ![Eseguire l'app in locale](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Eseguire la pubblicazione in Hub Docker

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **myfirstazurewebapp** e scegliere **Pubblica**.

1. Scegliere **Servizio app** e quindi selezionare **Pubblica**.

1. In **Selezionare una destinazione di pubblicazione**  selezionare **Registro Container** e **Docker Hub**, quindi fare clic su **Pubblica**.

   ![Pubblicare dalla pagina di panoramica progetto](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Fornire le credenziali dell'account Docker Hub e selezionare **Salva**.

   Attendere il completamento della distribuzione. La pagina **Pubblica** mostra ora il nome del repository da usare successivamente.

   ![Pubblicare dalla pagina di panoramica progetto](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Copiare il nome del repository da usare successivamente.

## <a name="create-a-windows-container-app"></a>Creare un'app contenitore Windows

1. Accedere al [portale di Azure]( https://portal.azure.com).

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

1. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace cercare **App Web per contenitori** e selezionare **Crea**.

1. In **App Web - Crea** selezionare la propria sottoscrizione e un gruppo in **Gruppo di risorse**. Se necessario, è possibile creare un nuovo gruppo di risorse.

1. Specificare un nome per l'app, ad esempio *win-container-demo*, e selezionare **Windows** per **Sistema operativo**. Selezionare **Avanti: Docker** per continuare.

   ![Creare un'app Web per contenitori](media/quickstart-custom-container/create-web-app-continer.png)

1. Per **Origine immagine** selezionare **Docker Hub** e per **Immagine e tag** immettere il nome del repository copiato in [Eseguire la pubblicazione in Docker Hub](#publish-to-docker-hub).

   ![Configurare l'app Web per contenitori](media/quickstart-custom-container/configure-web-app-continer.png)

    Se si ha un'immagine personalizzata per l'applicazione Web in un'altra posizione, ad esempio in [Registro Azure Container](../container-registry/index.yml) o in qualsiasi altro repository privato, si può configurare tale immagine.

1. Selezionare **Rivedi e crea** e quindi **Crea** e attendere che Azure crei le risorse necessarie.

## <a name="browse-to-the-container-app"></a>Passare all'app contenitore

Al termine dell'operazione di Azure, verrà visualizzata una casella di notifica.

![Distribuzione completata](media/quickstart-custom-container/portal-create-finished.png)

1. Fare clic su **Vai alla risorsa**.

1. Nella panoramica di questa risorsa seguire il collegamento accanto a **URL**.

Si aprirà la nuova pagina del browser illustrata di seguito:

![Pagina di avvio dell'app contenitore Windows](media/quickstart-custom-container/app-starting.png)

Attendere alcuni minuti e riprovare, finché non viene visualizzata la pagina iniziale di ASP.NET predefinita:

![App contenitore Windows in esecuzione](media/quickstart-custom-container/app-running-vs.png)

**Congratulazioni** È ora in esecuzione il primo contenitore Windows personalizzato nel servizio app di Azure.

## <a name="see-container-start-up-logs"></a>Visualizzare i log di avvio del contenitore

Il caricamento del contenitore Windows potrebbe richiedere tempo. Per visualizzare lo stato di avanzamento, passare all'URL seguente sostituendo *\<app_name>* con il nome dell'app.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

I log trasmessi sono simili al seguente:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Eseguire l'aggiornamento e la ridistribuzione in locale

1. In **Esplora soluzioni** di Visual Studio aprire **Visualizzazioni** > **Home** > **Index.cshtml**.

1. Trovare il tag HTML `<div class="jumbotron">` in alto e sostituire l'intero elemento con il codice seguente:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Per la ridistribuzione in Azure, fare clic con il pulsante destro del mouse sul progetto **myfirstazurewebapp** in **Esplora soluzioni** e scegliere **Pubblica**.

1. Nella pagina di pubblicazione selezionare **Pubblica** e attendere il completamento della pubblicazione.

1. Per indicare al servizio a di eseguire il pull della nuova immagine da Hub Docker, riavviare l'app. Di nuovo nella pagina dell'app nel portale fare clic su **Riavvia** > **Sì**.

   ![Riavviare l'app Web in Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Passare all'app contenitore](#browse-to-the-container-app) di nuovo. Quando si aggiorna la pagina Web, l'app dovrebbe prima tornare alla pagina "Avvio" e quindi visualizzare la pagina Web aggiornata di nuovo dopo alcuni minuti.

![App Web aggiornata in Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione al contenitore Windows in Azure](tutorial-custom-container.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare il contenitore personalizzato](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
Il servizio app in Linux fornisce stack di applicazioni predefiniti in Linux con il supporto per linguaggi quali .NET, PHP, Node.js e altri ancora. È anche possibile usare un'immagine Docker personalizzata per eseguire l'app Web in uno stack di applicazioni non ancora definito in Azure. Questo argomento di avvio rapido illustra come distribuire un'immagine da un [Registro Azure Container](../container-registry/index.yml) al servizio app.

## <a name="prerequisites"></a>Prerequisiti

* Un [account Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Estensione Servizio app di Azure per VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). È possibile usare questa estensione per creare, gestire e distribuire app Web Linux nella piattaforma distribuita come servizio (PaaS) di Azure.
* [Estensione Docker per VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). È possibile usare questa estensione per semplificare la gestione delle immagini e dei comandi Docker locali e per distribuire in Azure immagini di app compilate.

## <a name="create-an-image"></a>Creare un'immagine

Per completare questa guida di avvio rapido, è necessaria un'immagine dell'app Web appropriata archiviata in un [Registro Azure Container](../container-registry/index.yml). Seguire le istruzioni contenute nell'articolo [Avvio rapido: Creare un registro contenitori privato con il portale di Azure](../container-registry/container-registry-get-started-portal.md), ma usare l'immagine `mcr.microsoft.com/azuredocs/go` anziché `hello-world`. Per riferimento, il [Dockerfile di esempio è disponibile nel repository di esempi di Azure](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Assicurarsi di impostare l'opzione **Utente amministratore** su **Abilita** durante la creazione del registro contenitori. È anche possibile impostare questa opzione dalla sezione **Chiavi di accesso** della pagina del registro nel portale di Azure. Questa impostazione è obbligatoria per l'accesso al servizio app.

## <a name="sign-in"></a>Accedi

Avviare quindi VS Code e accedere al proprio account Azure usando l'estensione Servizio app di Azure. A tale scopo, selezionare il logo di Azure sulla barra delle attività, passare allo strumento di esplorazione **SERVIZIO APP**, selezionare **Accedi ad Azure** e seguire le istruzioni.

![Accedere ad Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Verificare i prerequisiti

È ora possibile verificare che tutti i prerequisiti siano installati e configurati correttamente.

In VS Code dovrebbero essere visualizzati l'indirizzo di posta elettronica di Azure sulla barra di stato e la sottoscrizione nello strumento di esplorazione **SERVIZIO APP**.

Verificare quindi che Docker sia installato e in esecuzione. Il comando seguente visualizzerà la versione di Docker, se è in esecuzione.

```bash
docker --version
```

Assicurarsi infine che Registro Azure Container sia connesso. A tale scopo, selezionare il logo Docker sulla barra delle attività e quindi passare a **REGISTRIES** (REGISTRI).

![Screenshot che mostra il valore Registri con il nodo Azure espanso e un file con estensione io.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Distribuire l'immagine nel Servizio app di Azure

Ora che tutto è configurato, è possibile distribuire l'immagine nel [Servizio app di Azure](https://azure.microsoft.com/services/app-service/) direttamente dallo strumento di esplorazione dell'estensione Docker.

Individuare l'immagine nel nodo **Registries** (Registri) nello strumento di esplorazione **DOCKER** ed espanderla per visualizzarne i tag. Fare clic con il pulsante destro del mouse su un tag e quindi scegliere **Deploy Image to Azure App Service** (Distribuisci immagine nel Servizio app di Azure).

Da qui, seguire le istruzioni per scegliere una sottoscrizione, un nome di app univoco a livello globale, un gruppo di risorse e un piano di servizio app. Scegliere **B1 Basic** come piano tariffario e un'area.

Dopo la distribuzione, l'app è disponibile all'indirizzo `http://<app name>.azurewebsites.net`.

Un **gruppo di risorse** è una raccolta denominata di tutte le risorse dell'applicazione in Azure. Può contenere ad esempio un riferimento a un sito Web, a un database e a una funzione di Azure.

Un **piano di servizio app** definisce le risorse fisiche che verranno usate per ospitare il sito Web. Questa guida di avvio rapido usa un piano di hosting **Basic** nell'infrastruttura **Linux**, il che significa che il sito verrà ospitato in un computer Linux insieme ad altri siti Web. Se si inizia con il piano **Basic**, è possibile usare il portale di Azure per aumentare le prestazioni in modo che il proprio sia l'unico sito in esecuzione su un computer.

## <a name="browse-the-website"></a>Esplorare il sito Web

Durante la distribuzione viene visualizzato il pannello **Output** che indica lo stato dell'operazione. Al termine dell'operazione, trovare l'app creata nello strumento di esplorazione **SERVIZIO APP**,fare clic su di essa con il pulsante destro del mouse e quindi scegliere **Browse Website** (Esplora sito Web) per aprire il sito nel browser.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Passaggi successivi

L'argomento avvio rapido è stato completato.

Vedere ora le altre estensioni di Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

In alternativa, è possibile ottenere tutte queste soluzioni installando il pacchetto di estensioni [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

Consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare il contenitore personalizzato](configure-custom-container.md)

::: zone-end