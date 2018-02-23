---
title: Distribuzione dell'archivio Git locale nel servizio app di Azure
description: Informazioni su come abilitare la distribuzione dell'archivio Git locale nel servizio app di Azure.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: cfowler
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 948c54a2e9be2260d0a7d2cce31b67ffbbd23d03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione dell'archivio Git locale nel servizio app di Azure

Questa esercitazione illustra come distribuire l'applicazione nelle [app Web di Azure](app-service-web-overview.md) da un repository Git nel computer locale. Il servizio app supporta questo approccio tramite l'opzione di distribuzione **Archivio Git locale** del [portale di Azure].

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari:

* Git. È possibile scaricare il file di installazione binario [qui](http://www.git-scm.com/downloads).
* Conoscenze di base di Git.
* Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial) oppure [attivare i vantaggi per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
>

## <a name="Step1"></a>Passaggio 1: Creare un repository locale

Per creare un nuovo repository Git, eseguire le operazioni seguenti.

1. Avviare uno strumento da riga di comando, ad esempio **Git Bash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.
1. Passare alla directory in cui deve essere collocato il contenuto da distribuire.
1. Eseguire il comando seguente per inizializzare un nuovo repository Git:

  ```bash
  git init
  ```

## <a name="Step2"></a>Passaggio 2: Eseguire il commit del contenuto

Il servizio app supporta applicazioni create in diversi linguaggi di programmazione.

1. Se il repository non include ancora il contenuto, aggiungere un file HTML statico come indicato di seguito oppure ignorare questo passaggio:
   * Usando un editor di testo creare un nuovo file denominato **index.html** nella radice del repository Git
   * Aggiungere il testo seguente come contenuto del file index.html e salvarlo: *Hello Git!*
1. Dalla riga di comando verificare che sia selezionata la radice del repository Git. Usare quindi il comando seguente per aggiungere file al repository:

        git add -A 
1. In seguito, eseguire il commit delle modifiche al repository con il comando seguente:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Passaggio 3: Abilitare il repository dell'app del servizio app

Eseguire la procedura seguente per abilitare un repository Git per l'app del servizio app.

1. Accedere al [portale di Azure].
1. Nel visualizzazione dell'app del servizio app fare clic su **Impostazioni > Origine distribuzione**. Fare clic su **Scegliere l'origine**, quindi su **Repository Git locale** e infine su **OK**.

    ![Repository Git locale](./media/app-service-deploy-local-git/local_git_selection.png)

1. Se si tratta della prima impostazione di un repository in Azure, è necessario creare le credenziali di accesso, che verranno usate per accedere al repository di Azure e per effettuare il push delle modifiche dal repository Git locale. Nella visualizzazione dell'app Web fare clic su **Impostazioni > Credenziali per la distribuzione**e quindi configurare il nome utente e la password per la distribuzione. Al termine, fare clic su **Salva**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Passaggio 4: Distribuire il progetto

Eseguire la procedura seguente per pubblicare l'app nel servizio app usando l'archivio Git locale.

1. Nella visualizzazione dell'app Web nel portale di Azure fare clic su **Impostazioni > Proprietà** per l'**URL GIT**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **URL Git** è il riferimento remoto in cui eseguire la distribuzione dal repository locale. Usare questo URL nei passaggi successivi.
1. Usando la riga di comando verificare che sia selezionata la radice del repository Git locale.
1. Utilizzare `git remote` per aggiungere il riferimento remoto elencato in **URL Git** dal passaggio 1. Il comando è simile al seguente:

    ```bash
    git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git
    ```

   > [!NOTE]
   > Il comando **remote** consente di aggiungere un riferimento denominato a un repository remoto. In questo esempio viene creato un riferimento denominato 'azure' per il repository dell'app Web.
   >

1. Effettuare il push del contenuto nel servizio app usando il nuovo riferimento remoto **azure** creato.

    ```bash
    git push azure master
    ```

    Viene richiesto di inserire la password creata in precedenza durante la reimpostazione delle credenziali di distribuzione nel portale di Azure. Immettere la password. Si noti che Gitbash non ripete gli asterischi nella console mentre si digita la password. 
1. Tornare all'app nel portale di Azure. Nella visualizzazione **Distribuzioni** verrà visualizzata una voce di log dell'ultimo push eseguito.

    ![](./media/app-service-deploy-local-git/deployment_history.png)

1. Fare clic sul pulsante **Sfoglia** nella parte superiore della pagina dell'app Web per verificare che il contenuto sia stato distribuito.

## <a name="Step5"></a>Risoluzione dei problemi

Di seguito sono riportati gli errori o i problemi che si verificano comunemente durante l'uso di Git per la pubblicazione in un'app del servizio app di Azure:

---
**Sintomo**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Causa**: questo errore può verificarsi se l'app non è in esecuzione.

**Soluzione**: avviare l'app nel portale di Azure. Se l'app Web viene arrestata, la distribuzione Git non sarà disponibile.

---
**Sintomo**: `Couldn't resolve host 'hostname'`

**Causa**: questo errore può verificarsi se le informazioni sull'indirizzo immesse durante la creazione del repository remoto 'azure' non sono corrette.

**Soluzione**: usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.

---
**Sintomo**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Causa**: questo errore può verificarsi se non si specifica un ramo durante l'operazione `git push` oppure se non è stato impostato il valore `push.default` in `.gitconfig`.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. Ad esempio: 

```bash
git push azure master
```

---
**Sintomo**: `src refspec [branchname] does not match any.`

**Causa**: questo errore può verificarsi se si tenta di effettuare il push in un ramo diverso dal master nel repository remoto 'azure'.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. Ad esempio: 

```bash
git push azure master
```

---
**Sintomo**: `RPC failed; result=22, HTTP code = 5xx.`

**Causa**: questo errore può verificarsi se si tenta di effettuare il push di un repository Git di grandi dimensioni tramite HTTPS.

**Soluzione**: modificare la configurazione Git nel computer locale per ingrandire il postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**Sintomo**: `Error - Changes committed to remote repository but your web app not updated.`

**Causa**: questo errore può verificarsi se si distribuisce un'app Node.js contenente un file package.json che specifica altri moduli necessari.

**Risoluzione**: i messaggi aggiuntivi contenenti 'npm ERR!' dovrebbero essere registrati prima di questo errore e possono fornire contesto aggiuntivo sul problema. Di seguito sono riportate le cause note di questo errore e del corrispondente messaggio 'npm ERR!' messaggio:

* **File package.json in formato non corretto**: npm ERR! Non è stato possibile leggere le dipendenze.
* **Modulo nativo senza una distribuzione binaria per Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Oppure
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Risorse aggiuntive

* [Documentazione su Git](http://git-scm.com/documentation)
* [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Distribuzione continua nel servizio app di Azure](app-service-continuous-deployment.md)
* [Come usare PowerShell per Azure](/powershell/azure/overview)
* [Come usare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[portale di Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure Command-Line Interface]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
