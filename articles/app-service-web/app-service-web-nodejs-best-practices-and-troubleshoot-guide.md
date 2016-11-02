<properties
	pageTitle="Procedure consigliate e guida alla risoluzione dei problemi per le applicazioni Node in App Web di Azure"
	description="Informazioni sulle procedure consigliate e sulle procedure per la risoluzione dei problemi delle applicazioni Node in App Web di Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="ranjithr"
	manager="wadeh"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="ranjithr;wadeh"/>
    
# Procedure consigliate e guida alla risoluzione dei problemi per le applicazioni Node in App Web di Azure

[AZURE.INCLUDE [schede](../../includes/app-service-web-get-started-nav-tabs.md)]

In questo articolo verranno illustrate le procedure consigliate e le procedure di risoluzione dei problemi per le [applicazioni Node](app-service-web-nodejs-get-started.md) in esecuzione in App Web di Azure con [iisnode](https://github.com/azure/iisnode).

>[AZURE.WARNING] Prestare attenzione durante l'uso delle procedure di risoluzione dei problemi nel sito di produzione. È consigliabile risolvere i problemi dell'app in una configurazione non di produzione, ad esempio nello slot di staging, e dopo la risoluzione del problema scambiare lo slot di staging con lo slot di produzione.

## Configurazione IISNODE

Questo [file di schema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra tutte le impostazioni che possono essere configurate per iisnode. Ecco alcune impostazioni utili per l'applicazione:

* nodeProcessCountPerApplication

    Questa impostazione controlla il numero di processi del nodo avviati per ogni applicazione IIS. Il valore predefinito è 1. Se si specifica 0 per questa impostazione, è possibile avviare un numero di file node.exe uguale al numero di core della macchina virtuale. Il valore consigliato è 0 per la maggior parte delle applicazioni, per consentire di utilizzare tutti i core disponibili nella macchina virtuale. Node.exe è un file a thread singolo, quindi un file node.exe utilizzerà al massimo 1 core. Per ottenere le prestazioni più elevate dall'applicazione Node, è consigliabile utilizzare tutti i core.

* nodeProcessCommandLine

    Questa impostazione controlla il percorso del file node.exe. È possibile impostare questo valore in modo che faccia riferimento alla versione specifica del file node.exe.

* maxConcurrentRequestsPerProcess

    Questa impostazione controlla il numero massimo di richieste concorrenti inviate da iisnode a ogni node.exe. In App Web di Azure il valore predefinito per questa impostazione è Infinite. Non sarà necessario preoccuparsi di questa impostazione. All'esterno di App Web di Azure il valore predefinito è 1024. Potrebbe essere necessario configurare questa impostazione in base al numero di richieste ricevute dall'applicazione e dalla velocità di elaborazione di ogni richiesta da parte dell'applicazione.

* maxNamedPipeConnectionRetry

    Questa impostazione controlla il numero massimo di tentativi di connessione da parte di iisnode alla named pipe per inviare la richiesta a node.exe. Questa impostazione, insieme a namedPipeConnectionRetryDelay, determina il timeout totale di ogni richiesta in iisnode. Il valore predefinito è 200 in App Web di Azure. Total Timeout in seconds = (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    Questa impostazione controlla la quantità di tempo (in ms) di attesa da parte di iisnode prima di ripetere il tentativo di invio di richieste a node.exe tramite la named pipe. Il valore predefinito è 250 ms. Total Timeout in seconds = (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000

    Per impostazione predefinita, il timeout totale in iisnode in App Web di Azure è 200 * 250 ms = 50 secondi.

* logDirectory

    Questa impostazione controlla la directory in cui iisnode registrerà stdout/stderr. Il valore predefinito è iisnode, ovvero un valore relativo alla directory di script principale (directory in cui si trova server.js)

* debuggerExtensionDll

    Questa impostazione controlla la versione di node-inspector verrà usata da iisnode durante il debug dell'applicazione Node. iisnode-inspector-0.7.3.dll e iisnode-inspector.dll sono attualmente gli unici due valori validi per questa impostazione. Il valore predefinito è iisnode-inspector-0.7.3.dll. La versione iisnode-inspector-0.7.3.dll usa node-inspector-0.7.3 e usa WebSocket. Sarà quindi necessario abilitare i protocolli WebSocket in App Web di Azure per usare questa versione. Per altre informazioni sulla configurazione di iisnode per l'uso di node-inspector, vedere <http://www.ranjithr.com/?p=98>.

* flushResponse

    Il comportamento predefinito di IIS prevede il buffering di un massimo di 4 MB di dati di risposta prima dello scaricamento oppure fino alla fine della risposta, a seconda di quale si verifica per primo. iisnode offre un'impostazione di configurazione per eseguire l'override di questo comportamento: per scaricare un frammento del corpo dell'entità della risposta non appena iisnode lo riceve da node.exe, è necessario impostare l'attributo iisnode/@flushResponse in web.config su 'true':
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    L'abilitazione dello scaricamento di ogni frammento del corpo dell'entità della risposta aggiunge un sovraccarico delle prestazioni, che riduce la velocità effettiva del sistema del 5% circa (a partire dalla versione 0.1.13). È quindi consigliabile limitare l'ambito di questa impostazione solo agli endpoint che richiedono lo streaming delle risposte, ad esempio usando l'elemento <location> nel file web.config.

    Per lo streaming di applicazioni sarà inoltre necessario impostare il valore responseBufferLimit del gestore di iisnode su 0.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    Si tratta di un elenco di file separato da punti e virgola che sarà controllato per rilevare le modifiche. Una modifica a un file provoca il riciclo da parte dell'applicazione. Ogni voce è costituita da un nome di directory facoltativo e un nome di file obbligatorio, relativi alla directory in cui si trova il punto di ingresso principale dell'applicazione. I caratteri jolly sono consentiti solo nella parte relativa al nome di file. Il valore predefinito è "*.js;web.config".

* recycleSignalEnabled

    Il valore predefinito è False. Se questa funzionalità è abilitata, l'applicazione Node si può connettere a una named pipe (variabile di ambiente IISNODE\_CONTROL\_PIPE) e può inviare un messaggio di "riciclo". In questo modo w3wp verrà riciclato correttamente.

* idlePageOutTimePeriod

    Il valore predefinito è 0, ovvero questa funzionalità è disabilitata. Se si specifica un valore maggiore di 0, iisnode eseguirà il paging di tutti i rispettivi processi figlio ogni 'idlePageOutTimePeriod' millisecondi. Per informazioni sul paging, vedere questa [documentazione](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Questa impostazione sarà utile per le applicazioni che utilizzano una quantità elevata di memoria e vogliono eseguire occasionalmente il paging della memoria sul disco per liberare RAM.

>[AZURE.WARNING] Prestare attenzione quando si abilitano le impostazioni di configurazione seguenti nelle applicazioni di produzione. È consigliabile non abilitarle nelle applicazioni di produzione live.

* debugHeaderEnabled

    Il valore predefinito è False. Se questa opzione è impostata su True, iisnode aggiungerà un'intestazione di risposta HTTP di tipo iisnode-debug a ogni risposta HTTP inviata. Il valore di intestazione di tipo iisnode-debug è un URL. I singoli elementi delle informazioni di diagnostica possono essere dedotti esaminando il frammento di URL, ma per ottenere una visualizzazione migliore è possibile aprire l'URL nel browser.

* loggingEnabled

    Questa impostazione controlla la registrazione di stdout e stderr da parte di iisnode. Iisnode acquisirà i valori di stdout/stderr dai processi di nodo avviati e li scriverà nella directory specificata nell'impostazione 'logDirectory'. Se si abilita questa impostazione, l'applicazione scriverà log nel file system e ciò potrebbe provocare problemi di prestazioni, in base alla quantità di registrazione eseguita dall'applicazione.

* devErrorsEnabled

    Il valore predefinito è False. Se questo valore è impostato su True, iisnode visualizzerà il codice di stato HTTP e il codice errore Win32 nel browser. Il codice win32 sarà utile per il debug di determinati tipi di problemi.
    
* debuggingEnabled (non abilitare questa impostazione nel sito di produzione live)

    Questa impostazione controlla la funzionalità di debug. Iisnode è integrato con node-inspector. Abilitando questa impostazione, si abilita il debug dell'applicazione Node. Dopo l'abilitazione di questa impostazione, iisnode presenterà i file di node-inspector necessari nella directory 'debuggerVirtualDir' alla prima richiesta di debug per l'applicazione Node. È possibile caricare node-inspector inviando una richiesta a http://yoursite/server.js/debug. È possibile controllare il segmento dell'URL di debug con l'impostazione 'debuggerPathSegment'. Per impostazione predefinita, debuggerPathSegment='debug'. È possibile impostare questo valore su un GUID, ad esempio, in modo che non possa essere individuato facilmente da altri utenti.

    Per altre informazioni sul debug, vedere questo [collegamento](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html).

## Scenari e indicazioni/risoluzione dei problemi

### L'applicazione Node effettua un numero eccessivo di chiamate in uscita.

Il funzionamento normale di molte applicazioni prevede l'esecuzione di connessioni in uscita. Quando si riceve una richiesta, ad esempio, l'app Node deve contattare un'API REST in una posizione diversa e ottenere informazioni per elaborare la richiesta. È consigliabile usare un agente keep-alive durante l'esecuzione di chiamate HTTP o HTTPS. È ad esempio possibile usare il modulo agentkeepalive come agente keep-alive durante le chiamate in uscita, per assicurare che i socket vengano riutilizzati nella macchina virtuale di App Web di Azure e ridurre il sovraccarico per la creazione di nuovi socket per ogni richiesta in uscita. Ciò consente anche di assicurare che si stia usando un numero minore di socket per effettuare molte richieste in uscita e che quindi non venga superato il valore maxSockets allocato per la macchina virtuale. In App Web di Azure è consigliabile impostare il valore maxSockets per agentKeepAlive su un totale di 160 socket per ogni macchina virtuale. Se quindi sono presenti quattro node.exe in esecuzione nella macchina virtuale, è consigliabile impostare il valore maxSockets per agentKeepAlive su 40 per ogni file node.exe, ovvero 160 in totale per ogni macchina virtuale.

Configurazione di esempio per agentKeepALive:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Questo esempio presuppone che siano presenti quattro file node.exe in esecuzione nella VM. Se nella macchina virtuale è presente un numero diverso di node.exe in esecuzione, sarà necessario modificare di conseguenza l'impostazione maxSockets.

### L'applicazione Node utilizza una quantità eccessiva di CPU.

È probabile che si riceva una raccomandazione relativa all'utilizzo elevato di CPU da App Web di Azure nel portale. È anche possibile configurare monitoraggi per il controllo di determinate [metriche](web-sites-monitor.md). Quando si verifica l'utilizzo della CPU nel [dashboard del portale di Azure](../application-insights/app-insights-web-monitor-performance.md), controllare i valori MAX per la CPU, in modo da non perdere i valori di picco. Se si ritiene che l'applicazione utilizzi una quantità eccessiva di CPU senza spiegazioni plausibili, sarà necessario profilare l'applicazione Node.

###

#### Profilatura dell'applicazione Node in App Web di Azure con V8-Profiler

Si supponga, ad esempio, che sia disponibile un'app hello world da profilare come illustrato di seguito:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Passare al sito scm https://yoursite.scm.azurewebsites.net/DebugConsole

Verrà visualizzato un prompt dei comandi, come illustrato di seguito. Passare al sito/directory wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Eseguire il comando "npm install v8-profiler".

Nella directory node\_modules dovrebbe essere eseguita l'installazione di v8-profiler e di tutte le rispettive dipendenze. Modificare il file server.js per profilare l'applicazione.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Le modifiche precedenti eseguiranno la profilatura della funzione WriteConsoleLog e quindi scriveranno l'output della profilatura nel file 'profile.cpuprofile' in wwwroot del sito. Inviare una richiesta all'applicazione. Verrà visualizzato un file 'profile.cpuprofile' creato in wwwroot del sito.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Scaricare questo file. Sarà necessario aprire il file con gli strumenti F12 di Chrome. Premere F12 in Chrome, quindi fare clic sulla scheda "Profiles" (Profili). Fare clic sul pulsante "Load" (Carica). Selezionare il file profile.cpuprofile appena scaricato. Fare clic sul profilo appena caricato.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Come si può notare, il 95% del tempo è stato utilizzato dalla funzione WriteConsoleLog, come illustrato di seguito. Vengono anche mostrati i numeri di riga esatti e i file di origine che provocano questo problema.

### L'applicazione Node utilizza una quantità eccessiva di memoria.

È probabile che si riceva da App Web di Azure nel portale una raccomandazione relativa all'utilizzo elevato di memoria. È anche possibile configurare monitoraggi per il controllo di determinate [metriche](web-sites-monitor.md). Quando si verifica l'utilizzo della memoria nel [dashboard del portale di Azure](../application-insights/app-insights-web-monitor-performance.md), controllare i valori MAX per la memoria, in modo da non perdere i valori di picco.

#### Rilevamento della perdita di risorse e delle differenze tra heap per node.js 

È possibile usare [node-memwatch](https://github.com/lloyd/node-memwatch) per semplificare l'identificazione delle perdite di memoria. È possibile installare memwatch con la stessa procedura usata per v8-profiler e modificare il codice per acquisire gli heap e rilevarne le differenze per identificare le perdite di memoria nell'applicazione.

### I file node.exe vengono terminati in modo casuale 

Questo problema può avere alcune cause:

1.  L'applicazione genera eccezioni non rilevate. Cercare nel file d:\\home\\LogFiles\\Application\\logging-errors.txt i dettagli nell'eccezione generata. Questo file include l'analisi dello stack, quindi è possibile risolvere i problemi dell'applicazione in base a tali informazioni.

2.  L'applicazione utilizza una quantità troppo elevata di memoria e ciò impedisce l'avvio di altri processi. Se il totale della memoria della macchina virtuale si avvicina al 100%, è possibile che il file node.exe venga terminato da gestione processi per consentire ad altri processi di eseguire alcune operazioni. Per risolvere questo problema, assicurarsi che l'applicazione non perda memoria OPPURE, se l'applicazione deve effettivamente usare una quantità elevata di memoria, selezionare dimensioni maggiori per la macchina virtuale, in modo da ottenere molta più RAM.

### Non è possibile avviare l'applicazione Node

Se l'applicazione restituisce errori di tipo 500 all'avvio, è possibile che si siano verificati i problemi seguenti:

1.  Node.exe non è presente nella posizione corretta. Verificare l'impostazione nodeProcessCommandLine.

2.  Il file di script principale non è presente nella posizione corretta. Controllare il file web.config e assicurarsi che il nome del file di script principale nella sezione dei gestori corrisponda al file di script principale.

3.  La configurazione di Web.config non è corretta. Controllare i nomi/valori delle impostazioni.

4.  Avvio a freddo: l'avvio dell'applicazione richiede troppo tempo. Se l'avvio dell'applicazione richiede più di (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000 secondi, iisnode restituirà un errore di tipo 500. Incrementare i valori di queste impostazioni, in modo che corrispondano al tempo di avvio dell'applicazione, per impedire il timeout di iisnode e la restituzione dell'errore di tipo 500.

### Si è verificato l'arresto anomalo dell'applicazione Node

L'applicazione genera eccezioni non rilevate. Cercare nel file d:\\home\\LogFiles\\Application\\logging-errors.txt i dettagli nell'eccezione generata. Questo file include l'analisi dello stack, quindi è possibile risolvere i problemi dell'applicazione in base a tali informazioni.

### L'avvio dell'applicazione Node richiede troppo tempo (avvio a freddo)

La causa più comune di questo problema deriva dalla presenza di un numero elevato di file dell'applicazione in node\_modules e dal tentativo dell'applicazione di caricare la maggior parte di questi file durante l'avvio. Per impostazione predefinita, poiché i file si trovano nella condivisione di rete in App Web di Azure, il caricamento di un numero così elevato di file può richiedere molto tempo. Ecco alcune soluzione per ridurre i tempi di avvio:

1.  Assicurarsi che sia disponibile una struttura di dipendenze semplice, senza dipendenze duplicate, usando npm3 per installare i moduli.

2.  Provare a eseguire il caricamento lazy di node\_modules e non caricare tutti i moduli all'avvio. La chiamata a require('module') deve essere quindi effettuata quando effettivamente necessario, all'interno della funzione in cui si prova a usare il modulo.

3.  App Web di Azure offre una funzionalità definita cache locale. Questa funzionalità copia il contenuto dalla condivisione di rete nel disco locale sulla macchina virtuale. Poiché i file sono locali, il tempo di caricamento di node\_modules risulta molto più veloce. Per informazioni dettagliate su come usare, la cache locale, vedere questa [documentazione](../app-service/app-service-local-cache.md).

## Stato e stato secondario HTTP di IISNODE

Questo [file di origine](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) elenca tutte le combinazioni possibili di stato/stato secondario che iisnode può restituire in caso di errore.

Abilitare FREB per l'applicazione per visualizzare il codice errore win32. Assicurarsi di abilitare FREB solo in siti non di produzione, per motivi di prestazioni.

| Stato HTTP | Stato secondario HTTP | Possibile motivo                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500 | 1000 | Si è verificato un problema durante l'invio della richiesta a IISNODE. Verificare se node.exe è stato avviato. È possibile che si sia verificato l'arresto anomalo di Node.exe all'avvio. Cercare eventuali errori nella configurazione del file web.config. |
| 500 | 1001 | - Win32Error 0x2: l'app non risponde all'URL. Controllare le regole di riscrittura degli URL o verificare se le route dell'app Express sono state definite correttamente. - Win32Error 0x6d: la named pipe è occupata. Node.exe non accetta richieste perché la pipe è occupata. Verificare se l'utilizzo della CPU è elevato. - Altri errori: controllare se si è verificato l'arresto anomalo di node.exe.
| 500 | 1002 | Si è verificato l'arresto anomalo di Node.exe: verificare se nel file d:\\home\\LogFiles\\logging-errors.txt è disponibile l'analisi dello stack. |
| 500 | 1003 | Problema di configurazione della pipe: questo errore non dovrebbe essere mai visualizzato. Se viene visualizzato, la configurazione della named pipe non è corretta. |
| 500 | 1004-1018 | Si è verificato un errore durante l'invio della richiesta o l'elaborazione della risposta verso/da node.exe. Controllare se si è verificato l'arresto anomalo di node.exe. Verificare se nel file d:\\home\\LogFiles\\logging-errors.txt è disponibile l'analisi dello stack. |
| 503 | 1000 | La memoria non è sufficiente per allocare altre connessioni a named pipe. Verificare i motivi per cui l'app utilizza una quantità così elevata di memoria. Controllare il valore dell'impostazione maxConcurrentRequestsPerProcess. Se non è impostata su Infinite e il numero di richieste è elevato, incrementare il valore per evitare questo errore. |
| 503 | 1001 | Non è stato possibile inviare la richiesta a node.exe perché l'applicazione è in fase di riciclo. Dopo il riciclo dell'applicazione, le richieste dovrebbero essere gestite normalmente. |
| 503 | 1002 | Cercare il motivo effettivo nel codice errore win32. Non è stato possibile inviare la richiesta a node.exe. |
| 503 | 1003 | La named pipe è troppo occupata. Controllare se il nodo utilizza una quantità elevata di CPU.                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
NODE.exe include un'impostazione definita NODE\_PENDING\_PIPE\_INSTANCES. Per impostazione predefinita, all'esterno di App Web di Azure questo valore è 4. Node.exe può quindi accettare quattro richieste alla volta nella named pipe. In App Web di Azure questo valore è impostato su 5000 e questo valore dovrebbe essere appropriato per la maggior parte delle applicazioni in esecuzione in App Web di Azure. L'errore 503.1003 non dovrebbe essere visualizzato in App Web di Azure perché è stato impostato un valore elevato per NODE\_PENDING\_PIPE\_INSTANCES. |

## Altre risorse

Per altre informazioni sulle applicazioni node.js nel servizio app di Azure, selezionare questi collegamenti.

* [Introduzione alle app Web Node.js nel servizio app di Azure](app-service-web-nodejs-get-started.md)
* [Come eseguire il debug di un'app Web Node.js nel servizio app di Azure](web-sites-nodejs-debug.md)
* [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
* [Blog sulle app Web del servizio app di Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro per sviluppatori di Node. js](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Esplorazione della console segreta di debug di Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

<!---HONumber=AcomDC_0629_2016-->