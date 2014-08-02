<properties  linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="" solutions="" manager="" editor="" />

# Creazione di un'applicazione Web Node.js utilizzando Express in un servizio cloud di Azure

Node.js include un set minimo di funzionalità nel runtime core. Gli sviluppatori utilizzano spesso moduli di terze parti per fornire funzionalità aggiuntive durante lo sviluppo di un'applicazione Node.js. In questa esercitazione si creerà una nuova applicazione utilizzando il modulo [Express][1] che fornisce un framework MVC per la creazione di applicazioni Web Node.js.

Di seguito è riportata una schermata dell'applicazione completata:

![Visualizzazione di Welcome to Express in Azure in un Web
browser](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Creazione di un progetto di servizio cloud

Eseguire la procedura seguente per creare un nuovo progetto di servizio cloud denominato 'expressapp':

1.  Dal **menu Start** o dalla **schermata Start** cercare **Azure
    PowerShell**. Fare infine clic con il pulsante destro del mouse su
    **Azure PowerShell** e scegliere **Esegui come amministratore**.
    
    ![Icona di Azure
    PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
    
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Passare alla directory **c:\node** e immettere i comandi seguenti
    per creare una nuova soluzione denominata **expressapp** e un ruolo
    Web denominato **WebRole1**:
    
         PS C:\node> New-AzureServiceProject expressapp
         PS C:\Node> Add-AzureNodeWebRole

## Installazione di Express

1.  Installare il modulo Express eseguendo il comando seguente:
    
         PS C:\node\expressapp> npm install express -g
    
    L'output del comando npm dovrebbe essere analogo al risultato
    mostrato di seguito.
    
    ![Visualizzazione dell'output del comando npm install express in
    Windows
    PowerShell.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2.  Passare alla directory **WebRole1** e utilizzare il comando express
    per generare una nuova applicazione:
    
         PS C:\node\expressapp\WebRole1> express
    
    Verrà richiesto di sovrascrivere l'applicazione precedente.
    Immettere **y** o **yes** per continuare. Express genererà il file
    app.js e una struttura di cartelle per creare l'applicazione.
    
    ![Output del comando
    express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  Eliminare il file **server.js** e rinominare il file **app.js**
    generato in **server.js**.
    
        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

4.  Per installare le dipendenze aggiuntive definite nel file
    package.json, immettere il comando seguente:
    
        PS C:\node\expressapp\WebRole1> npm install
    
    ![Output del comando npm
    install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

5.  Utilizzare il comando seguente per eseguire l'applicazione
    nell'emulatore di Windows Azure:
    
        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    
    ![Una pagina Web contenente Welcome to
    Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modifica della visualizzazione

Modificare le visualizzazione in modo che il messaggio visualizzato sia
"Welcome to Express in Azure".

1.  Immettere il comando seguente per aprire il file index.jade:
    
        PS C:\node\expressapp\WebRole1> notepad views/index.jade
    
    ![Contenuto del file
    index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
    
    Jade è il motore di visualizzazione predefinito utilizzato dalle
    applicazioni Express. Per ulteriori informazioni sul motore di
    visualizzazione Jade, vedere [http://jade-lang.com][2].

2.  Modificare l'ultima riga di testo aggiungendo **in Azure**.
    
    ![Nell'ultima riga del file index.jade risulta: p Welcome to
    \#{title} in
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Salvare il file e chiudere il Blocco note.

4.  Aggiornare il tuo browser per vedere le modifiche.
    
    ![Una finestra del browser in cui la pagina contiene Welcome to
    Express in
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Dopo aver testato l'applicazione, utilizzare il cmdlet
**Stop-AzureEmulator** per arrestare l'emulatore.

## Pubblicazione dell'applicazione in Azure

Nella finestra di Azure PowerShell utilizzare il cmdlet
**Publish-AzureServiceProject** per distribuire l'applicazione a un
servizio di cloud

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Al termine dell'operazione di distribuzione, verrà aperto il browser e verrà visualizzata la pagina Web.

![Visualizzazione della pagina Express in un Web browser. L'URL indica
che la pagina ora è ospitata su Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)



[1]: http://expressjs.com/
[2]: http://jade-lang.com