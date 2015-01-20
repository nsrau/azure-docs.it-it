<properties urlDisplayName="Web App with Express" pageTitle="Applicazione Web con Express (Node.js) - Esercitazione di Azure" metaKeywords="esercitazione di Node.js Hello World di Azure, Node.js Hello World di Azure, esercitazione introduttiva di Node.js di Azure, esercitazione di Node.js di Azure, esercitazione di Node.js con Express di Azure" description="Esercitazione basata sull'esercitazione del servizio cloud e che illustra come usare il modulo Express." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Creazione di un'applicazione Web Node.js usando Express in un servizio cloud di Azure

Node.js include un set minimo di funzionalità nel runtime core. Gli sviluppatori utilizzano spesso moduli di terze parti per fornire funzionalità aggiuntive durante lo sviluppo di un'applicazione Node.js. In questa esercitazione si creerà una nuova applicazione usando il modulo Express che fornisce un framework MVC per la creazione di applicazioni Web Node.js.

Di seguito è riportata una schermata dell'applicazione completata:

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Creazione di un progetto servizio cloud

Eseguire la procedura seguente per creare un nuovo progetto servizio cloud denominato 'expressapp':

1. Nel menu **Start** o nella schermata **Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Passare alla directory **c:\\node** e immettere i comandi seguenti per creare una nuova soluzione denominata **expressapp** e un ruolo Web denominato **WebRole1**:

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [WACOM.NOTE] Per impostazione predefinita, in **Add-AzureNodeWebRole** viene usata una versione precedente di Node.js. La precedente istruzione **Set-AzureServiceProjectRole** indica ad Azure di usare la versione 0.10.21 di Node. 

##Installazione di Express

1. Installare il generatore di Express eseguendo il comando seguente:

		PS C:\node\expressapp> npm install express-generator -g

	L'output del comando npm dovrebbe essere analogo al risultato mostrato di seguito. 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Passare alla directory **WebRole1** e usare il comando express per generare una nuova applicazione:

        PS C:\node\expressapp\WebRole1> express

	Verrà richiesto di sovrascrivere l'applicazione precedente. Immettere **s** o **sì** per continuare. Express genererà il file app.js e una struttura di cartelle per creare l'applicazione.

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Per installare le dipendenze aggiuntive definite nel file package.json, immettere il comando seguente:

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Usare il comando seguente per copiare il file **bin/www** in **server.js**. In questo modo, il servizio cloud potrà trovare il punto di ingresso per questa applicazione.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	Dopo il completamento del comando, nella directory WebRole1 dovrebbe essere presente un file **server.js**.

7.  Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla riga seguente.

		var app = require('../app');

	Dopo questa modifica la riga dovrebbe avere un aspetto analogo al seguente.

		var app = require('./app');

	Questa modifica è obbligatoria perché il file (in precedenza **bin/www**) è stato spostato nella stessa directory del file dell'app richiesto. Al termine di questa modifica, salvare il file **server.js**.

8.  Utilizzare il comando seguente per eseguire l'applicazione nell'emulatore di Windows Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modifica della visualizzazione

Modificare la visualizzazione in modo che il messaggio visualizzato sia "Welcome to Express in
Azure".

1.  Immettere il comando seguente per aprire il file index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade è il motore di visualizzazione predefinito usato dalle applicazioni Express. Per altre informazioni sul motore di visualizzazione Jade, vedere [http://jade-lang.com][].

2.  Modificare l'ultima riga di testo aggiungendo **in Azure**.

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Salvare il file e chiudere il Blocco note.

4.  Aggiornare il browser per visualizzare le modifiche.

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Dopo aver testato l'applicazione, usare il cmdlet **Stop-AzureEmulator** per arrestare l'emulatore.

##Pubblicazione dell'applicazione in Azure

Nella finestra di Azure PowerShell usare il cmdlet **Publish-AzureServiceProject** per distribuire l'applicazione a un servizio cloud.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Al termine dell'operazione di distribuzione, verrà aperto il browser e verrà visualizzata la pagina Web.

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Applicazione Web Node.js]: http://www.windowsazure.com/it-it/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


<!--HONumber=35.2-->
