---
title: Distribuzione dell&quot;archivio Git locale nel servizio app di Azure
description: Informazioni su come abilitare la distribuzione dell&quot;archivio Git locale nel servizio app di Azure.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 5842188b5d0a66436db7ab0f6b85bf14b4759c50
ms.lasthandoff: 02/16/2017


---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione dell'archivio Git locale nel servizio app di Azure
In questa esercitazione viene illustrato come distribuire l'applicazione nel [servizio app di Azure] da un repository Git nel computer locale. Il servizio app supporta questo approccio tramite l'opzione di distribuzione **Archivio Git locale** del [portale di Azure].  
Molti comandi Git descritti in questo articolo vengono eseguiti automaticamente quando si crea un'app del servizio app usando l'[interfaccia della riga di comando di Azure] come descritto [qui](app-service-web-get-started.md).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, sono necessari:

* Git. È possibile scaricare il file di installazione binario [qui](http://www.git-scm.com/downloads).  
* Conoscenze di base di Git.
* Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial) oppure [attivare i vantaggi per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.  
> 
> 

## <a name="a-namestep1astep-1-create-a-local-repository"></a><a name="Step1"></a>Passaggio 1: Creare un repository locale
Per creare un nuovo repository Git, eseguire le operazioni seguenti.

1. Avviare uno strumento da riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale** .
2. Passare alla directory in cui deve essere collocato il contenuto da distribuire.
3. Eseguire il comando seguente per inizializzare un nuovo repository Git:
   
        git init

## <a name="a-namestep2astep-2-commit-your-content"></a><a name="Step2"></a>Passaggio 2: Eseguire il commit del contenuto
Il servizio app supporta applicazioni create in diversi linguaggi di programmazione. 

1. Se il repository include già il contenuto ignorare questo passaggio e passare al passaggio 2. Se non include ancora il contenuto, popolare il repository con un file HTML statico come indicato di seguito: 
   
   * Usando un editor di testo creare un nuovo file denominato **index.html** nella radice del repository Git
   * Aggiungere il testo seguente come contenuto del file index.html e salvarlo: *Hello Git!*
2. Dalla riga di comando verificare che sia selezionata la radice del repository Git. Usare quindi il comando seguente per aggiungere file al repository:
   
        git add -A 
3. In seguito, eseguire il commit delle modifiche al repository con il comando seguente:
   
        git commit -m "Hello Azure App Service"

## <a name="a-namestep3astep-3-enable-the-app-service-app-repository"></a><a name="Step3"></a>Passaggio 3: Abilitare il repository dell'app del servizio app
Eseguire la procedura seguente per abilitare un repository Git per l'app del servizio app.

1. Accedere al [portale di Azure].
2. Nel pannello dell'app del servizio app fare clic su **Impostazioni > Origine distribuzione**. Fare clic su **Scegliere l'origine**, quindi su **Repository Git locale** e infine su **OK**.  
   
    ![Repository Git locale](./media/app-service-deploy-local-git/local_git_selection.png)
3. Se si tratta della prima impostazione di un repository in Azure, è necessario creare le credenziali di accesso, che verranno usate per accedere al repository di Azure e per effettuare il push delle modifiche dal repository Git locale. Dal pannello dell'app fare clic su **Impostazioni > Credenziali per la distribuzione**, quindi configurare il nome utente e la password per la distribuzione. Al termine, fare clic su **Salva**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="a-namestep4astep-4-deploy-your-project"></a><a name="Step4"></a>Passaggio 4: Distribuire il progetto
Eseguire la procedura seguente per pubblicare l'app nel servizio app usando l'archivio Git locale.

1. Nel pannello dell'app nel portale di Azure fare clic su **Impostazioni > Proprietà** per l'**URL GIT**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **URL Git** è il riferimento remoto in cui eseguire la distribuzione dal repository locale. Questo URL verrà utilizzato nella procedura seguente.
2. Usando la riga di comando verificare che sia selezionata la radice del repository Git locale.
3. Utilizzare `git remote` per aggiungere il riferimento remoto elencato in **URL Git** dal passaggio 1. Il comando sarà simile al seguente:
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > Il comando **remote** consente di aggiungere un riferimento denominato a un repository remoto. In questo esempio viene creato un riferimento denominato 'azure' per il repository dell'app Web.
   > 
   > 
4. Effettuare il push del contenuto nel servizio app usando il nuovo riferimento remoto **azure** creato.
   
        git push azure master
   
    Verrà richiesto di inserire la password creata in precedenza durante la reimpostazione delle credenziali di distribuzione nel portale di Azure. Immettere la password. Si noti che Gitbash non ripete gli asterischi nella console mentre si digita la password. 
5. Tornare all'app nel portale di Azure. Nel pannello **Distribuzioni** verrà visualizzata una voce di log dell'ultimo push effettuato. 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Fare clic sul pulsante **Sfoglia** nella parte superiore del pannello dell'app per verificare che il contenuto sia stato distribuito. 

## <a name="a-namestep5atroubleshooting"></a><a name="Step5"></a>Risoluzione dei problemi
Di seguito sono riportati gli errori o i problemi che si verificano comunemente durante l'uso di Git per la pubblicazione in un'app del servizio app di Azure:

- - -
**Sintomo**: non è possibile accedere a '[siteURL]'. La connessione a [scmAddress] non è riuscita.

**Causa**: questo errore può verificarsi se l'app non è in esecuzione.

**Soluzione**: avviare l'app nel portale di Azure. La distribuzione Git non funziona se l'app non è in esecuzione. 

- - -
**Sintomo**: non è possibile risolvere il nome dell'host 'nomehost'.

**Causa**: questo errore può verificarsi se le informazioni sull'indirizzo immesse durante la creazione del repository remoto 'azure' non sono corrette.

**Soluzione**: usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.

- - -
**Sintomo**: non sono stati trovati riferimenti in comune e non ne sono stati specificati. Non viene eseguita alcuna operazione. Forse è necessario specificare un ramo, ad esempio 'master'.

**Causa**: questo errore può verificarsi se non si specifica un ramo quando si effettua un'operazione push in Git e non è stato impostato il valore push.default usato da Git.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. Ad esempio:

    git push azure master

- - -
**Sintomo**: non sono state trovate corrispondenze per src refspec [nomeramo].

**Causa**: questo errore può verificarsi se si tenta di effettuare il push in un ramo diverso dal master nel repository remoto 'azure'.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. ad esempio:

    git push azure master

- - -
**Sintomo**: errore. Le modifiche vengono sottoposte a commit nel repository remoto ma l'app Web non viene aggiornata.

**Causa**: questo errore può verificarsi se si distribuisce un'app Node.js contenente un file package.json che specifica altri moduli necessari.

**Risoluzione**: i messaggi aggiuntivi contenenti 'npm ERR!' dovrebbero essere registrati prima di questo errore e possono fornire contesto aggiuntivo sul problema. Di seguito sono riportate le cause note di questo errore e del corrispondente messaggio 'npm ERR!' messaggio:

* **File package.json in formato non corretto**: npm ERR! Non è stato possibile leggere le dipendenze.
* **Modulo nativo senza una distribuzione binaria per Windows**:
  
  * npm ERR! Si è verificato un errore di \`cmd "/c" "node-gyp rebuild"\` con 1
    
      OPPURE
  * npm ERR! [modulename@version] preinstall: \`make || gmake\`

## <a name="additional-resources"></a>Risorse aggiuntive
* [Documentazione su Git](http://git-scm.com/documentation)
* [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Distribuzione continua nel servizio app di Azure](app-service-continuous-deployment.md)
* [Come usare PowerShell per Azure](/powershell/azureps-cmdlets-docs)
* [Come usare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

[servizio app di Azure]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[portale di Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[interfaccia della riga di comando di Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

