<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

Creazione di un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure
==================================================================================

Socket.IO fornisce comunicazioni in tempo reale tra il server node.js e i client. In questa esercitazione verrà illustrato l'hosting di un'applicazione di chat basata su socket.IO come sito Web di Azure. Per ulteriori informazioni su Socket.IO, vedere <http://socket.io/>.

**Nota**

Le procedure descritte in questa attività si applicano a Siti Web di Azure. Per Servizi cloud, vedere [Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure](http://www.windowsazure.com/it-it/develop/nodejs/tutorials/app-using-socketio/).

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con l'applicazione di chat](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

Download dell'esempio di chat
-----------------------------

Per questo progetto, verrà utilizzato l'esempio di chat dell'[archivio GitHub Socket.IO](https://github.com/LearnBoost/socket.io/tree/0.9.14). Eseguire la procedura seguente per scaricare l'esempio e aggiungerlo al progetto creato in precedenza.

1.  Creare una copia locale dell'archivio utilizzando il pulsante **Clone**. È inoltre possibile utilizzare il pulsante **ZIP** per scaricare il progetto.

    ![Finestra del browser con https://github.com/LearnBoost/socket.io/tree/master/examples/chat e l'icona per il download di ZIP evidenziata](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  Passare alla struttura della directory dell'archivio locale fino a individuare la directory **examples\\chat**. Copiare il contenuto di questa directory in una directory separata, ad esempio **\\node\\chat**.

Modifica di App.js e installazione dei moduli
---------------------------------------------

Prima distribuire l'applicazione in Azure, è necessario apportare alcune piccole modifiche. Eseguire la procedura seguente per il file app.js:

1.  Aprire il file app.js nel Blocco note o in un altro editor di testo.

2.  Individuare la sezione **Module dependencies** all'inizio del file app.js e sostituire la riga contenente **sio = require('..//..//lib//socket.io')** con **sio = require('socket.io')**, come illustrato di seguito:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Per assicurarsi che l'applicazione resti in ascolto sulla porta corretta, aprire il file app.js nel Blocco note o in un altro editor di testo e modificare la riga seguente sostituendo **3000** con **process.env.PORT**, come illustrato di seguito:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.PORT, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Dopo aver salvato le modifiche apportate al file APP.js, eseguire la procedura seguente per installare i moduli necessari:

1.  Dalla riga di comando passare alla directory **\\node\\chat** e utilizzare il comando seguente per installare i moduli necessari per questa applicazione:

        npm install

    Verranno installati i moduli elencati nel file package.json. Dopo il completamento del comando, l'output dovrebbe essere simile al seguente:

    ![Output del comando npm install](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  Poiché l'esempio in origine faceva parte dell'archivio GitHub Socket.IO e faceva riferimento direttamente alla libreria Socket.IO mediante percorso relativo, a Socket.IO non viene fatto riferimento nel file package.json, quindi è necessario installarlo immettendo il comando seguente:

        npm install socket.io -save

Creazione di un sito Web di Azure e abilitazione della pubblicazione Git
------------------------------------------------------------------------

Per creare un sito Web di Azure e quindi abilitare la pubblicazione Git, attenersi alla procedura seguente.

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Dalla riga di comando passare alla directory **\\node\\chat** e utilizzare il comando seguente per creare un nuovo sito Web di Azure e abilitare un archivio Git per il sito Web e la directory locale. Verrà inoltre creato un archivio Git remoto denominato 'azure'.

         azure site create mysitename --git

    Sostituire "mysitename" con un nome univoco per il sito Web.

2.  Eseguire il commit dei file esistenti nell'archivio locale utilizzando i comandi seguenti:

         git add .
         git commit -m "Initial commit"

3.  Effettuare il push dei file nell'archivio del sito Web di Azure con il comando seguente:

         git push azure master

    Durante le operazioni di importazione dei moduli nel server, verranno visualizzati messaggi di stato. Al termine del processo, l'applicazione sarà ospitata nel sito Web di Azure.



    <b>Nota</b>
    <p>Durante l'installazione del modulo, è possibile notare errori di tipo 'Il progetto importato... non è stato trovato'. Tali errori possono essere ignorati.</p>


1.  Socket.IO utilizza i WebSocket che non sono abilitati per impostazione predefinita in Azure. Per abilitare i WebSocket, utilizzare il comando seguente:

         azure site set -w

    Se richiesto, immettere il nome del sito Web.

    > [WACOM.NOTE] Il comando 'azure site set -w' funziona solo con la versione 0.7.4 o successiva dell'interfaccia della riga di comando multipiattaforma di Azure. Per abilitare il supporto WebSocket, è anche possibile utilizzare il portale di gestione di Azure.
    >
    > Per abilitare WebSocket tramite il [portale di gestione di Azure](https://manage.windowsazure.com), selezionare la pagina Configure per il sito Web, selezionare 'ON' per la voce Web Sockets e quindi fare clic su Save.
    >
    > ![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  Per visualizzare il sito Web in Azure, utilizzare il comando seguente per avviare il Web browser e passare al sito Web ospitato.

         azure site browse

L'applicazione è ora in esecuzione in Azure ed è in grado di inoltrare i messaggi di chat tra diversi client tramite Socket.IO.

**Nota**

Per semplicità, in questo esempio viene illustrata solo la chat tra utenti connessi alla stessa istanza. Questo significa che se il servizio cloud crea due istanze del ruolo di lavoro, gli utenti saranno in grado di comunicare solo con altri connessi alla stessa istanza. Per scalare l'applicazione in modo da utilizzare più istanze del ruolo, è possibile utilizzare una tecnologia come il bus di servizio per condividere lo stato dell'archivio Socket.IO tra le istanze. Per alcuni esempi, vedere le apposite sezioni relative a code e argomenti del bus di servizio nell'[archivio GitHub di Azure SDK per Node.js](https://github.com/WindowsAzure/azure-sdk-for-node).

Passaggi successivi
-------------------

In questa esercitazione è stato illustrato come creare un'applicazione di chat di base ospitata in un sito Web di Azure. È inoltre possibile ospitare l'applicazione come un servizio cloud di Azure. Per le procedure che illustrano come eseguire questa operazione, vedere [Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure](/en-us/develop/nodejs/tutorials/app-using-socketio/).

