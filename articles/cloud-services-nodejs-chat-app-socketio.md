<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="App Using Socket.IO" pageTitle="Node.js application using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js application hosted on Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure

Socket.IO fornisce comunicazioni in tempo reale tra il server node.js e i client. Questa esercitazione illustra l'hosting di un'applicazione di chat basata su socket.IO in Azure. Per altre informazioni su Socket.IO, vedere <http://socket.io/>.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con il servizio ospitato in Azure][Finestra del browser con il servizio ospitato in Azure]

## Creazione di un progetto servizio cloud

Eseguire le operazioni seguenti per creare il progetto servizio cloud che ospiterà l'applicazione Socket.IO.

1.  Dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

    ![Icona di Azure PowerShell][Icona di Azure PowerShell]

    [WACOM.INCLUDE [install-dev-tools][install-dev-tools]]

2.  Passare alla directory **c:\\node** e immettere i comandi seguenti per creare una nuova soluzione denominata **chatapp** e un ruolo Web denominato **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Verrà visualizzata la risposta seguente:

    ![Output dei cmdlet new-azureservice e add-azurenodeworkerrole][Output dei cmdlet new-azureservice e add-azurenodeworkerrole]

## Download dell'esempio di chat

Per questo progetto verrà usato l'esempio di chat del [repository GitHub Socket.IO][repository GitHub Socket.IO]. Eseguire la procedura seguente per scaricare l'esempio
e aggiungerlo al progetto creato in precedenza.

