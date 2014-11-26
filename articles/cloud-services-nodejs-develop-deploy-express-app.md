<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Creazione di un'applicazione Web Node.js usando Express in un servizio cloud di Azure

Node.js include un set minimo di funzionalità nel runtime core. Gli sviluppatori utilizzano spesso moduli di terze parti per fornire funzionalità aggiuntive durante lo sviluppo di un'applicazione Node.js.. In questa esercitazione si creerà una nuova applicazione usando il modulo [Express][Express] che fornisce un framework MVC per la creazione di applicazioni Web Node.js.

Di seguito è riportata una schermata dell'applicazione completata:

![Visualizzazione di Welcome to Express in Azure in un Web browser][Visualizzazione di Welcome to Express in Azure in un Web browser]

## Creazione di un progetto servizio cloud

Eseguire la procedura seguente per creare un nuovo progetto servizio cloud denominato 'expressapp':

1.  Dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

    ![Icona di Azure PowerShell][Icona di Azure PowerShell]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Passare alla directory **c:\\node** e immettere i comandi seguenti per creare una nuova soluzione denominata **expressapp** e un ruolo Web denominato **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

    > [WACOM.NOTE] Per impostazione predefinita, **Add-AzureNodeWebRole** usa una versione precedente di Node.js. L'istruzione **Set-AzureServiceProjectRole** riportata sopra indica ad Azure di usare la versione v0.10.21 di Node.

## Installazione di Express

1.  Installare il generatore di Express eseguendo il comando seguente:

        PS C:\node\expressapp> npm install express-generator -g

    L'output del comando npm dovrebbe essere analogo al risultato mostrato di seguito.

    ![Visualizzazione dell'output del comando npm install express in Windows PowerShell.][Visualizzazione dell'output del comando npm install express in Windows PowerShell.]

2.  Passare alla directory **WebRole1** e usare il comando express per generare una nuova applicazione:

        PS C:\node\expressapp\WebRole1> express

    Verrà richiesto di sovrascrivere l'applicazione precedente. Immettere **y** o **yes** per continuare. Express genererà il file app.js e una struttura di cartelle per creare l'applicazione.

    ![Output del comando express][Output del comando express]

3.  Per installare le dipendenze aggiuntive definite nel file package.json, immettere il comando seguente:

        PS C:\node\expressapp\WebRole1> npm install

    ![Output del comando npm install][Output del comando npm install]

4.  Usare il comando seguente per copiare il file **bin/www** in **server.js**. In questo modo, il servizio cloud potrà trovare il punto di ingresso per questa applicazione.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Dopo il completamento del comando, nella directory WebRole1 dovrebbe essere presente un file **server.js**.

5.  Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla riga seguente.

        var app = require('../app');

    Dopo questa modifica la riga dovrebbe avere un aspetto analogo al seguente.

        var app = require('./app');

    Questa modifica è obbligatoria perché il file (in precedenza **bin/www**) è stato spostato nella stessa directory del file dell'app richiesto. Al termine di questa modifica, salvare il file **server.js**.

6.  Usare il comando seguente per eseguire l'applicazione nell'emulatore di Microsoft Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Una pagina Web contenente Welcome to Express.][Una pagina Web contenente Welcome to Express.]

## Modifica della visualizzazione

Modificare le visualizzazione in modo che il messaggio visualizzato sia "Welcome to Express in Azure".

1.  Immettere il comando seguente per aprire il file index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Contenuto del file index.jade.][Contenuto del file index.jade.]

    Jade è il motore di visualizzazione predefinito utilizzato dalle applicazioni Express. Per altre informazioni sul motore di visualizzazione Jade, vedere [][]<http://jade-lang.com></a>.

2.  Modificare l'ultima riga di testo aggiungendo **in Azure**.

    ![Nell'ultima riga del file index.jade risulta: p Welcome to \#{title} in Azure][Nell'ultima riga del file index.jade risulta: p Welcome to \#{title} in Azure]

3.  Salvare il file e chiudere il Blocco note.

4.  Aggiornare il tuo browser per vedere le modifiche.

    ![Una finestra del browser in cui la pagina contiene Welcome to Express in Azure][Una finestra del browser in cui la pagina contiene Welcome to Express in Azure]

Dopo aver testato l'applicazione, usare il cmdlet **Stop-AzureEmulator** per arrestare l'emulatore.

## Pubblicazione dell'applicazione in Azure

Nella finestra di Azure PowerShell usare il cmdlet **Publish-AzureServiceProject** per distribuire l'applicazione a un servizio di cloud

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Al termine dell'operazione di distribuzione, verrà aperto il browser e verrà visualizzata la pagina Web.

![Visualizzazione della pagina Express in un Web browser. L'URL indica che la pagina ora è ospitata su Azure.][Visualizzazione di Welcome to Express in Azure in un Web browser]

  [Express]: http://expressjs.com/
  [Visualizzazione di Welcome to Express in Azure in un Web browser]: ./media/cloud-services-nodejs-develop-deploy-express-app/node36.png
  [Icona di Azure PowerShell]: ./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png
  [Visualizzazione dell'output del comando npm install express in Windows PowerShell.]: ./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png
  [Output del comando express]: ./media/cloud-services-nodejs-develop-deploy-express-app/node23.png
  [Output del comando npm install]: ./media/cloud-services-nodejs-develop-deploy-express-app/node26.png
  [Una pagina Web contenente Welcome to Express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/node28.png
  [Contenuto del file index.jade.]: ./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png
  []: http://jade-lang.com
  [Una finestra del browser in cui la pagina contiene Welcome to Express in Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node32.png
