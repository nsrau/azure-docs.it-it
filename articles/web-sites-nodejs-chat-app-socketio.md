<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure

Socket.IO fornisce comunicazioni in tempo reale tra il server node.js e i client usando WebSocket. Supporta inoltre il fallback in altri tipi di trasporto (ad esempio il polling prolungato) che funzionano con browser precedenti. In questa esercitazione verrà illustrato l'hosting di un'applicazione di chat basata su Socket.IO come sito Web di Azure. Per altre informazioni su Socket.IO, vedere [][]<http://socket.io/></a>.

> [WACOM.NOTE] Le procedure descritte in questa attività si applicano ai siti Web di Azure; per i servizi cloud vedere [Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure][Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure].

## <span id="Download"></span></a>Download dell'esempio di chat

Per questo progetto verrà usato l'esempio di chat del [repository GitHub
Socket.IO]. Eseguire la procedura seguente per scaricare l'esempio
e aggiungerlo al progetto creato in precedenza.

1.  Scaricare una [versione archiviata ZIP o GZ][versione archiviata ZIP o GZ] del progetto Socket.IO (per questo documento è stata usata la versione 1.0.6)

2.  Estrarre l'archivio e copiare la directory **examples\\chat**
    in una nuova posizione, ad esempio
    **\\node\\chat**.

## <span id="Modify"></span></a>Modifica di App.js e installazione dei moduli

Prima di distribuire l'applicazione in Azure, è necessario
apportare alcune piccole modifiche.

1.  Rinominare il file **index.js** in **app.js**. Ciò consente ad Azure di rilevare che si tratta di un'applicazione Node.js.

2.  Aprire il file **app.js** nel Blocco note o in un altro editor di testo.

3.  Trovare la sezione **Module dependencies** all'inizio del file app.js e modificare la riga contenente `var io = require('../..')(server);` in `var io = require('socket.io')(server);` come mostrato di seguito:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

Dopo aver salvato le modifiche apportate al file app.js, eseguire la procedura seguente per
installare i moduli necessari:

1.  Dalla riga di comando passare alla directory **\\node\\chat** e usare il comando seguente per installare i moduli necessari per questa applicazione:

        npm install

    Verranno installati i moduli elencati nel file package.json. Dopo
    il completamento del comando, l'output dovrebbe essere simile al
    seguente:

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Poiché l'esempio in origine faceva parte del repository
    GitHub Socket.IO e faceva riferimento direttamente alla libreria Socket.IO mediante
    percorso relativo, a Socket.IO non viene fatto riferimento nel file package.json
    , quindi è necessario installarlo immettendo il comando seguente:

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Nonostante le versioni più recenti di Socket.IO possano funzionare con la procedura descritta in questo articolo, il test è stato effettuato con la versione 1.0.6.

## <span id="Publish"></span></a>Creare un sito Web di Azure

Per creare un sito Web di Azure, abilitare la pubblicazione Git e quindi abilitare il supporto WebSocket per il sito Web, attenersi alla procedura seguente.

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

1.  Dalla riga di comando passare alla directory **\\node\\chat** e usare il comando seguente per creare un nuovo sito Web di Azure e abilitare un repository Git per il sito Web e la directory locale. Verrà inoltre creato un repository Git remoto denominato 'azure'.

        azure site create mysitename --git

    È necessario sostituire "nomesito" con un nome univoco per il sito Web.

2.  Eseguire il commit dei file esistenti nel repository locale usando i comandi seguenti:

        git add .
        git commit -m "Initial commit"

3.  Effettuare il push dei file nel repository del sito Web di Azure con il comando seguente:

        git push azure master

    Durante le operazioni di importazione dei moduli nel server, verranno visualizzati messaggi di stato. Al termine del processo, l'applicazione sarà ospitata nel sito Web di Azure.

    > [WACOM.NOTE] Durante l'installazione del modulo, è possibile notare errori di tipo 'Il progetto importato... non è stato trovato'. Tali errori possono essere ignorati.

4.  Socket.IO utilizza i WebSocket che non sono abilitati per impostazione predefinita in Azure. Per abilitare i WebSocket, usare il comando seguente:

        azure site set -w

    Se richiesto, immettere il nome del sito Web.

    > [WACOM.NOTE]
    > Il comando 'azure site set -w' funziona solo con la versione 0.7.4 o successiva dell'interfaccia della riga di comando multipiattaforma di Azure. Per abilitare il supporto WebSocket, è anche possibile usare il portale di gestione di Azure.
    >
    > Per abilitare WebSocket tramite il [portale di gestione di Azure][portale di gestione di Azure], selezionare la pagina Configura per il sito Web, selezionare ATTIVATO per la voce Web Socket e quindi fare clic su Salva.
    >
    > ![websockets][websockets]