1.  Creare una copia locale del repository usando il pulsante **Clone**. È inoltre possibile usare il pulsante **ZIP** per scaricare il progetto.

    ![Finestra del browser con https://github.com/LearnBoost/socket.io/tree/master/examples/chat e l'icona per il download di ZIP evidenziata][Finestra del browser con https://github.com/LearnBoost/socket.io/tree/master/examples/chat e l'icona per il download di ZIP evidenziata]

2.  Passare alla struttura della directory del repository locale fino a individuare la directory **examples\\chat**.     Copiare il contenuto di questa directory nella directory    **C:\\node\\chatapp\\WorkerRole1** creata in precedenza.

    ![Esplora risorse con il contenuto della directory examples\\chat estratto dall'archivio]

    Gli elementi evidenziati nella schermata precedente sono i file copiati dalla directory **examples\\chat**.

3.  Nella directory **C:\\node\\chatapp\\WorkerRole1** eliminare il file **server.js** e rinominare il file **app.js** in **server.js**. Il file **server.js** predefinito creato in precedenza dal cmdlet **Add-AzureNodeWorkerRole** viene rimosso e sostituito dal file dell'applicazione dell'esempio di chat.

### Modificare Server.js e installare i moduli

Prima di testare l'applicazione nell'emulatore di Azure, è necessario apportare alcune piccole modifiche. Eseguire la procedura seguente per il file server.js:

1.  Aprire il file server.js nel Blocco note o in un altro editor di testo.

2.  Individuare la sezione **Module dependencies** all'inizio del file server.js e sostituire la riga contenente **sio = require('..//..//lib//socket.io')** con **sio = require('socket.io')**, come illustrato di seguito:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Per assicurarsi che l'applicazione resti in ascolto sulla porta corretta, aprire    il file server.js nel Blocco note o in un altro editor di testo e modificare la    riga seguente sostituendo **3000** con **process.env.port**, come illustrato di seguito:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Dopo aver salvato le modifiche apportate al file server.js, eseguire la procedura seguente per installare i moduli necessari, quindi testare l'applicazione nell' emulatore di Azure:

1.  Tramite **Azure PowerShell** passare alla directory **C:\\node\\chatapp\\WorkerRole1** e usare il comando seguente per installare i moduli necessari per l'applicazione:

        PS C:\node\chatapp\WorkerRole1> npm install

    Verranno installati i moduli elencati nel file package.json. Dopo    il completamento del comando, l'output dovrebbe essere simile al    seguente:

    ![Output del comando npm install][Output del comando npm install]

2.  Poiché l'esempio in origine faceva parte del repository    GitHub Socket.IO e faceva riferimento direttamente alla libreria Socket.IO mediante    percorso relativo, a Socket.IO non viene fatto riferimento nel file package.json    , quindi è necessario installarlo immettendo il comando seguente:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Test e distribuzione

1.  Avviare l'emulatore immettendo il comando seguente:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Quando si apre la finestra del browser, immettere un nome alternativo e premere INVIO.    In questo modo sarà possibile inviare messaggi con un nome alternativo specifico. Per testare    la funzionalità multiutente, aprire altre finestre del browser usando lo    stesso URL e immettere nomi alternativi diversi.

    ![Due finestre del browser con i messaggi della chat di User1 e User2][Due finestre del browser con i messaggi della chat di User1 e User2]

3.  Dopo aver testato l'applicazione, immettere il comando seguente per
    interrompere l'emulatore:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Per distribuire l'applicazione in Azure, usare il cmdlet
    **Publish-AzureServiceProject**, Ad esempio:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">
<strong>Nota</strong>
<p>Assicurarsi di usare un nome univoco, in caso contrario il processo di pubblicazione avr&agrave; esito negativo. Al termine della distribuzione, verr&agrave; visualizzata una finestra del browser che consente di passare al servizio distribuito.</p>
<p>Se viene visualizzato un messaggio di errore indicante che il nome della sottoscrizione specificato non esiste nel profilo di pubblicazione importato, &egrave; necessario scaricare e importare il profilo di pubblicazione per la sottoscrizione prima della distribuzione in Azure. Vedere la sezione <b>Distribuzione dell'applicazione in Azure</b> dell'argomento <a href="https://www.windowsazure.com/it-it/develop/nodejs/tutorials/getting-started/">Creazione e distribuzione di un'applicazione Node.js in un servizio cloud di Azure</a></p>
</div>

    ![Finestra del browser con il servizio ospitato in Azure][Finestra del browser con il servizio ospitato in Azure]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Se viene visualizzato un messaggio di errore indicante che il nome della sottoscrizione specificato non esiste nel profilo di pubblicazione importato, &egrave; necessario scaricare e importare il profilo di pubblicazione per la sottoscrizione prima della distribuzione in Azure. Vedere la sezione <b>Distribuzione dell'applicazione in Azure</b> dell'argomento <a href="https://www.windowsazure.com/it-it/develop/nodejs/tutorials/getting-started/">Creazione e distribuzione di un'applicazione Node.js in un servizio cloud di Azure</a></p>
</div>

L'applicazione è ora in esecuzione in Azure ed è in grado di inoltrare i messaggi di chat tra diversi client tramite Socket.IO.

<div class="dev-callout">
<strong>Nota</strong>
<p>Per semplicit&agrave;, in questo esempio viene illustrata solo la chat tra utenti connessi alla stessa istanza. Questo significa che se il servizio cloud crea due istanze del ruolo di lavoro, gli utenti saranno in grado di comunicare solo con altri connessi alla stessa istanza. Per scalare l'applicazione in modo da usare pi&ugrave; istanze del ruolo, &egrave; possibile usare una tecnologia come il bus di servizio per condividere lo stato dell'archivio Socket.IO tra le istanze. Per alcuni esempi, vedere le apposite sezioni relative a code e argomenti del bus di servizio nel <a href="https://github.com/WindowsAzure/azure-sdk-for-node">repository GitHub di Azure SDK per Node.js</a>.</p>
</div>

## Passaggi successivi

In questa esercitazione è stato illustrato come creare un'applicazione di chat di base ospitata in un servizio cloud di Azure. Per informazioni su come ospitare questa applicazione in un sito Web di Azure, vedere [Creazione di un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure][Creazione di un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure].

  [Finestra del browser con il servizio ospitato in Azure]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Icona di Azure PowerShell]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md
  [Output dei cmdlet new-azureservice e add-azurenodeworkerrole]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [repository GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Finestra del browser con https://github.com/LearnBoost/socket.io/tree/master/examples/chat e l'icona per il download di ZIP evidenziata]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [Esplora risorse con il contenuto della directory examples\\chat estratto dall'archivio]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [Output del comando npm install]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Due finestre del browser con i messaggi della chat di User1 e User2]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Creazione e distribuzione di un'applicazione Node.js in un servizio cloud di Azure]: https://www.windowsazure.com/it-it/develop/nodejs/tutorials/getting-started/
  [repository GitHub di Azure SDK per Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Creazione di un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure]: /it-it/develop/nodejs/tutorials/website-using-socketio/
