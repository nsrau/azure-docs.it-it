---
title: Procedure consigliate e risoluzione dei problemi per Node.js - Servizio app di Azure
description: Informazioni sulle procedure consigliate e sui passaggi per la risoluzione dei problemi delle applicazioni Node nel Servizio app di Azure.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: db412d3fd0af84d528ad0c83d86cc5d055359914
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632688"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Procedure consigliate e risoluzione dei problemi per le applicazioni Node nel Servizio app di Azure per Windows

In questo articolo verranno illustrate le procedure consigliate e le procedure di risoluzione dei problemi per le [applicazioni Node](app-service-web-get-started-nodejs.md) in esecuzione in Servizio app di Azure con [iisnode](https://github.com/azure/iisnode).

> [!WARNING]
> Prestare attenzione durante l'uso delle procedure di risoluzione dei problemi nel sito di produzione. È consigliabile risolvere i problemi dell'app in una configurazione non di produzione, ad esempio nello slot di staging, e dopo la risoluzione del problema scambiare lo slot di staging con lo slot di produzione.
>

## <a name="iisnode-configuration"></a>Configurazione IISNODE

Questo [file di schema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra tutte le impostazioni che è possibile configurare per iisnode. Ecco alcune impostazioni utili per l'applicazione:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Questa impostazione controlla il numero di processi del nodo avviati per ogni applicazione IIS. Il valore predefinito è 1. È possibile avviare tanti file node.exe quanto è il numero di vCPU della macchina virtuale modificando il valore in 0. Il valore consigliato è 0 per la maggior parte delle applicazioni, per consentire di usare tutte le CPU virtuali disponibili nel computer. Node.exe è a thread singolo e pertanto usa un massimo di una CPU virtuale. Per ottenere prestazioni ottimali dall'applicazione Node, usare tutte le CPU virtuali.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Questa impostazione controlla il percorso del file node.exe. È possibile impostare questo valore in modo che faccia riferimento alla versione specifica del file node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Questa impostazione controlla il numero massimo di richieste concorrenti inviate da iisnode a ogni node.exe. In Servizio app di Azure il valore predefinito è Infinito. È possibile configurare il valore in base al numero di richieste ricevute dall'applicazione e dalla velocità in cui l'applicazione elabora ogni richiesta.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Questa impostazione controlla il numero massimo di tentativi di connessione da parte di iisnode alla named pipe per inviare la richiesta a node.exe. Questa impostazione, insieme a namedPipeConnectionRetryDelay, determina il timeout totale di ogni richiesta in iisnode. Il valore predefinito è 200 in Servizio app di Azure. Timeout totale in secondi = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Questa impostazione controlla la quantità di tempo (in ms) di attesa da parte di iisnode prima di ripetere il tentativo di invio di richieste a node.exe tramite la named pipe. Il valore predefinito è 250 ms.
Timeout totale in secondi = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Per impostazione predefinita, il timeout totale in iisnode in Servizio app di Azure è 200 \* 250 ms = 50 secondi.

### <a name="logdirectory"></a>logDirectory

Questa impostazione controlla la directory in cui iisnode registra stdout/stderr. Il valore predefinito è iisnode, ovvero un valore relativo alla directory di script principale (directory in cui si trova server.js)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Questa impostazione controlla la versione di node-inspector usata da iisnode durante il debug dell'applicazione Node. iisnode-inspector-0.7.3.dll e iisnode-inspector.dll sono attualmente gli unici due valori validi per questa impostazione. Il valore predefinito è iisnode-inspector-0.7.3.dll. La versione di iisnode-inspector-0.7.3.dll usa node-inspector-0.7.3 e WebSocket. Per usare questa versione, abilitare i WebSocket nell'app Web di Azure. Per altre informazioni su come configurare iisnode per usare il nuovo node-inspector, vedere <https://ranjithblogs.azurewebsites.net/?p=98>.

### <a name="flushresponse"></a>flushResponse

Il comportamento predefinito di IIS prevede il buffering di un massimo di 4 MB di dati di risposta prima dello scaricamento oppure fino alla fine della risposta, a seconda di quale si verifica per primo. iisnode offre un'impostazione di configurazione per eseguire l'override di questo comportamento: per scaricare un frammento del corpo dell'entità della risposta non appena iisnode lo riceve da node.exe, è necessario impostare l'attributo iisnode/@flushResponse in web.config su 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Se si abilita lo scaricamento di ogni frammento del corpo dell'entità della risposta, si verifica un overhead delle prestazioni che riduce la velocità effettiva del sistema di circa il 5% (a partire da v0.1.13). L'approccio migliore è definire l'ambito di questa impostazione solo per gli endpoint che richiedono lo streaming delle risposte, ad esempio usando l'elemento `<location>` in web.config

Per lo streaming di applicazioni è inoltre necessario impostare il valore responseBufferLimit del gestore di iisnode su 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Elenco di file con valori separati da punti e virgola che vengono controllati per rilevare le modifiche. Qualsiasi modifica apportata a un file provoca il riciclo da parte dell'applicazione. Ogni voce è costituita da un nome di directory facoltativo e da un nome di file obbligatorio, relativi alla directory in cui si trova il punto di ingresso principale dell'applicazione. I caratteri jolly sono consentiti solo nella parte relativa al nome di file. Il valore predefinito è `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Il valore predefinito è False. Se questa funzionalità è abilitata, l'applicazione Node si può connettere a una named pipe (variabile di ambiente IISNODE\_CONTROL\_PIPE) e può inviare un messaggio di "riciclo". In questo modo w3wp verrà riciclato correttamente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Il valore predefinito è 0, ovvero questa funzionalità è disabilitata. Se si specifica un valore maggiore di 0, iisnode eseguirà il paging di tutti i rispettivi processi figlio ogni 'idlePageOutTimePeriod' in millisecondi. Per informazioni sul paging, vedere questa [documentazione](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Questa impostazione è utile per le applicazioni che usano una quantità elevata di memoria e vogliono eseguire occasionalmente il paging della memoria sul disco per liberare RAM.

> [!WARNING]
> Prestare attenzione quando si abilitano le impostazioni di configurazione seguenti nelle applicazioni di produzione. È consigliabile non abilitarle nelle applicazioni di produzione live.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Il valore predefinito è False. Se è True, iisnode aggiunge un'intestazione di risposta HTTP `iisnode-debug` a ogni risposta HTTP inviata. Il valore di intestazione `iisnode-debug` è un URL. Le singole informazioni di diagnostica possono essere ottenute esaminando il frammento di URL, ma è disponibile una visualizzazione tramite l'apertura dell'URL in un browser.

### <a name="loggingenabled"></a>loggingEnabled

Questa impostazione controlla la registrazione di stdout e stderr da parte di iisnode. Iisnode acquisisce i valori di stdout/stderr dai processi di nodo avviati e li scriverà nella directory specificata nell'impostazione 'logDirectory'. Se si abilita questa impostazione, l'applicazione scrive log nel file system e ciò potrebbe provocare problemi di prestazioni, in base alla quantità di registrazione eseguita dall'applicazione.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Il valore predefinito è False. Se questo valore è impostato su True, iisnode visualizza il codice di stato HTTP e il codice errore Win32 nel browser. Il codice win32 risulta utile per il debug di determinati tipi di problemi.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (non abilitare questa impostazione nel sito di produzione live)

Questa impostazione controlla la funzionalità di debug. Iisnode è integrato con node-inspector. Abilitando questa impostazione, si abilita il debug dell'applicazione Node. Dopo l'abilitazione di questa impostazione, iisnode crea i file node-inspector necessari nella directory "debuggerVirtualDir" alla prima richiesta di debug per l'applicazione Node. È possibile caricare node-inspector inviando una richiesta a http://yoursite/server.js/debug. È possibile controllare il segmento dell'URL di debug con l'impostazione 'debuggerPathSegment'. Per impostazione predefinita, debuggerPathSegment='debug'. È possibile impostare `debuggerPathSegment` su un GUID, ad esempio, in modo che non possa essere individuato facilmente da altri utenti.

Per altre informazioni sul debug, vedere [Debug node.js applications on Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) (Eseguire il debug di applicazioni Node.js in Windows).

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenari e indicazioni/risoluzione dei problemi

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>L'applicazione Node effettua un numero eccessivo di chiamate in uscita

Il funzionamento normale di molte applicazioni prevede l'esecuzione di connessioni in uscita. Quando si riceve una richiesta, ad esempio, l'app Node deve contattare un'API REST in una posizione diversa e ottenere informazioni per elaborare la richiesta. È consigliabile usare un agente keep-alive durante l'esecuzione di chiamate HTTP o HTTPS. È possibile usare il modulo agentkeepalive come agente keep-alive durante le chiamate in uscita.

Il modulo agentkeepalive assicura il riutilizzo dei socket nella macchina virtuale in App Web di Azure. La creazione di un nuovo socket a ogni richiesta in uscita comporta un ulteriore sovraccarico per l'applicazione. Facendo in modo che l'applicazione riutilizzi i socket per le richieste in uscita, è possibile evitare che l'applicazione superi il valore di maxSockets allocato per ogni macchina virtuale. In Servizio app di Azure è consigliabile impostare il valore di maxSockets per agentKeepAlive su un totale di 160 socket per macchina virtuale (4 istanze di node.exe \* 40 maxSockets/istanza).

Configurazione di esempio per [agentKeepALive](https://www.npmjs.com/package/agentkeepalive):

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Questo esempio presuppone che siano presenti quattro file node.exe in esecuzione nella VM. Se nella macchina virtuale è presente un numero diverso di node.exe in esecuzione, è necessario modificare di conseguenza l'impostazione maxSockets.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>L'applicazione Node usa una quantità eccessiva di CPU

Si potrebbe ricevere un'indicazione da Servizio app di Azure nel portale riguardo all'utilizzo elevato di CPU. È anche possibile configurare monitoraggi per il controllo di determinate [metriche](web-sites-monitor.md). Quando si verifica l'uso della CPU nel [dashboard del portale di Azure](../application-insights/app-insights-web-monitor-performance.md), controllare i valori MAX per la CPU, in modo da non perdere i valori di picco.
Se si ritiene che l'applicazione usi una quantità eccessiva di CPU senza una spiegazione plausibile, è possibile profilare l'applicazione Node per individuare il motivo.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilatura dell'applicazione Node in Servizio app di Azure con V8-Profiler

Si supponga, ad esempio, che sia disponibile un'app hello world da profilare come illustrato di seguito:

```nodejs
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

Passare al sito della console di debug all'indirizzo https://yoursite.scm.azurewebsites.net/DebugConsole

Passare alla directory site/wwwroot. Verrà visualizzato un prompt dei comandi, come mostrato nell'esempio seguente:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Eseguire il comando `npm install v8-profiler`.

Questo comando installa v8-profiler nella directory \_modules, insieme a tutte le dipendenze.
Modificare il file server.js per profilare l'applicazione.

```nodejs
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

Il codice precedente eseguirà la profilatura della funzione WriteConsoleLog e quindi scriverà l'output della profilatura nel file 'profile.cpuprofile' nella directory wwwroot del sito. Inviare una richiesta all'applicazione. Verrà visualizzato un file 'profile.cpuprofile' creato nella directory wwwroot del sito.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Scaricare questo file e aprirlo con gli strumenti F12 di Chrome. Premere F12 in Chrome, quindi fare clic sulla scheda **Profiles** (Profili). Fare clic sul pulsante **Load** (Carica). Selezionare il file profile.cpuprofile scaricato. Fare clic sul profilo appena caricato.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Come si può notare, il 95% del tempo è stato usato dalla funzione WriteConsoleLog. L'output mostra anche l'esatto numero di righe e di file di origine che hanno causato il problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>L'applicazione Node usa una quantità eccessiva di memoria

Se l'applicazione usa una quantità eccessiva di memoria, nel portale viene visualizzato un avviso di Servizio app di Azure indicante questa situazione. È possibile configurare monitoraggi per il controllo di determinate [metriche](web-sites-monitor.md). Quando si verifica l'uso della memoria nel [dashboard del portale di Azure](../application-insights/app-insights-web-monitor-performance.md), assicurarsi di controllare i valori MAX per la memoria, in modo da non perdere i valori di picco.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Rilevamento della perdita di risorse e delle differenze tra heap per Node.js

È possibile usare [node-memwatch](https://github.com/lloyd/node-memwatch) per semplificare l'identificazione delle perdite di memoria.
È possibile installare `memwatch` con la stessa procedura usata per v8-profiler e modificare il codice per acquisire gli heap e rilevarne le differenze in modo da identificare le perdite di memoria nell'applicazione.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>I file node.exe vengono terminati in modo casuale

Esistono diversi motivi per cui node.exe è stato arrestato in modo casuale:

1. L'applicazione genera eccezioni non rilevate. Cercare nel file d:\\home\\LogFiles\\Application\\logging-errors.txt i dettagli nell'eccezione generata. Questo file include l'analisi dello stack per semplificare il debug e la correzione dell'applicazione.
2. L'applicazione usa una quantità eccessiva di memoria, impedendo l'avvio di altri processi. Se la memoria totale della macchina virtuale si avvicina al 100%, è possibile che il file node.exe venga terminato dalla gestione dei processi. La gestione dei processi termina alcuni processi per offrire ad altri processi la possibilità di eseguire alcune attività. Per risolvere questo problema, profilare l'applicazione per individuare le perdite di memoria. Se l'applicazione richiede grandi quantità di memoria, applicare la scalabilità verticale a una macchina virtuale di dimensioni maggiori (che aumenta la quantità di RAM disponibile per la macchina virtuale).

### <a name="my-node-application-does-not-start"></a>Non è possibile avviare l'applicazione Node

Se l'applicazione restituisce errori di tipo 500 all'avvio, è possibile che si siano verificati i problemi seguenti:

1. Node.exe non è presente nella posizione corretta. Verificare l'impostazione nodeProcessCommandLine.
2. Il file di script principale non è presente nella posizione corretta. Controllare il file web.config e assicurarsi che il nome del file di script principale nella sezione dei gestori corrisponda al file di script principale.
3. La configurazione di Web.config non è corretta. Controllare i nomi/valori delle impostazioni.
4. Avvio a freddo: l'avvio dell'applicazione richiede troppo tempo. Se l'avvio dell'applicazione richiede più di (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 secondi, iisnode restituisce un errore di tipo 500. Incrementare i valori di queste impostazioni, in modo che corrispondano al tempo di avvio dell'applicazione, per impedire il timeout di iisnode e la restituzione dell'errore di tipo 500.

### <a name="my-node-application-crashed"></a>Si è verificato l'arresto anomalo dell'applicazione Node

L'applicazione genera eccezioni non rilevate. Controllare il file `d:\\home\\LogFiles\\Application\\logging-errors.txt` per informazioni sull'eccezione generata. Questo file include l'analisi dello stack per semplificare la diagnosi e la correzione dell'applicazione.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>L'avvio dell'applicazione Node richiede troppo tempo (avvio a freddo)

Tempi prolungati di avvio dell'applicazione sono dovuti a un numero elevato di file nel nodo \_modules. L'applicazione tenta di caricare la maggior parte di questi file all'avvio. Per impostazione predefinita, poiché i file sono archiviati nella condivisione di rete in Servizio app di Azure, il caricamento di un numero eccessivo di file può richiedere tempo.
Ecco alcune soluzione per velocizzare questo processo:

1. Assicurarsi che sia disponibile una struttura di dipendenze semplice, senza dipendenze duplicate, usando npm3 per installare i moduli.
2. Provare a eseguire il caricamento lazy di node\_modules e non caricare tutti i moduli all'avvio dell'applicazione. Per i moduli di caricamento lazy, la chiamata a require('module') deve essere effettuata quando è effettivamente necessario che il modulo sia incluso nella funzione prima della prima esecuzione del codice del modulo.
3. Servizio app di Azure offre una funzionalità definita cache locale. Questa funzionalità copia il contenuto dalla condivisione di rete nel disco locale sulla macchina virtuale. Poiché i file sono locali, il tempo di caricamento di node\_modules risulta molto più veloce.

## <a name="iisnode-http-status-and-substatus"></a>Stato e stato secondario HTTP di IISNODE

Il [file di origine](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) `cnodeconstants` elenca tutte le possibili combinazioni di stato/stato secondario che possono essere restituite da iisnode a causa di un errore.

Abilitare FREB per l'applicazione per visualizzare il codice errore win32. Assicurarsi di abilitare FREB solo in siti non di produzione, per motivi di prestazioni.

| Stato HTTP | Stato secondario HTTP | Possibile motivo |
| --- | --- | --- |
| 500 |1000 |Si è verificato un problema durante l'invio della richiesta a IISNODE. Verificare se node.exe è stato avviato. È possibile che si sia verificato l'arresto anomalo di Node.exe all'avvio. Cercare eventuali errori nella configurazione del file web.config. |
| 500 |1001 |- Win32Error 0x2: l'app non risponde all'URL. Controllare le regole di riscrittura dell'URL o verificare se sono state definite le route corrette per l'app Express. - Win32Error 0x6d: la named pipe è occupata. Node.exe non accetta le richieste perché la pipe è occupata. Controllare se l'utilizzo della CPU è elevato. - Altri errori: controllare se si è verificato l'arresto anomalo di node.exe. |
| 500 |1002 |Si è verificato l'arresto anomalo di Node.exe: verificare se nel file d:\\home\\LogFiles\\logging-errors.txt è disponibile l'analisi dello stack. |
| 500 |1003 |Problema di configurazione della pipe: la configurazione della named pipe non è corretta. |
| 500 |1004-1018 |Si è verificato un errore durante l'invio della richiesta o l'elaborazione della risposta verso/da node.exe. Controllare se si è verificato l'arresto anomalo di node.exe. Verificare se nel file d:\\home\\LogFiles\\logging-errors.txt è disponibile l'analisi dello stack. |
| 503 |1000 |La memoria non è sufficiente per allocare altre connessioni a named pipe. Verificare i motivi per cui l'app utilizza una quantità così elevata di memoria. Controllare il valore dell'impostazione maxConcurrentRequestsPerProcess. Se non è impostata su Infinite e il numero di richieste è elevato, incrementare il valore per evitare questo errore. |
| 503 |1001 |Non è stato possibile inviare la richiesta a node.exe perché l'applicazione è in fase di riciclo. Dopo il riciclo dell'applicazione, le richieste dovrebbero essere gestite normalmente. |
| 503 |1002 |Cercare il motivo effettivo nel codice errore win32. Non è stato possibile inviare la richiesta a node.exe. |
| 503 |1003 |La named pipe è troppo occupata. Verificare se node.exe sta usando una quantità eccessiva di CPU |

NODE.exe include un'impostazione denominata `NODE_PENDING_PIPE_INSTANCES`. In Servizio app di Azure questo valore è impostato su 5000. Ossia, node.exe può accettare 5000 richieste alla volta nella named pipe. Questo valore dovrebbe essere appropriato per la maggior parte delle applicazioni in esecuzione in Servizio app di Azure. L'errore 503.1003 non dovrebbe essere visualizzato in Servizio app di Azure perché è stato impostato un valore elevato per `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Altre risorse

Per altre informazioni sulle applicazioni node.js nel servizio app di Azure, selezionare questi collegamenti.

* [Introduzione alle app Web Node.js nel servizio app di Azure](app-service-web-get-started-nodejs.md)
* [Come eseguire il debug di un'app Web Node.js nel servizio app di Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
* [App Web del Servizio app di Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro per sviluppatori di Node. js](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Esplorazione della console segreta di debug di Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)