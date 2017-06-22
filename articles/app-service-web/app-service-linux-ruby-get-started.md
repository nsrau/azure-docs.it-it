---
title: Creare un&quot;app Ruby con l&quot;App Web del servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come creare app Ruby con l&quot;App Web del servizio app di Azure in Linux.
keywords: Servizio app di Azure, Linux, OSS
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Creare un'app Ruby con App Web di Azure in Linux:anteprima

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come creare un'applicazione Ruby on Rails di base in locale e distribuirla in App Web di Azure in Linux.

## <a name="prerequisites"></a>Prerequisiti

* [Ruby 2.3.3 o versione successiva](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller) installato nel computer di sviluppo.
* [Git](https://git-scm.com/downloads) installato nel computer di sviluppo
* Una [sottoscrizione di Azure attiva](https://azure.microsoft.com/pricing/free-trial/)
* Questa esercitazione è scritta nel contesto di un ambiente Ubuntu. Tutti i comandi di sistema sono specifici per bash.


## <a name="create-a-new-local-rails-application"></a>Creare una nuova applicazione Rails locale

1. Creare una directory per la nuova app e passare a tale directory.

        mkdir ~/workspace
        cd ~/workspace

2. Inizializzare Ruby e verificare che la versione usi il comando `ruby -v`.

    ![Init di Ruby](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Installare Rails usando il comando `gem install rails`.

    ![Installare Rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Creare un'applicazione Rails denominata **hello-world** usando il comando seguente:

    Se si usa Rails 5.1.0 o versioni successive, includere l'opzione `--skip-yarn` come mostrato nel comando seguente:

        rails new hello-world --skip-yarn

    Per le versioni di Rails precedenti alla 5.1.0, usare il comando seguente:

        rails new hello-world 

    ![Nuovo Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![Nuovo Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Se si usano le versioni di Rails 5.1 e successive, viene creato package.json se l'opzione `--skip-yarn` non viene usata. Non si desidera includerla nella distribuzione. In alternativa, è possibile eliminare il file package.json o aggiungerlo al file *.git-ignore* nella directory come segue: 

        # Ignore package.json
        /package.json

5. Passare alla directory *hello-world* e avviare il server.

        cd hello-world
        rails server

    ![Avviare Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. Tramite il Web browser, passare a `http://localhost:3000` per testare l'app in locale.    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Preparare l'app per Azure

Per impostazione predefinita, l'immagine di Ruby esegue il server con il flag `-e production`. Questo ambiente richiede alcune configurazioni per App Web di Azure in Linux. Il contenitore si occupa di alcune configurazioni, ad esempio impostando `SECRET_KEY_BASE`. È necessario configurare una route predefinita. In caso contrario, esplorando il sito viene visualizzato un errore 404.

Per configurare una route predefinita:

1. Aprire *~/workspace/hello-world/config/routes.rb* per la modifica. Aggiungere la riga seguente come illustrato nella schermata. 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. Aprire *~/workspace/hello-world/app/controllers/application_controller.rb* per la modifica. Aggiungere le righe seguenti come illustrato nella schermata.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. Ora l'app è configurata. Tramite il Web browser, passare a `http://localhost:3000` per confermare la pagina di destinazione principale.

    ![Hello World configurato](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Creare un sito Web di Ruby in Azure

1. Passare al [portale di Azure](http://portal.azure.com) ed eseguire l'accesso con la sottoscrizione. Aggiungere un'**App Web su Linux** come illustrato nella schermata seguente:

    ![Creare un'App Web in Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. Viene quindi aperto il **pannello Crea**, come illustrato nell'immagine seguente:

    ![Pannello Crea](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Assegnare all'app Web un nome.
    2. Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Vedere le aree disponibili nella [sezione sulle limitazioni](app-service-linux-intro.md).
    3. Scegliere un piano di servizio app di Azure esistente o crearne uno nuovo. Vedere le note sul piano di servizio app nella [sezione sulle limitazioni](app-service-linux-intro.md).
    4. Scegliere lo stack di runtime predefinito **Ruby 2.3** per la configurazione del contenitore.
    5. Fare clic su **Aggiungi al dashboard** per l'app Web.
    6. Fare clic su **Crea**.

3. Dopo aver creato l'app Web, viene visualizzato il pannello **Panoramica**. Copiare l'**URL** per la nuova app Web e aprirlo nel browser. Viene visualizzata la pagina iniziale seguente.

    ![pagina iniziale](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Distribuire l'applicazione

In questa esercitazione, viene usato Git per distribuire l'applicazione locale Ruby in Azure.

1. Il nuovo sito Web di Azure ha già una distribuzione Git configurata. È possibile trovare l'URL di distribuzione Git passando all'URL seguente dopo l'inserimento del nome dell'app Web:

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    L'URL Git ha il formato seguente, in base al nome dell'app Web:

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. Eseguire i comandi seguenti per distribuire l'applicazione locale al sito Web di Azure.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    Verificare che per le operazioni di distribuzione in remoto venga segnalato l'esito positivo.

    ![Distribuzione dell'app Web](./media/app-service-linux-ruby-get-started/deployment-success.png)

    Se viene visualizzato un errore che informa che il computer remoto presenta dei problemi, è probabile che la distribuzione sia ancora in corso. In questo caso, passare all'URL seguente nel browser:

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. Dopo aver completato la distribuzione, riavviare l'app Web affinché la distribuzione venga applicata. Nel [portale di Azure](http://portal.azure.com) passare al pannello **Panoramica** dell'app Web.

    Fare clic su **Riavvia** nella barra degli strumenti.

    ![Riavviare l'app Web](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. Passare al sito e verificare che gli aggiornamenti siano attivi. 

    Mentre l'app viene riavviata, il tentativo di aprire il sito genera un errore HTTP con codice di stato 503, Server non disponibile. Il completamento del riavvio potrebbe richiedere alcuni minuti.

        http://{your web app name}.azurewebsites.net

    ![app Web aggiornata](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su App Web del Servizio app di Azure in Linux, vedere i collegamenti seguenti. È anche possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creating Apps in an App Service on Linux](app-service-linux-how-to-create-web-app.md) (Creazione di app Web nel servizio app in Linux)
* [Come usare un'immagine Docker personalizzata per il servizio app in Linux](app-service-linux-using-custom-docker-image.md)
* [Uso della configurazione PM2 per Node.js nelle app Web in Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core nelle app Web del servizio App di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)