5.  Per visualizzare il sito Web in Azure, usare il comando seguente per lanciare il Web browser e passare al sito Web ospitato:

        azure site browse

L'applicazione è ora in esecuzione in Azure ed è in grado di inoltrare i messaggi di chat
tra diversi client tramite Socket.IO.

> [WACOM.NOTE] Per semplicità, in questo esempio viene illustrata solo la chat tra utenti connessi alla stessa istanza. Questo significa che se il servizio cloud crea due istanze del ruolo di lavoro, gli utenti saranno in grado di comunicare solo con altri connessi alla stessa istanza. Per scalare l'applicazione in modo da usare più istanze del ruolo, è possibile usare una tecnologia come il bus di servizio per condividere lo stato dell'archivio Socket.IO tra le istanze. Per alcuni esempi, vedere le apposite sezioni relative a code e argomenti del bus di servizio nel [repository GitHub di Azure SDK per Node.js][repository GitHub di Azure SDK per Node.js].

## Scalabilità orizzontale

Le applicazioni Socket.IO possono essere scalate usando un **adattatore** per distribuire messaggi ed eventi tra più istanze di applicazioni. Nonostante siano disponibili diversi adattatori, l'adattatore [socket.io-redis][socket.io-redis] può essere usato facilmente con la funzionalità Cache Redis di Azure.

> [WACOM.NOTE] Un ulteriore requisiti per la scalabilità orizzontale di una soluzione Socket.IO è il supporto di sessioni permanenti. Le sessioni permanenti sono abilitate per impostazione predefinita per i siti Web di Azure tramite il routing delle richieste di Azure. Per altre informazioni, vedere il blog relativo all'[affinità delle istanze in Siti Web di Azure][affinità delle istanze in Siti Web di Azure]

### Creare una cache Redis

Eseguire la procedura descritta in [Creare una cache in Cache Redis di Azure][Creare una cache in Cache Redis di Azure] per creare una nuova cache.

> [WACOM.NOTE] Salvare il **Nome host** e la **Chiave primaria** per la cache, in quanto saranno necessari nei prossimi passaggi.

### Aggiungere i moduli redis e socket.io-redis

1.  Dalla riga di comando, passare alla directory **\\node\\chat** e usare il comando seguente.

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] Le versioni specificate in questo comando sono le stesse versioni usate nei test di questo articolo.

2.  Modificare il file **app.js** per aggiungere le righe seguenti immediatamente dopo `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Sostituire **redishostname** e **rediskey** con il nome host e la chiave per la cache Redis.

    In questo modo viene creato un client di pubblicazione e sottoscrizione alla cache Redis creata in precedenza. I client vengono quindi usati con l'adattatore per configurare Socket.IO in modo da usare la cache Redis per passare messaggi ed eventi tra le istanze dell'applicazione

    > [WACOM.NOTE] Nonostante l'adattatore **socket.io-redis** possa comunicare direttamente con Redis, la versione corrente (alla data del 14 luglio 2014) non supporta l'autenticazione richiesta dalla Cache Redis di Azure. Pertanto, la connessione iniziale viene creata usando il modulo **redis**, quindi il client viene passato all'adattatore **socket.io-redis**.
    >
    > Nonostante Cache Redis di Azure supporti le connessioni sicure usando la porta 6380, alla data del 14 luglio 2014 i moduli usati in questo esempio non supportano le connessioni sicure. Il codice riportato sopra usa la porta 6380, predefinita e non sicura.

3.  Salvare il file **app.js** modificato

### Eseguire il commit delle modifiche ed effettuare la ridistribuzione

Dalla riga di comando nella directory **\\node\\chat**, usare i comandi seguenti per eseguire il commit delle modifiche e ridistribuire l'applicazione.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Dopo aver effettuato il push delle modifiche al server, è possibile scalare il sito tra più istanze tramite il comando seguente.

    azure site scale instances --instances #

Dove **\#** è il numero di istanze da creare.

È possibile effettuare la connessione al sito Web da più browser o computer per verificare che i messaggi siano inviati correttamente a tutti i client.

## <span id="tshooting"></span></a>Risoluzione dei problemi

### Limiti di connessione

Siti Web di Azure è disponibile in più SKU, che determinano le risorse disponibili per il sito, incluso il numero di connessioni WebSocket consentite. Per altre informazioni, vedere la [pagina Dettagli dei prezzi di Siti Web][pagina Dettagli dei prezzi di Siti Web].

### I messaggi non vengono inviati usando WebSocket

Se i browser client continuano a eseguire il fallback al polling prolungato invece di usare WebSocket, il motivo potrebbe essere uno dei seguenti.

-   **Tentare di limitare il trasporto ai soli WebSocket**

    Affinché Socket.IO possa usare WebSocket per il trasporto dei messaggi, sia il server che il client devono supportare WebSocket. Diversamente, Socket.IO negozierà un altro tipo di trasporto, come il polling prolungato. L'elenco predefinito dei tipi di trasporto usati da Socket.IO è `websocket, htmlfile, xhr-polling, jsonp-polling`. È possibile forzarlo in modo da usare solo WebSocket aggiungendo il codice seguente al file **app.js**, dopo la riga contenente `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Si noti che i browser precedenti che non supportano WebSocket non potranno effettuare la connessione al sito mentre il codice riportato sopra è attivo, in quanto quest'ultimo limita la comunicazione ai soli WebSocket.

