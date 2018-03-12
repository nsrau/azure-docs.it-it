---
title: API RESTful con CORS nel servizio app di Azure | Microsoft Docs
description: Informazioni su come usare il servizio app di Azure per ospitare le API RESTful con supporto CORS.
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 7420e92bc929808f074e9be00dfbcb7d8476654a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="host-a-restful-api-with-cors-in-azure-app-service"></a>Ospitare un'API RESTful con CORS nel servizio app di Azure

[Servizio app di Azure](app-service-web-overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Il servizio app mette anche a disposizione il supporto integrato per la [condivisione di risorse tra le origini (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) per le API RESTful. Questa esercitazione illustra come distribuire un'app per le API ASP.NET Core nel servizio app con supporto per CORS. L'app verrà configurata usando gli strumenti da riga di comando e distribuita tramite Git. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare risorse del servizio app usando l'interfaccia della riga di comando di Azure
> * Distribuire un'API RESTful in Azure con Git
> * Abilitare il supporto per CORS del servizio app

I passaggi illustrati in questa esercitazione possono essere eseguiti in macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/).
* [Installare .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Creare un'app ASP.NET Core locale

In questo passaggio si configura il progetto ASP.NET Core locale. Il servizio app supporta lo stesso flusso di lavoro per le API scritte in altri linguaggi.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.  

Eseguire il comando seguente per clonare l'archivio di esempio. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Questo repository contiene un'app creata in base all'esercitazione [Pagine della Guida dell'API Web ASP.NET Core con Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Usa un generatore di Swagger per distribuire l'[interfaccia utente Swagger](https://swagger.io/swagger-ui/) e l'endpoint JSON Swagger.

### <a name="run-the-application"></a>Eseguire l'applicazione

Usare i comandi seguenti per installare i pacchetti necessari, eseguire le migrazioni dei database e avviare l'applicazione.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Passare a `http://localhost:5000/swagger` in un browser per provare a usare l'interfaccia utente Swagger.

![API ASP.NET Core in esecuzione in locale](./media/app-service-web-tutorial-rest-api/local-run.png)

Passare a `http://localhost:5000/api/todo` e visualizzare un elenco di elementi ToDo JSON.

Passare a `http://localhost:5000` e provare a usare l'app browser. L'app browser verrà successivamente puntata a un'API remota del servizio app per testare la funzionalità CORS. Il codice per l'app browser è disponibile nella directory _wwwroot_ del repository.

Per arrestare ASP.NET Core in qualsiasi momento, premere `Ctrl+C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio si distribuisce l'applicazione .NET Core connessa al database SQL nel servizio app.

### <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare a `http://<app_name>.azurewebsites.net/swagger` in un browser e provare a usare l'interfaccia utente Swagger.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/app-service-web-tutorial-rest-api/azure-run.png)

Passare a `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` per visualizzare il file _swagger.json_ per l'API distribuita.

Passare a `http://<app_name>.azurewebsites.net/api/todo` per visualizzare l'API distribuita in funzione.

## <a name="add-cors-functionality"></a>Aggiungere funzionalità CORS

Abilitare ora il supporto integrato per CORS nel servizio app per l'API.

### <a name="test-cors-in-sample-app"></a>Testare CORS nell'app di esempio

Nel repository locale aprire _wwwroot/index.html_.

Nella riga 51 impostare la variabile `apiEndpoint` sull'URL dell'API distribuita (`http://<app_name>.azurewebsites.net`). Sostituire _\<appname>_ con il nome dell'app nel servizio app.

Nella finestra del terminale locale eseguire di nuovo l'app di esempio.

```bash
dotnet run
```

Passare all'app browser all'indirizzo `http://localhost:5000`. Aprire la finestra degli strumenti di sviluppo nel browser (`Ctrl`+`Shift`+`i` in Chrome per Windows) ed esaminare la scheda **Console**. Verrà visualizzato il messaggio di errore `No 'Access-Control-Allow-Origin' header is present on the requested resource`

![Errore CORS nel client browser](./media/app-service-web-tutorial-rest-api/cors-error.png)

A causa della mancata corrispondenza di dominio tra l'app browser (`http://localhost:5000`) e la risorsa remota (`http://<app_name>.azurewebsites.net`) e dato che l'API nel servizio app non invia l'intestazione `Access-Control-Allow-Origin`, il browser ha impedito il caricamento di contenuto tra domini nell'app browser.

Nell'ambiente di produzione, l'app browser avrebbe un URL pubblico anziché l'URL localhost, ma la modalità di abilitazione di CORS per un URL localhost e per un URL pubblico è la stessa.

### <a name="enable-cors"></a>Abilitare CORS 

In Cloud Shell abilitare CORS per l'URL del client usando il comando [`az resource update`](/cli/azure/resource#az_resource_update). Sostituire il segnaposto _&lt;appname>_ .

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

È possibile impostare più di un URL client in `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`). È anche possibile abilitare tutti gli URL client con `"['*']"`.

### <a name="test-cors-again"></a>Testare di nuovo CORS

Aggiornare l'app browser all'indirizzo `http://localhost:5000`. Il messaggio di errore nella finestra **Console** non viene più visualizzato ed è possibile esaminare i dati dell'API distribuita e interagirvi. L'API remota supporta ora CORS per l'app browser in esecuzione in locale. 

![Supporto per CORS abilitato nel client browser](./media/app-service-web-tutorial-rest-api/cors-success.png)

L'API viene ora eseguita nel servizio app di Azure con il supporto per CORS.

## <a name="app-service-cors-vs-your-cors"></a>CORS del servizio app e CORS personalizzato

È possibile usare le proprie utilità CORS anziché le utilità CORS del servizio app per una maggiore flessibilità. Può essere ad esempio opportuno specificare origini consentite differenti per route e metodi diversi. Poiché CORS del servizio app consente di specificare un set di origini accettate per tutte le route e i metodi dell'API, si vuole usare il proprio codice CORS. Per informazioni sul modo in cui ASP.NET Core esegue questa operazione, vedere [Abilitare le richieste tra le origini (CORS)](/aspnet/core/security/cors).

> [!NOTE]
> Non provare a usare il codice CORS del servizio app e il proprio codice CORS insieme. Se usati insieme, il codice CORS del servizio app ha la precedenza e il proprio codice CORS non avrà alcun effetto.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare risorse del servizio app usando l'interfaccia della riga di comando di Azure
> * Distribuire un'API RESTful in Azure con Git
> * Abilitare il supporto per CORS del servizio app

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)
