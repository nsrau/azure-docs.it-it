<properties
   pageTitle="Creare un'app Web ASP.NET 5 in Visual Studio Code"
   description="Questa esercitazione illustra come creare un'app Web ASP.NET 5 usando Visual Studio Code."
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>

# Creare un'app Web ASP.NET 5 in Visual Studio Code

## Panoramica

Questa esercitazione illustra come creare un'app Web ASP.NET 5 usando [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode) e come distribuirla nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

> [AZURE.NOTE] Sebbene in questo articolo si faccia riferimento alle app Web, è applicabile anche ad app per le API e app per dispositivi mobili.

ASP.NET 5 è una riprogettazione significativa di ASP.NET. Costituisce un nuovo framework open source e multipiattaforma per la creazione di moderne app Web basate sul cloud tramite .NET. Per altre informazioni, vedere l'articolo di [introduzione ad ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html) Per altre informazioni sulle app Web del servizio app di Azure, vedere [Panoramica delle app Web](app-service-web-overview.md).

[AZURE.INCLUDE [App-Service-Web-try-App-Service.MD](../../includes/app-service-web-try-app-service.md)]

## Prerequisiti  

* Installare [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Installare [Node.js](http://nodejs.org): Node.js è una piattaforma per la creazione di applicazioni server veloci e scalabili tramite JavaScript. Node è il runtime (nodo), mentre [npm](http://www.npmjs.com/) è lo strumento di gestione pacchetti per i moduli Node. In questa esercitazione si userà npm per eseguire lo scaffolding di un'app Web ASP.NET 5.
* Installare Git: è possibile installarlo da una delle seguenti posizioni: [Chocolatey](https://chocolatey.org/packages/git) o [git-scm.com](http://git-scm.com/downloads). Se non si ha familiarità con Git, scegliere [git-scm.com](http://git-scm.com/downloads) e selezionare l'opzione **Usare Git dal prompt dei comandi di Windows**. Dopo aver installato Git, è necessario impostare il nome utente e l'indirizzo di posta elettronica Git, poiché verrà richiesto più avanti nell'esercitazione (quando si eseguirà un'operazione di commit da Visual Studio Code).  

## Installare ASP.NET 5 e DNX
ASP.NET 5/DNX (l’ambiente di esecuzione .NET) è uno stack .NET snello per la creazione di un cloud moderno e di app Web in esecuzione su OS X, Linux e Windows. È stato completamente riprogettato per fornire un framework di sviluppo ottimizzato per le app che vengono distribuite nel cloud o eseguite in locale. È costituito da componenti modulari con un overhead minimo, in modo da garantire la massima flessibilità durante la creazione di soluzioni.

Questa esercitazione è stata realizzata per consentire la creazione di applicazioni con le più recenti versioni di sviluppo di ASP.NET 5 e DNX. Le istruzioni seguenti sono specifiche di Windows. Per istruzioni di installazione più dettagliate per OS X, Linux e Windows, vedere l'articolo relativo all'[installazione di ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Per installare .NET Version Manager (DNVM) in Windows, aprire un prompt dei comandi ed eseguire il comando seguente.

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	Lo script DNVM verrà scaricato e inserito nella directory del profilo utente.

2. Per completare l'installazione di DNVM, **riavviare Windows**.

	Dopo aver riavviato Windows, è possibile aprire il prompt dei comandi per verificare il percorso di DNVM immettendo quanto segue:

		where dnvm

	Il prompt dei comandi visualizzerà un percorso analogo al seguente.

	![Percorso dnvm](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

3. A questo punto è necessario usare DNVM per scaricare DNX ed eseguire le applicazioni. Eseguire quanto segue al prompt dei comandi:

		dnvm upgrade

	Verificare DNVM e visualizzare il runtime attivo immettendo quanto segue al prompt dei comandi:

		dnvm list

	Il prompt dei comandi visualizzerà i dettagli del runtime attivo.

	![Percorso DNVM](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

	Se è elencato più di un runtime DNX, è possibile scegliere di immettere quello seguente (o una versione più recente) al prompt dei comandi per impostare il runtime DNX attivo. Impostarlo per la stessa versione utilizzata dal generatore di ASP.NET 5 quando si crea l'app Web più avanti in questa esercitazione. *Potrebbe non essere necessario cambiare il runtime attivo se questo è impostato sull'ultimo disponibile.*

		dnvm use 1.0.0-update1 –p

> [AZURE.NOTE] Per istruzioni di installazione più dettagliate per OS X, Linux e Windows, vedere l'articolo relativo all'[installazione di ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Creare l'app Web 

Questa sezione illustra come eseguire lo scaffolding di una nuova app Web ASP.NET. Si userà npm (Node Package Manager) per installare [Yeoman](http://yeoman.io/) (strumento di scaffolding di applicazioni, l'equivalente di Visual Studio Code dell'operazione **File > Nuovo progetto** di Visual Studio), [Grunt](http://gruntjs.com/) (utilità di esecuzione di attività JavaScript) e [Bower](http://bower.io/) (strumento di gestione pacchetti sul lato client).

1. Aprire un prompt dei comandi con diritti di amministratore e passare al percorso in cui si desidera creare il progetto ASP.NET. Ad esempio, creare una directory *vscodeprojects* nella radice di C:.

2. Digitare quanto segue al prompt dei comandi per installare Yeoman e gli strumenti di supporto.

		npm install -g yo grunt-cli generator-aspnet bower

	> [AZURE.NOTE] È possibile che venga visualizzato un avviso che segnala che la versione npm non è aggiornata. Questo avviso non dovrebbe incidere su questa esercitazione.

3. Digitare quanto segue al prompt dei comandi per creare la cartella di progetto ed eseguire lo scaffolding dell'app.

		yo aspnet

4. Usare i tasti freccia per selezionare il tipo di **Applicazione Web base** dal menu generatore di ASP.NET 5 e premere **&lt;Enter>**.

	![Yeoman - Generatore di ASP.NET 5](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. Assegnare alla nuova app Web ASP.NET il nome **SampleWebApp**. Questo nome verrà usato nel corso dell'intera esercitazione, quindi se si seleziona un nome diverso, sarà necessario sostituirlo per ogni occorrenza di **SampleWebApp**. Nel momento in cui si preme **&lt;Enter>**, Yeoman crea una nuova cartella denominata **SampleWebApp** e i file necessari per la nuova app.

6. Al prompt dei comandi, modificare e directory nella nuova cartella del progetto:

		cd SampleWebApp

7. Inoltre, al prompt dei comandi, per installare i pacchetti NuGet necessari per eseguire l'applicazione, immettere il comando seguente:

		dnu restore

8. Aprire Visual Studio Code immettendo quanto segue al prompt dei comandi.

		code .

## Eseguire l'app Web in locale

Dopo aver creato l'app Web e recuperato tutti i pacchetti NuGet per l'app, è ora possibile eseguire l'app Web in locale.

1. In Visual Studio Code, immettere quanto segue in **Command Palette** per visualizzare le opzioni di esecuzione dei comandi disponibili.

		dnx: Run Command

	> [AZURE.NOTE] Se il server Omnisharp non è in esecuzione, si avvierà. Immettere nuovamente il comando precedente.

	Selezionare quindi il comando seguente per eseguire l'app web:
		
		dnx web - (SampleWebApp)

	Nella finestra di comando verrà visualizzato l'applicazione è stata avviata. Se nella finestra di comando non compare questo messaggio, verificare che nell'angolo inferiore sinistro della finestra di VSCode non siano presenti errori relativi al progetto.
	
	> [AZURE.NOTE] Per eseguire un comando da **Command Palette**, è necessario inserire un carattere **>** all'inizio della riga di comando. È possibile visualizzare i dettagli del comando **web** nel file *project.json*.

2. Aprire un browser e passare all'URL seguente.

	**http://localhost:5000**

	Verrà visualizzata la pagina predefinita dell'app Web, come illustrato di seguito.

	![App Web locale in un browser](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

3. Chiudere il browser. Nella **finestra di comando**, premere **Ctrl+C** per arrestare l'applicazione e chiudere la **finestra di comando**.

## Creazione di un'app Web nel portale di Azure ##

La procedura seguente consente di creare di un'app Web nel Portale di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO** nella parte superiore sinistra del portale.

3. Fare clic su **App Web > App Web**.

	![Nuova app Web di Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. Immettere un valore in **Nome**, ad esempio **SampleWebAppDemo**. Il nome deve essere univoco, come imposto dal portale quando si tenta di immettere il nome. Se si immette un valore diverso, sarà necessario sostituirlo per ogni occorrenza di **SampleWebAppDemo** presente in questa esercitazione.

5. Selezionare un piano esistente in **Piano di servizio app** o crearne uno nuovo. Se si crea un nuovo piano, selezionare i piano tariffario, la posizione e altre opzioni. Per altre informazioni sui piani di servizio app, vedere l'articolo [Panoramica approfondita dei piani del servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	![Pannello Nuova app Web di Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. Fare clic su **Crea**.

	![Pannello dell'app Web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## Abilitare la pubblicazione Git per la nuova app Web

Git è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire l'app Web del servizio app di Azure. Il codice scritto per l'app Web verrà archiviato in un repository Git locale e verrà distribuito in Azure tramite il push in un repository remoto.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**.

3. Fare clic su **App Web** per visualizzare un elenco delle app Web associate alla sottoscrizione di Azure.

4. Selezionare l'app Web creata in questa esercitazione.

5. Nel pannello dell'app Web fare clic su **Impostazioni** > **Distribuzione continua**.

	![Host dell'app Web di Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. Fare clic su **Scegliere l'origine > Repository Git locale**.

7. Fare clic su **OK**.

	![Repository Git locale di Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Se le credenziali di distribuzione per la pubblicazione di un'app Web o di un'altra app del servizio app non sono ancora state configurate, è possibile farlo ora:

	* Fare clic su **Impostazioni** > **Credenziali di distribuzione**. Verrà visualizzato il pannello **Imposta credenziali di distribuzione**.

	* Creare un nome utente e una password. La password sarà necessaria più avanti durante la configurazione di Git.

	* Fare clic su **Save**.

9. Nel pannello dell'app Web fare clic su **Impostazioni > Proprietà**. L'URL del repository Git remoto in cui verrà effettuata la distribuzione è visualizzato in **URL GIT**.

10. Copiare il valore dell'opzione **URL GIT**, che sarà necessario più avanti nell'esercitazione.

	![URL Git di Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Pubblicare l'app Web nel servizio app di Azure

In questa sezione si creerà un repository Git locale e si eseguirà il push dal repository ad Azure per poter distribuire l'app Web in Azure.

1. In Visual Studio Code selezionare l'opzione **Git** nella barra di spostamento a sinistra.

	![Icona di Git in Visual Studio Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. Selezionare **Initialize git repository** per accertarsi che l'area di lavoro sia sotto il controllo del codice sorgente di Git.

	![Initialize Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. Aprire la finestra di comando e passare alla directory dell'app Web. Immettere quindi il comando seguente:

		git config core.autocrlf false

	Questo comando evita un problema relativo al testo che concerne le terminazioni CRLF e LF.

4. In Visual Studio Code, aggiungere un messaggio per il commit e fare clic sull'icona con il segno di spunta per **Commit All**.

	![Commit All in Git](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

5. Al termine dell'operazione di elaborazione, nella finestra Git non sarà più elencato alcun file e verrà invece visualizzata la voce **Changes**.

	![Nessuna modifica in Git](./media/web-sites-create-web-app-using-vscode/no-changes.png)

6. Tornare alla finestra di comando in cui il prompt dei comandi punta alla directory in cui si trova l'app Web.

7. Creare un riferimento remoto per effettuare il push degli aggiornamenti nell'app Web usando l'URL GIT (che termina con "git") copiato in precedenza.

		git remote add azure [URL for remote repository]

8. Configurare Git per salvare le credenziali a livello locale, in modo che siano aggiunte automaticamente ai comandi push generati da Visual Studio Code.

		git config credential.helper store

9. Effettuare il push delle modifiche in Azure usando il comando seguente. Dopo questo push iniziale in Azure, sarà possibile eseguire tutti i comandi push di Visual Studio Code.

		git push -u azure master

	Verrà richiesto di specificare la password creata in precedenza in Azure. **Nota: La password non sarà visibile.**

	L'output generato dal comando precedente termina con un messaggio in cui si specifica che la distribuzione è stata completata correttamente.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE] Se si apportano modifiche all'app, è possibile ripetere la pubblicazione direttamente in Visual Studio Code con la funzionalità Git predefinita integrata selezionando l'opzione **Commit All** seguita dall'opzione **Push**. L'opzione **Push** è disponibile nel menu a discesa accanto ai pulsanti **Commit all** e **Refresh**.

Se è necessario collaborare a un progetto, considerare la possibilità di effettuare il push in GitHub mentre si effettua il push in Azure.

## Eseguire l'app in Azure
Dopo aver distribuito l'app Web, è possibile ora eseguirla mentre è ospitata in Azure.

A questo scopo, è possibile eseguire una delle due operazioni seguenti:

* Aprire un browser e immettere il nome dell'app Web come indicato di seguito.   

		http://SampleWebAppDemo.azurewebsites.net
 
* Nel Portale di Azure, individuare il pannello dell'app Web e fare clic su **Sfoglia** per visualizzare l'app
* nel browser predefinito.

![App Web di Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## Riepilogo
In questa esercitazione si è appreso come creare un'app Web in Visual Studio Code e distribuirla in Azure. Per altre informazioni su Visual Studio Code, vedere l'articolo [Vantaggi di Visual Studio Code](https://code.visualstudio.com/Docs/). Per altre informazioni sulle app Web del servizio app, vedere [Panoramica delle app Web](app-service-web-overview.md).

<!---HONumber=AcomDC_0525_2016-->