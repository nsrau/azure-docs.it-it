<properties
   pageTitle="Debug delle app in un contenitore Docker locale con Edit and Refresh | Microsoft Azure"
   description="Informazioni su come modificare un'app in esecuzione in un contenitore Docker locale e aggiornare il contenitore tramite la funzionalità di modifica e aggiornamento"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/25/2016"
   ms.author="tarcher" />

# Debug delle app in un contenitore Docker locale con Edit and Refresh

## Panoramica
Visual Studio Tools for Docker offre un modo pratico per sviluppare e testare l'applicazione localmente in un contenitore Docker senza dover riavviare il contenitore ogni volta che si apporta una modifica al codice. Questo articolo illustra come usare la funzionalità "Edit and Refresh" per avviare un'app Web ASP.NET 5 in un contenitore Docker locale, apportare le modifiche necessarie e quindi aggiornare il browser per visualizzare le modifiche.

## Prerequisiti
È necessario installare gli strumenti seguenti.

- [Visual Studio 2015 Update 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP.NET e strumenti Web 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)
- [Configurare il client Docker](./vs-azure-tools-docker-setup.md)

> [AZURE.NOTE] Se è installata una versione precedente di Visual Studio 2015 Tools for Docker, sarà necessario disinstallarla dal Pannello di controllo prima di installare la versione più recente.

## Modifica di un'app in esecuzione in un contenitore Docker locale
Visual Studio 2015 Tools for Docker consente agli sviluppatori di app Web ASP .NET 5 di testare ed eseguire l'applicazione in un contenitore Docker, di apportare modifiche all'applicazione in Visual Studio e di aggiornare il browser per visualizzare le modifiche applicate all'app in esecuzione nel contenitore.

1. Nel menu di Visual Studio selezionare **File > Nuovo > Progetto**. 

1. Nella sezione **Modelli** della finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web**.

1. Selezionare **Applicazione Web ASP.NET**.

1. Assegnare un nome alla nuova applicazione (o accettare quello predefinito).

1. Toccare **OK**.

1. In **Modelli ASP.NET 5** selezionare **Applicazione Web ASP.NET**.

1. Toccare **OK**.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > Supporto Docker**.

	![][0]
 
1. Nel nodo del progetto vengono creati i file seguenti:

	![][1]

1. Impostare Configurazione soluzione su `Debug` e premere **&lt;F5>** per avviare il test dell'applicazione localmente.

1. Una volta che l'immagine del contenitore è stata compilata ed è in esecuzione in un contenitore Docker, una console di PowerShell cercherà di avviare l'app Web nel browser predefinito. Se si usa il browser Microsoft Edge, vedere la sezione [Risoluzione dei problemi](#troubleshooting).

1. Tornare a Visual Studio e aprire `Views\Home\Index.cshtml`.

1. Aggiungere il contenuto HTML seguente alla fine del file e salvare le modifiche.

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	Tornare al browser e aggiornarlo.

1.	Scorrere fino alla fine della home page per visualizzare le modifiche applicate. Si noti che la ricompilazione del sito può richiedere alcuni secondi. Se quindi le modifiche non vengono immediatamente visualizzate, è sufficiente aggiornare di nuovo il browser.

##Risoluzione dei problemi 

- **L'esecuzione dell'app fa sì che PowerShell venga aperto, visualizzi un errore e quindi venga chiuso. La pagina del browser non si apre.**

	È possibile che si verifichi un errore durante l'esecuzione di `docker-compose-up`. Per visualizzare l'errore, eseguire questi passaggi:

	1. Aprire il file `Properties\launchSettings.json`.
	
	1. Individuare la voce Docker.
	
	1. Individuare la riga che inizia come segue:

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. Aggiungere il parametro `-noexit` in modo che la riga ora sia simile alla seguente. In questo modo PowerShell rimarrà aperto e sarà possibile visualizzare l'errore.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **Compilazione: non è possibile compilare l'immagine. Errore durante il controllo della connessione TLS: l'host non è in esecuzione.**

	Verificare che l'host Docker predefinito sia in esecuzione. Vedere l’articolo [Configurare il client Docker](./vs-azure-tools-docker-setup.md).

- **Non è possibile trovare il mapping del volume.**

	Per impostazione predefinita, VirtualBox condivide `C:\Users` come `c:/Users`. Se il progetto non è in `c:\Users`, aggiungerlo manualmente alle [cartelle condivise](https://www.virtualbox.org/manual/ch04.html#sharedfolders) di VirtualBox.

- **Uso di Microsoft Edge come browser predefinito.**

	Se si usa il browser Microsoft Edge, è possibile che il sito non si apra perché Edge considera l'indirizzo IP non sicuro. Per risolvere il problema, eseguire questa procedura:
	1. Nella casella Esegui di Windows digitare `Internet Options`.
	2. Toccare **Opzioni Internet** quando vengono visualizzate. 
	2. Toccare la scheda **Sicurezza**.
	3. Selezionare l'area **Intranet locale**.
	4. Toccare **Siti**. 
	5. Aggiungere l'IP della macchina virtuale (in questo caso, l'host Docker) nell'elenco. 
	6. Aggiornare la pagina in Edge per visualizzare il sito come operativo. 
	7. Per altre informazioni su questo problema, vedere il post di blog di Scott Hanselman relativo all'[impossibilità di aprire o visualizzare in Microsoft Edge i siti Web ospitati in VirtualBox](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx).

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0330_2016-->