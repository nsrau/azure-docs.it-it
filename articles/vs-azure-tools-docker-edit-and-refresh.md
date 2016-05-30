<properties
   pageTitle="Debug di applicazioni in un contenitore Docker locale | Microsoft Azure"
   description="Informazioni su come modificare un'app in esecuzione in un contenitore Docker locale, aggiornare il contenitore tramite la funzionalità di modifica e aggiornamento e impostare i punti di interruzione del debug"
   services="visual-studio-online"
   documentationCenter="na"
   authors="AllenClark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/13/2016"
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

Per eseguire i contenitori Docker in locale, è necessario un client di Docker locale. È possibile usare la [casella degli strumenti di Docker](https://www.docker.com/products/overview#/docker_toolbox) che richiede la disabilitazione di Hyper-V o in alternativa è possibile usare [Docker per la versione Beta di Windows](https://beta.docker.com) che usa Hyper-V e richiede Windows 10.

Nella casella degli strumenti di Docker, è necessario [configurare il client di Docker](./vs-azure-tools-docker-setup.md)

## Modifica di un'app in esecuzione in un contenitore Docker locale
Gli Strumenti di Visual Studio 2015 per Docker consentono agli sviluppatori di App Web ASP .NET Core RC2 di testare ed eseguire l'applicazione in un contenitore Docker, apportare modifiche all'applicazione in Visual Studio e aggiornare il browser per visualizzare le modifiche applicate all'app in esecuzione nel contenitore. Con .NET Core e gli Strumenti di Visual Studio per Docker versione 0.20, è anche possibile impostare punti di interruzione per il codice in esecuzione nel contenitore Docker.

1. Nel menu di Visual Studio selezionare **File > Nuovo > Progetto**.

1. Nella sezione **Modelli** della finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web**.

1. Selezionare **ASP.NET Core Web Application (.NET Core)** (applicazione Web ASP.NET Core (.NET Core).

1. Assegnare un nome alla nuova applicazione (o accettare quello predefinito), quindi toccare **OK**.

1. In **ASP.NET Core Templates** (modelli di ASP.NET Core) selezionare **Applicazione Web** e toccare **OK**.

1. Deselezionare **Ospita nel cloud** dal momento che come soluzione di distribuzione verrà usato Docker.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Add > Docker Support** (Aggiungi > Supporto Docker).

	![][0]

1. Nel nodo del progetto vengono creati i file seguenti:

	![][1]

> [AZURE.NOTE] Se si sta usando [Docker per la versione Beta di Windows](https://beta.docker.com), aprire Properties (Proprietà)\\Docker.props, eliminare il valore predefinito e riavviare Visual Studio in modo che il valore abbia effetto. ![][2]

##Modifica e aggiornamento
Per eseguire rapidamente l'iterazione delle modifiche, è possibile avviare l'applicazione in un contenitore e continuare ad apportare modifiche, visualizzandole come si farebbe con IIS Express.

1. Impostare la configurazione della soluzione su `Debug` e premere **& lt;CTRL + F5 >** per creare l'immagine Docker ed eseguirla localmente. Visualizzare la finestra di output, usando la compilazione oppure

1. Una volta che l'immagine del contenitore è stata compilata ed è in esecuzione in un contenitore Docker, Visual Studio cercherà di avviare l'App Web nel browser predefinito. Se si usa il browser Microsoft Edge o se si verificano problemi, vedere l'articolo sulla [risoluzione dei problemi](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Tornare a Visual Studio e aprire `Views\Home\About.cshtml`.

1. Aggiungere il contenuto HTML seguente alla fine del file e salvare le modifiche.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Visualizzare la finestra di output e, quando viene completata la compilazione di .NET e viene visualizzato `Application started. Press Ctrl+C to shut down`, tornare al browser e aggiornare la pagina.

1.	Si dovrebbero vedere visualizzate le modifiche applicate.

##Punto di interruzione di debug
Spesso è necessario analizzare le modifiche in modo più approfondito, sfruttando le funzionalità di debug di Visual Studio.

1.	Tornare a Visual Studio e aprire `Controllers\HomeController.cs`

1.  Sostituire il contenuto del metodo About() con quanto riportato di seguito:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Impostare un punto di interruzione a sinistra della riga `string message`...

1.  Premere **& lt; F5 >** per avviare il debug.

1.  Accedere alla pagina About per raggiungere il punto di interruzione.

1.  Passare a Visual Studio per visualizzare il punto di interruzione ed esaminare il valore del messaggio.

	![][3]

##Riepilogo
Con gli [Strumenti di Visual Studio 2015 per Docker](https://aka.ms/DockerToolsForVS), si ottiene la produttività tipica del lavoro in locale, nella realtà produttiva offerta dallo sviluppo in un contenitore Docker.

## Risoluzione dei problemi
[Risoluzione dei problemi di sviluppo di Docker in Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Altre informazioni su Docker con Visual Studio, Windows e Azure

- [Strumenti di Docker per Visual Studio](http://aka.ms/dockertoolsforvs): sviluppo di codice .NET Core in un contenitore
- [Strumenti di Docker per Visual Studio Team Services](http://aka.ms/dockertoolsforvsts): compilare e distribuire contenitori Docker
- [Strumenti di Docker per Visual Studio Code](http://aka.ms/dockertoolsforvscode): servizi di linguaggio per la modifica dei file di Docker, con altri scenari e2e presto disponibili
- [Informazioni sul contenitore di Windows](http://aka.ms/containers): informazioni su Windows Server e Nano Server
- [Servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/): [Introduzione al servizio contenitore di Azure](http://aka.ms/AzureContainerService)

## Altri strumenti di Docker

[Some great docker tools ](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/) (Importanti strumenti di Docker, blog di Steve Lasker)

## Articoli utili

[Introduction to Microservices from NGINX](https://www.nginx.com/blog/introduction-to-microservices/) (Introduzione ai microservizi di NGINX)

## Presentazioni

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/) (Presentazione dal vivo a Las Vegas nel 2016: e2e di Docker)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Introduzione ad ASP.NET Core build 2016 - Demo)
- [Developing .NET apps in containers, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/) (Sviluppo di applicazioni .NET in contenitori, Channel 9)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0518_2016-->