-   **Usa SSL**

    WebSocket si basa su intestazioni HTTP usate con minore frequenza, come l'intestazione **Upgrade**. Alcuni dispositivi di rete intermedi, come i proxy Web, possono rimuovere tali intestazioni. Per evitare il problema, è possibile stabilire la connessione WebSocket su SSL.

    È possibile effettuare facilmente questa operazione configurando Socket.IO su `match origin protocol`. Questa configurazione fornisce a Socket.IO l'istruzione di proteggere la comunicazione WebSocket nello stesso modo della richiesta HTTP/HTTPS di origine per la pagina Web. Se un browser usa un URL HTTPS per visitare il sito Web, le comunicazioni WebSocket successive tramite Socket.IO verranno protette su SSL.

    Per modificare questo esempio in modo da abilitare questa configurazione, aggiungere il codice seguente al file **app.js** dopo la riga contenente `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Verificare le impostazioni del ile web.config**

    I siti Web di Azure che ospitano l'applicazione Node.js host usano il file **web.config** per eseguire il routing delle richieste in ingresso all'applicazione Node.js. Affinché i WebSocket funzionino correttamente con le applicazioni Node.js, il file **web.config** deve contenere la seguente voce.

        <webSocket enabled="false"/>

    In questo modo viene disabilitato il modulo IIS WebSocket, che include la propria distribuzione di WebSocket ed è in conflitto con i moduli WebSocket specifici di Node.js come Socket.IO. L'assenza di questa riga o la relativa impostazione su `true` potrebbe essere il motivo per il quale il trasporto WebSocket non funziona per l'applicazione.

    Di norma le applicazioni Node.js non includono un file **web.config**, pertanto Siti Web di Azure ne genererà automaticamente uno per le applicazioni Node.js quando queste vengono distribuite. Poiché questo file viene generato automaticamente sul server, per visualizzarlo è necessario usare l'URL FTP o FTPS per il sito Web. È possibile trovare gli URL FTP e FTPS per il sito nel portale di gestione di Azure selezionando il sito Web e quindi il collegamento **Dashboard**. Gli URL vengono visualizzati nella sezione **Riepilogo rapido**.

    > [WACOM.NOTE] Il file **web.config** viene generato da Siti Web di Azure solo se non viene fornito dall'applicazione. Se si fornisce un file **web.config** nella radice del progetto dell'applicazione, tale file verrà usato da Siti Web di Azure.

    Se la voce non è presente, o è impostata su un valore di `true`, è necessario creare un file **web.config** nella radice dell'applicazione Node.js e specificare un valore di `false`. Di seguito viene fornito, come riferimento, un file **web.config** predefinito per un'applicazione che usa **app.js** come punto di ingresso.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] Se l'applicazione usa un punto di ingresso diverso da **app.js**, è necessario sostituire tutte le occorrenze di **app.js** con il punto di ingresso corretto, ad esempio, la sostituzione di **app.js** con **server.js**.

## Passaggi successivi

In questa esercitazione è stato illustrato come creare un'applicazione di chat di base ospitata in un sito Web di Azure. È inoltre possibile ospitare l'applicazione come un servizio cloud di Azure. Per le procedure che illustrano come eseguire questa operazione, vedere [Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure][1].

  []: http://socket.io/
  [Creazione di un'applicazione di chat Node.js con Socket.IO in un servizio cloud di Azure]: http://www.windowsazure.com/it-it/develop/nodejs/tutorials/app-using-socketio/
  [versione archiviata ZIP o GZ]: https://github.com/Automattic/socket.io/releases
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [repository GitHub di Azure SDK per Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [affinità delle istanze in Siti Web di Azure]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Creare una cache in Cache Redis di Azure]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [pagina Dettagli dei prezzi di Siti Web]: /it-it/pricing/details/web-sites/
  [1]: /it-it/develop/nodejs/tutorials/app-using-socketio/
