---
title: Creare un&quot;app Web ASP.NET Core in Visual Studio Code
description: Questa esercitazione illustra come creare un&quot;app Web ASP.NET Core usando Visual Studio Code.
services: app-service\web
documentationcenter: .net
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e8f78602b293863b2e58160a5eb2cf8a5855576b
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-aspnet-core-web-app-in-visual-studio-code"></a>Creare un'app Web ASP.NET Core in Visual Studio Code
## <a name="overview"></a>Panoramica
Questa esercitazione illustra come creare un'app Web ASP.NET Core usando [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode) e come distribuirla nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md). 

> [!NOTE]
> Sebbene in questo articolo si faccia riferimento alle app Web, è applicabile anche ad app per le API e app per dispositivi mobili. 
> 
> 

ASP.NET Core è un'importante riprogettazione di ASP.NET. Costituisce un nuovo framework open source e multipiattaforma per la creazione tramite .NET di moderne app Web basate sul cloud. Per altre informazioni, vedere [Introduction to ASP.NET Core](http://docs.asp.net/latest/conceptual-overview/aspnet.html) (Cenni preliminari su ASP.NET Core). Per altre informazioni sulle app Web del servizio app di Azure, vedere [Panoramica delle app Web](app-service-web-overview.md).

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>Prerequisiti
* Installare [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Installare Git: è possibile installarlo da una delle seguenti posizioni: [Chocolatey](https://chocolatey.org/packages/git) o [git-scm.com](http://git-scm.com/downloads). Se non si ha familiarità con Git, scegliere [git-scm.com](http://git-scm.com/downloads) e selezionare l'opzione **Usare Git dal prompt dei comandi di Windows**. Dopo aver installato Git, è necessario impostare il nome utente e l'indirizzo di posta elettronica Git, poiché verrà richiesto più avanti nell'esercitazione (quando si eseguirà un'operazione di commit da Visual Studio Code).  

## <a name="install-aspnet-core"></a>Installare ASP.NET Core
ASP.NET Core è uno stack .NET snello per la creazione di un cloud moderno e di app Web in esecuzione su OS X, Linux e Windows. È stato completamente riprogettato per fornire un framework di sviluppo ottimizzato per le app che vengono distribuite nel cloud o eseguite in locale. È costituito da componenti modulari con un overhead minimo, in modo da garantire la massima flessibilità durante la creazione di soluzioni.

Questa esercitazione è stata realizzata per illustrare la creazione di applicazioni con le più recenti versioni di sviluppo di ASP.NET Core. Le istruzioni seguenti sono specifiche di Windows. Per istruzioni sull'installazione in OS X, Linux e Windows, vedere [Getting started with ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started) (Introduzione ad ASP.NET Core). 


> [!NOTE]
> Per istruzioni di installazione più dettagliate per OS X, Linux e Windows, vedere [Installing ASP.NET Core](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx) (Installazione di ASP.NET Core). 
> 
> 

## <a name="create-the-web-app"></a>Creare l'app Web
Questa sezione illustra come eseguire lo scaffolding di una nuova app Web ASP.NET usando lo strumento dell'interfaccia della riga di comando dotnet. 

1. Digitare quanto segue al prompt dei comandi per creare la cartella di progetto ed eseguire lo scaffolding dell'app.
   
```terminal
mkdir SampleWebApp
cd SampleWebApp
dotnet new mvc
```
![Generatore dell'interfaccia della riga di comando dotnet - ASP.NET Core](./media/web-sites-create-web-app-using-vscode/dotnetcore-mvc-01.png)

2. Per installare i pacchetti NuGet necessari per eseguire il comando seguente:
   
    ```terminal
    dotnet restore
    ```

## <a name="run-the-web-app-locally"></a>Eseguire l'app Web in locale
Dopo aver creato l'app Web e recuperato tutti i pacchetti NuGet per l'app, è ora possibile eseguire l'app Web in locale.

1. Eseguire l'app (il comando `dotnet run` compilerà l'app quando è scaduta):
    ```terminal
    dotnet run
    ```
2. Aprire un browser e passare all'URL seguente.
   
    **http://localhost:5000**
   
    Verrà visualizzata la pagina predefinita dell'app Web, come illustrato di seguito.
   
    ![App Web locale in un browser](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. Chiudere il browser. Nella **finestra di comando** premere **Ctrl+C** per arrestare l'applicazione e chiudere la **finestra di comando**. 

## <a name="create-a-web-app-in-the-azure-portal"></a>Creazione di un'app Web nel portale di Azure ##
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

## <a name="enable-git-publishing-for-the-new-web-app"></a>Abilitare la pubblicazione Git per la nuova app Web
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
   
   * Fare clic su **Impostazioni** > **Credenziali di distribuzione**. Verrà visualizzato il pannello **Imposta credenziali di distribuzione** .
   * Creare un nome utente e una password.  La password sarà necessaria più avanti durante la configurazione di Git.
   * Fare clic su **Save**.
9. Nel pannello dell'app Web fare clic su **Impostazioni > Proprietà**. L'URL del repository Git remoto in cui verrà effettuata la distribuzione è visualizzato in **URL GIT**.
10. Copiare il valore dell'opzione **URL GIT** , che sarà necessario più avanti nell'esercitazione.
    
    ![URL Git di Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Pubblicare l'app Web nel servizio app di Azure
In questa sezione si creerà un repository Git locale e si eseguirà il push dal repository ad Azure per poter distribuire l'app Web in Azure.

1. In Visual Studio Code selezionare l'opzione **Git** nella barra di spostamento a sinistra.
   
    ![Icona di Git in Visual Studio Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. Selezionare **Initialize git repository** per accertarsi che l'area di lavoro sia sotto il controllo del codice sorgente di Git. 
   
    ![Initialize Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. Aprire la finestra di comando e passare alla directory dell'app Web. Immettere quindi il comando seguente:
   
        git config core.autocrlf false
   
    Questo comando evita un problema relativo al testo che concerne le terminazioni CRLF e LF.
4. In Visual Studio Code, aggiungere un messaggio per il commit e fare clic sull'icona con il segno di spunta per **Commit All** .
   
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

> [!NOTE]
> Se si apportano modifiche all'app, è possibile ripetere la pubblicazione direttamente in Visual Studio Code con la funzionalità Git predefinita integrata selezionando l'opzione **Commit All** seguita dall'opzione **Push**. L'opzione **Push** è disponibile nel menu a discesa accanto ai pulsanti **Commit all** e **Refresh**.
> 
> 

Se è necessario collaborare a un progetto, considerare la possibilità di effettuare il push in GitHub mentre si effettua il push in Azure.

## <a name="run-the-app-in-azure"></a>Eseguire l'app in Azure
Dopo aver distribuito l'app Web, è possibile ora eseguirla mentre è ospitata in Azure. 

A questo scopo, è possibile eseguire una delle due operazioni seguenti:

* Aprire un browser e immettere il nome dell'app Web come indicato di seguito.   
  
        http://SampleWebAppDemo.azurewebsites.net
* Nel Portale di Azure, individuare il pannello dell'app Web e fare clic su **Sfoglia** per visualizzare l'app 
* nel browser predefinito.

![App Web di Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>Riepilogo
In questa esercitazione si è appreso come creare un'app Web in Visual Studio Code e distribuirla in Azure. Per altre informazioni su Visual Studio Code, vedere l'articolo [Vantaggi di Visual Studio Code](https://code.visualstudio.com/Docs/). Per altre informazioni sulle app Web del servizio app, vedere [Panoramica delle app Web](app-service-web-overview.md). 


