---
title: Compilare e distribuire un'app Node.js Express a servizi Cloud di Azure
description: Compilare e distribuire un'applicazione di Express.js in Node.js servizi Cloud di Azure
services: cloud-services
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: befca8d95880936e96d1b3618c172f87499627ed
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Compilare e distribuire un'applicazione web Node.js mediante Express in un Cloud di servizi di Azure

Node.js include un set minimo di funzionalità nel runtime core.
Gli sviluppatori usano spesso moduli di terze parti per fornire funzionalità aggiuntive durante lo sviluppo di un'applicazione Node.js. In questa esercitazione si creerà una nuova applicazione usando il modulo [Express](https://github.com/expressjs/express), che fornisce un framework MVC per la creazione di applicazioni Web Node.js.

Di seguito è riportata una schermata dell'applicazione completata:

![Visualizzazione di Welcome to Express in Azure in un Web browser](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Creazione di un progetto di servizio cloud
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Eseguire la procedura seguente per creare un nuovo progetto di servizio cloud denominato 'expressapp':

1. Nel **menu Start** o nella **schermata Start** cercare **Windows PowerShell**. Fare infine clic con il pulsante destro del mouse su **Windows PowerShell** e scegliere **Esegui come amministratore**.
   
    ![Icona di Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Passare alla directory **c:\\node** e immettere i comandi seguenti per creare una nuova soluzione denominata **expressapp** e un ruolo Web denominato **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Per impostazione predefinita, in **Add-AzureNodeWebRole** viene usata una versione precedente di Node.js. La precedente istruzione **Set-AzureServiceProjectRole** indica ad Azure di usare la versione 0.10.21 di Node.  Si noti che i parametri fanno distinzione tra maiuscole e minuscole.  È possibile verificare di aver selezionato la versione corretta di Node.js controllando la proprietà **engines** in **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Installazione di Express
1. Installare il generatore di Express eseguendo il comando seguente:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    L'output del comando npm dovrebbe essere analogo al risultato mostrato di seguito. 
   
    ![Visualizzazione dell'output del comando npm install express in Windows PowerShell.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Passare alla directory **WebRole1** e usare il comando express per generare una nuova applicazione:
   
        PS C:\node\expressapp\WebRole1> express
   
    Verrà richiesto di sovrascrivere l'applicazione precedente. Immettere **y** o **yes** per continuare. Express genererà il file app.js e una struttura di cartelle per creare l'applicazione.
   
    ![Output del comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Per installare le dipendenze aggiuntive definite nel file package.json, immettere il comando seguente:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Output del comando npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Usare il comando seguente per copiare il file **bin/www** in **server.js**. In questo modo, il servizio cloud potrà trovare il punto di ingresso per questa applicazione.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Dopo il completamento del comando, nella directory WebRole1 dovrebbe essere presente un file **server.js** .
5. Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla riga seguente.
   
       var app = require('../app');
   
   Dopo questa modifica la riga dovrebbe avere un aspetto analogo al seguente.
   
       var app = require('./app');
   
   Questa modifica è obbligatoria perché il file (in precedenza **bin/www**) è stato spostato nella stessa directory del file dell'app richiesto. Al termine di questa modifica, salvare il file **server.js** .
6. Utilizzare il comando seguente per eseguire l'applicazione nell'emulatore di Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Una pagina Web contenente Welcome to Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modifica della visualizzazione
Modificare le visualizzazione in modo che il messaggio visualizzato sia "Welcome to Express in Azure".

1. Immettere il comando seguente per aprire il file index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Contenuto del file index.jade](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade è il motore di visualizzazione predefinito utilizzato dalle applicazioni Express. Per altre informazioni sul motore di visualizzazione Jade, vedere [http://jade-lang.com][http://jade-lang.com].
2. Modificare l'ultima riga di testo aggiungendo **in Azure**.
   
   ![File index.jade che nell'ultima riga riporta: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Salvare il file e chiudere il Blocco note.
4. Aggiornare il tuo browser per vedere le modifiche.
   
   ![Una finestra del browser in cui la pagina contiene Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Dopo aver testato l'applicazione, usare il cmdlet **Stop-AzureEmulator** per arrestare l'emulatore.

## <a name="publishing-the-application-to-azure"></a>Pubblicazione dell'applicazione in Azure
Nella finestra di Azure PowerShell usare il cmdlet **Publish-AzureServiceProject** per distribuire l'applicazione a un servizio di cloud

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Al termine dell'operazione di distribuzione, verrà aperto il browser e verrà visualizzata la pagina Web.

![A web browser displaying the Express page. L'URL indica che la pagina ora è ospitata su Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](/develop/nodejs/).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


