<properties
   pageTitle="Debug di applicazioni in un contenitore Docker locale | Microsoft Azure"
   description="Informazioni su come modificare un'app in esecuzione in un contenitore Docker locale, aggiornare il contenitore tramite la funzionalità di modifica e aggiornamento e impostare i punti di interruzione del debug"
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="allclark" />

# Debug delle applicazioni in un contenitore Docker locale

## Panoramica
Gli Strumenti di Visual Studio per Docker consentono di sviluppare e convalidare l'applicazione in locale in un contenitore Docker. Non è necessario riavviare il contenitore ogni volta che si esegue una modifica del codice. Questo articolo illustra come usare la funzionalità di modifica e aggiornamento per avviare un'App Web ASP.NET Core in un contenitore Docker locale, apportare le modifiche necessarie e quindi aggiornare il browser per visualizzare le modifiche. Illustra anche come impostare i punti di interruzione per il debug.

> [AZURE.NOTE] Il supporto del contenitore di Windows sarà disponibile nelle versioni future

## Prerequisiti
È necessario installare gli strumenti seguenti.

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)

Per eseguire i contenitori Docker in locale, è necessario un client di Docker locale. È possibile utilizzare la [casella degli strumenti di Docker](https://www.docker.com/products/overview#/docker_toolbox) che richiede la disabilitazione di Hyper-V o in alternativa è possibile utilizzare [Docker per la versione beta di Windows](https://beta.docker.com) che utilizza Hyper-V e richiede Windows 10.

Nella casella degli strumenti di Docker è necessario [configurare il client di Docker](./vs-azure-tools-docker-setup.md)

## 1\. Creare un'app Web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Aggiungere il supporto di Docker

[AZURE.INCLUDE [Aggiungere il supporto di Docker](../includes/vs-azure-tools-docker-add-docker-support.md)]


## 3\. Modificare il codice e aggiornarlo

Per eseguire rapidamente l'iterazione delle modifiche, è possibile avviare l'applicazione in un contenitore e continuare ad apportare modifiche, visualizzandole come si farebbe con IIS Express.

1. Impostare la configurazione della soluzione su `Debug` e premere **& lt;CTRL+F5>** per creare l'immagine Docker ed eseguirla localmente.

    Una volta che l'immagine del contenitore è stata compilata ed è in esecuzione in un contenitore Docker, Visual Studio avvierà l'App Web nel browser predefinito. Se si usa il browser Microsoft Edge o se si verificano problemi, vedere la sezione relativa alla [risoluzione dei problemi](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Passare alla pagina About, da dove verranno apportate le modifiche.

1. Tornare a Visual Studio e aprire `Views\Home\About.cshtml`.

1. Aggiungere il contenuto HTML seguente alla fine del file e salvare le modifiche.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Visualizzare la finestra di output e, quando viene completata la compilazione di .NET e vengono visualizzate queste righe, tornare al browser e aggiornare la pagina About.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	Le modifiche sono state applicate.

## 4\. Eseguire il debug con punti di interruzione

Spesso è necessario analizzare le modifiche in modo più approfondito, sfruttando le funzionalità di debug di Visual Studio.

1.	Tornare a Visual Studio e aprire `Controllers\HomeController.cs`

1.  Sostituire il contenuto del metodo About() con quanto riportato di seguito:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Impostare un punto di interruzione a sinistra della riga `string message`.

1.  Premere **& lt;F5>** per avviare il debug.

1.  Accedere alla pagina About per raggiungere il punto di interruzione.

1.  Passare a Visual Studio per visualizzare il punto di interruzione ed esaminare il valore del messaggio.

	![][2]

##Riepilogo

Con [Docker Tools for Visual Studio 2015](https://aka.ms/DockerToolsForVS) si ottiene la produttività tipica del lavoro in locale, con la realtà produttiva offerta dallo sviluppo in un contenitore Docker.

## Risoluzione dei problemi

[Risoluzione dei problemi di sviluppo di Docker in Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Altre informazioni su Docker con Visual Studio, Windows e Azure

- [Docker Tools for Visual Studio 2015](http://aka.ms/dockertoolsforvs): sviluppo di codice .NET Core in un contenitore
- [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts): compilare e distribuire contenitori Docker
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode): servizi di linguaggio per la modifica dei file di Docker, con altri scenari e2e presto disponibili
- [Documentazione dei contenitori di Windows](http://aka.ms/containers): informazioni su Windows Server e Nano Server
- [Servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) - [introduzione al servizio contenitore di Azure](http://aka.ms/AzureContainerService)
-    Per ulteriori esempi di utilizzo di Docker, vedere [Working with Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) (Uso di Docker) dalla [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Guide introduttive agli strumenti di sviluppo di Azure).

## Altri strumenti di Docker

[Some great docker tools (Importanti strumenti di Docker, blog di Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Articoli utili

[Introduction to Microservices from NGINX (Introduzione ai microservizi di NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## Presentazioni

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Presentazione dal vivo a Las Vegas nel 2016: e2e di Docker)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (Introduzione ad ASP.NET Core build 2016 - Demo)](https://channel9.msdn.com/Events/Build/2016/B810)
- [Developing .NET apps in containers, Channel 9 (Sviluppo di applicazioni .NET in contenitori, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0727_2016-->