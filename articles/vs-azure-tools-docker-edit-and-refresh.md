---
title: Debug di applicazioni in un contenitore Docker locale | Documentazione Microsoft
description: "Informazioni su come modificare un&quot;app in esecuzione in un contenitore Docker locale, aggiornare il contenitore tramite la funzionalità di modifica e aggiornamento e impostare i punti di interruzione del debug"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: fcd58736d8915a61683a416fb9bf3892ba7b7bd8
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="debugging-apps-in-a-local-docker-container"></a>Debug delle applicazioni in un contenitore Docker locale
## <a name="overview"></a>Panoramica
Visual Studio Tools per Docker consente di sviluppare e convalidare l'applicazione in locale in un contenitore Docker. di Linux.
Non è necessario riavviare il contenitore ogni volta che si esegue una modifica del codice.
Questo articolo illustra come usare la funzionalità di modifica e aggiornamento per avviare un'app Web ASP.NET Core in un contenitore Docker locale, apportare le modifiche necessarie e quindi aggiornare il browser per visualizzare tali modifiche.
Illustra inoltre come impostare i punti di interruzione per il debug.

> [!NOTE]
> Il supporto del contenitore di Windows sarà disponibile nelle versioni future
>
>

## <a name="prerequisites"></a>Prerequisiti
È necessario che siano installati gli strumenti seguenti.

* [Ultima versione di Visual Studio](https://www.visualstudio.com/downloads/)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Per eseguire i contenitori Docker in locale, è necessario un client di Docker locale.
È possibile usare la [casella degli strumenti di Docker](https://www.docker.com/products/docker-toolbox), che richiede la disabilitazione di Hyper-V, o in alternativa [Docker per Windows](https://www.docker.com/get-docker), che usa Hyper-V e richiede Windows 10.

Nella casella degli strumenti di Docker è necessario [configurare il client di Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Creare un'app Web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Aggiungere il supporto di Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Modificare il codice e aggiornarlo
Per eseguire rapidamente l'iterazione delle modifiche, è possibile avviare l'applicazione in un contenitore e continuare ad apportare modifiche, visualizzandole come si farebbe con IIS Express.

1. Impostare la configurazione della soluzione su `Debug` e premere **&lt;CTRL + F5>** per creare l'immagine Docker ed eseguirla localmente.

    Una volta che l'immagine del contenitore è stata compilata ed è in esecuzione in un contenitore Docker, Visual Studio avvierà l'App Web nel browser predefinito.
    Se si usa il browser Microsoft Edge o se si verificano problemi, vedere la sezione relativa alla [risoluzione dei problemi](vs-azure-tools-docker-troubleshooting-docker-errors.md) .
2. Passare alla pagina About, da dove verranno apportate le modifiche.
3. Tornare a Visual Studio e aprire `Views\Home\About.cshtml`.
4. Aggiungere il contenuto HTML seguente alla fine del file e salvare le modifiche.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Visualizzare la finestra di output e, quando viene completata la compilazione di .NET e vengono visualizzate queste righe, tornare al browser e aggiornare la pagina About.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Le modifiche sono state applicate.

## <a name="4-debug-with-breakpoints"></a>4. Eseguire il debug con punti di interruzione
Spesso è necessario analizzare le modifiche in modo più approfondito, sfruttando le funzionalità di debug di Visual Studio.

1. Tornare a Visual Studio e aprire `Controllers\HomeController.cs`
2. Sostituire il contenuto del metodo About() con quanto riportato di seguito:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Impostare un punto di interruzione a sinistra della riga `string message`.
4. Premere **&lt;F5>** per avviare il debug.
5. Accedere alla pagina About per raggiungere il punto di interruzione.
6. Passare a Visual Studio per visualizzare il punto di interruzione ed esaminare il valore del messaggio.

   ![][2]

## <a name="summary"></a>Riepilogo
Con [Docker Tools for Visual Studio 2015](https://aka.ms/DockerToolsForVS)si ottiene la produttività tipica del lavoro in locale, con la realtà produttiva offerta dallo sviluppo in un contenitore Docker.

## <a name="troubleshooting"></a>risoluzione dei problemi
[Risoluzione dei problemi di sviluppo di Docker in Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Altre informazioni su Docker con Visual Studio, Windows e Azure
* [Docker Tools for Visual Studio 2015](http://aka.ms/dockertoolsforvs) : sviluppo di codice .NET Core in un contenitore
* [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) : compilare e distribuire contenitori Docker
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) : servizi di linguaggio per la modifica dei file di Docker, con altri scenari e2e presto disponibili
* [Documentazione dei contenitori di Windows](http://aka.ms/containers): informazioni su Windows Server e Nano Server
* [Servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) - [Contenuto servizio contenitore di Azure](http://aka.ms/AzureContainerService)
* Per altri esempi di uso di Docker, vedere [Working with Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) (Uso di Docker) dalla [demo](https://github.com/Microsoft/HealthClinic.biz) [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) 2015 Connect. Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Guide introduttive agli strumenti di sviluppo di Azure).

## <a name="various-docker-tools"></a>Altri strumenti di Docker
[Some great docker tools  (Importanti strumenti di Docker, blog di Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articoli utili
[Introduction to Microservices from NGINX (Introduzione ai microservizi di NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentazioni
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Presentazione dal vivo a Las Vegas nel 2016: e2e di Docker)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core @ Build 2016 - Where You At Demo (Introduzione ad ASP.NET Core in Build 2016 - Demo)](https://channel9.msdn.com/Events/Build/2016/B810)
* [Developing .NET apps in containers, Channel 9 (Sviluppo di applicazioni .NET in contenitori, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

