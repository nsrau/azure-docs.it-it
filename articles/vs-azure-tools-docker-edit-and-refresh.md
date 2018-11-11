---
title: Debug di applicazioni in un contenitore Docker locale | Documentazione Microsoft
description: Informazioni su come modificare un'app in esecuzione in un contenitore Docker locale, aggiornare il contenitore tramite la funzionalità di modifica e aggiornamento e impostare i punti di interruzione del debug
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 8844992ffbed2d57ad54a5f0e4a2d0b05c1d277a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968729"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Debug delle applicazioni in un contenitore Docker locale
## <a name="overview"></a>Panoramica
Visual Studio 2017 consente di sviluppare in un contenitore Docker e convalidare l'applicazione in locale.
Non è necessario riavviare il contenitore ogni volta che si esegue una modifica del codice.
Questo articolo illustra come usare la funzionalità di modifica e aggiornamento per avviare un'app Web ASP.NET Core in un contenitore Docker locale, apportare le modifiche necessarie e quindi aggiornare il browser per visualizzare tali modifiche.
Illustra inoltre come impostare i punti di interruzione per il debug.

## <a name="prerequisites"></a>Prerequisiti
È necessario che siano installati gli strumenti seguenti.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro Sviluppo Web installato.

Per eseguire i contenitori Docker in locale, è necessario un client di Docker locale.
È possibile usare la [casella degli strumenti di Docker](https://www.docker.com/products/docker-toolbox), che richiede la disabilitazione di Hyper-V, o in alternativa [Docker per Windows](https://www.docker.com/get-docker), che usa Hyper-V e richiede Windows 10.

Nella casella degli strumenti di Docker è necessario [configurare il client di Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Creare un'app Web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2. Modificare il codice e aggiornarlo
Per eseguire rapidamente l'iterazione delle modifiche, è possibile avviare l'applicazione in un contenitore e continuare ad apportare modifiche, visualizzandole come si farebbe con IIS Express.

1. Impostare la configurazione della soluzione su `Debug` e premere **&lt;CTRL + F5>** per creare l'immagine Docker ed eseguirla localmente.

    Dopo che l'immagine del contenitore è stata compilata ed è in esecuzione in un contenitore Docker, Visual Studio avvia l'app Web nel browser predefinito.
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

## <a name="3-debug-with-breakpoints"></a>3. Eseguire il debug con punti di interruzione
Spesso è necessario analizzare le modifiche in modo più approfondito, sfruttando le funzionalità di debug di Visual Studio.

1. Tornare a Visual Studio e aprire `About.cshtml.cs`
2. Sostituire il contenuto del metodo OnGet() con quanto riportato di seguito:

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. Impostare un punto di interruzione a sinistra della riga di codice.
4. Premere **&lt;F5>** per avviare il debug.
5. Accedere alla pagina About per raggiungere il punto di interruzione.
6. Passare a Visual Studio per visualizzare il punto di interruzione ed esaminare il valore del messaggio.

   ![][2]

## <a name="summary"></a>Summary
Con il supporto di Docker in Visual Studio 2017 si ottiene la produttività tipica del lavoro in locale, con la realtà produttiva offerta dallo sviluppo in un contenitore Docker.

## <a name="troubleshooting"></a>risoluzione dei problemi
[Risoluzione dei problemi di sviluppo di Docker in Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Altre informazioni su Docker con Visual Studio, Windows e Azure
* [Sviluppo con i contenitori in Visual Studio](/visualstudio/containers): pagina hub sullo sviluppo di contenitori
* [Docker Integration for Azure Pipelines](http://aka.ms/dockertoolsforvsts) (Integrazione di Docker per pipeline Azure): compilare e distribuire contenitori Docker
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) : servizi di linguaggio per la modifica dei file di Docker, con altri scenari e2e presto disponibili
* [Documentazione dei contenitori di Windows](http://aka.ms/containers): informazioni su Windows Server e Nano Server
* [Servizio Kubernetes di Azure](https://azure.microsoft.com/services/kubernetes-service/) - [Documentazione del servizio Kubernetes di Azure](/azure/aks)

## <a name="various-docker-tools"></a>Altri strumenti di Docker
[Some great docker tools  (Importanti strumenti di Docker, blog di Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articoli utili
[Introduction to Microservices from NGINX (Introduzione ai microservizi di NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentazioni
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Presentazione dal vivo a Las Vegas nel 2016: e2e di Docker)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core @ Build 2016 - Where You At Demo (Introduzione ad ASP.NET Core in Build 2016 - Demo)](https://channel9.msdn.com/Events/Build/2016/B810)
* [Developing .NET apps in containers, Channel 9 (Sviluppo di applicazioni .NET in contenitori, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
