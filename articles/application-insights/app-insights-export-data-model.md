<properties 
	pageTitle="Modello di dati di Application Insights" 
	description="Descrive le proprietà esportate da esportazione continua in JSON e usate come filtri." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/12/2015" 
	ms.author="awills"/>

# Modello di dati di esportazione di Application Insights

Questa tabella elenca le proprietà di telemetria inviate al portale dagli SDK di [Application Insights](app-insights-overview.md). Queste proprietà saranno visualizzate nell'output dei dati di [Esportazione continua](app-insights-export-telemetry.md). Sono visibili anche nei filtri delle proprietà in [Esplora metriche](app-insights-metrics-explorer.md) e [Ricerca diagnostica](app-insights-diagnostic-search.md).

Ci sono diversi [esempi](app-insights-export-telemetry.md#code-samples) che illustrano come usarle.

La voce "&lt;telemetryType&gt;" della prima sezione è un segnaposto per qualsiasi nome di tipo di telemetria: visualizzazione, richiesta e così via.


## & lt; telemetryType & gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Un contenitore delle proprietà di coppia chiave valore (KVP) che fornisce l'estensibilità sugli articoli di telemetria di AppInsights per l’aggiunta di metriche personalizzate. 

    *Derivazione:* i nomi delle misurazioni hanno dimensioni massime pari a 100

    *Predefinito:* in presenza di una chiave esistente, il valore mancante è quindi count = 1, value = 0, min/max = 0

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Un contenitore delle proprietà di coppia chiave valore (KVP) che fornisce l'estensibilità sugli articoli di telemetria di AppInsights per l’aggiunta di proprietà personalizzate. Lo sviluppatore è in grado di fornire un elenco di coppia chiave-valore associato a un elemento di dati di telemetria. Ogni chiave viene rilevata e possono essere fornite massimo 200 chiavi univoche per ogni AppInsights ikey (applicazione). Una chiave può avere una lunghezza massima di 100 caratteri. Tutti i valori vengono considerati come stringa e può essere fornita una dimensione massima di 1000 caratteri. Ogni proprietà inizialmente è classificata come una dimensione, che abilita le funzionalità di segmentazione in base a ogni set di valore della proprietà. Ogni set di valore è registrato per ogni chiave di proprietà per la cardinalità. Quando la cardinalità di una chiave supera 100 valori univoci, la proprietà viene classificata come un attributo. Un attributo può essere ricercato, ma non può essere la destinazione di segmentazione (aggregazione o raggruppamento). 

    *Derivazione:*i nomi delle proprietà hanno dimensioni massime pari a 100; i valori delle proprietà hanno dimensioni massime pari a 1024

    *Predefinito:* in presenza di una chiave esistente, il valore mancante è quindi value = null

**count**

    long <telemetryType>.count      
* 
    Il count dell'elemento di telemetria.   

    *Derivazione:* se Null, count = 1

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    La durata dell'elemento di telemetria. Per la richiesta, questo è il tempo di esecuzione della richiesta. 

    *Predefinito:* R1, per la visualizzazione questo campo è facoltativo

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Un messaggio di testo nel tipo di telemetria. Questa stringa è disponibile per la ricerca full-text. 

**nome**

    string <telemetrytype>.name      Max: 250
* 
    Il nome dell'elemento di telemetria. Questo nome è non univoco tra più istanze e rappresenta un raggruppamento di tipi di telemetria. Per le visualizzazioni, viene specificata la URLData.base. Per gli eventi, questo è un’etichetta fornita dallo sviluppatore. Per le richieste, questa è una forma leggibile della richiesta, ad esempio il controller\\action. 

    *Esempi*<br/> Visualizzare i nomi:<br/>Domanda 1 Esame 70-486<br/>Informazioni su - Applicazione ASP.NET<br/><br/>Nomi di richiesta di esempio:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**gravità**

    string <telemetryType>.severity      Max: 100
* 
    La gravità dell'elemento di telemetria. Ciò è valido per gli elementi di telemetria di Traccia ed Eccezione 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    L'URL del pageview, evento, richiesta o RDD. L'URL completo ed è supportato nella ricerca full-text e nell'esportazione. Questo campo può avere una cardinalità elevata ed è un attributo. Viene analizzato in un set di elementi di dati urlData che può essere utilizzato per le aggregazioni in Esplorazione di metrica. 

    *Predefinito:* R2: in remotedepencyType se dependencyType = HTTP questo campo è obbligatorio<br/> In clientperformanceType questo campo è obbligatorio

    *Esempi*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Una parte dell'elemento di dati URL esclusi host, parametri di query. È la radice URI. Questo valore può essere utilizzato per la segmentazione/aggregazione. 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    Il testo di hashtag dell'elemento di dati URL 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    L'host dell'elemento di dati URL. Se l'elemento di dati URL è un URI locale, allora è rappresentato come vuoto 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>

**urlData.port**

    string <telemetrytype>.urldata.port      Max: 100
* 
    La porta dell'elemento di dati URL, se è rappresentato l'URL completo. In caso contrario, è vuoto. 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> 80<br/>443

**urlData.protocol**

    string <telemetrytype>.urldata.protocol      Max: 100
* 
    Il protocollo (HTTP, FTP ecc.) dell'elemento di dati URL 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> http<br/>https

**urlData.queryParameters.parameter**

    string <telemetrytype>.urldata.queryparameters.parameter      Max: 100
* 
    Una matrice dei nomi di parametro di query dell'elemento di dati URL 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> etc<br/>extraqs<br/>pagemode<br/>pagetype

**urlData.queryParameters.value**

    string <telemetrytype>.urldata.queryparameters.value      Max: 100
* 
    Una matrice di valori del parametro di query analizzati dall'elemento di dati URL 

    *Derivazione:* vedere l'appendice per la trasformazione di URL


## disponibilità

**disponibilità**

    simpleMetric availability.availability      
* 
    Un indicatore dell'esito positivo del test di disponibilità 

**dataSize**

    simpleMetric availability.datasize      
* 
    Le dimensioni della & lt; telemetry & gt;. messaggio di testo messaggio 

**risultato**

    enum (pass/fail) availability.result      
* 
    Un puntatore (chiamata HTTP) per recuperare informazioni dettagliate del test web di disponibilità 

**runLocation**

    string availability.runlocation      Max: 100
* 
    Il percorso di esecuzione del test di disponibilità 

**testName**

    string availability.testname      Max: 1024
* 
    Il nome del test di disponibilità 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Un id univoco per l'istanza dell'esecuzione del test di disponibilità 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    Il timestamp dell'inizio dell'istanza di esecuzione dei test di disponibilità 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**statico**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Derivazione:* vedere l'appendice per l'analisi dello stack di chiamate 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**Exceptions.Message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Una parte del tempo perTotal. Questa parte rappresenta il tempo che l'applicazione ha impiegato per elaborare la risposta. Per un client web, questo è il tempo di elaborazione del Modello Oggetto del Documento (DOM). Questo intervallo viene acquisito utilizzando l’API perfTiming del browser moderno. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Una parte del tempo perTotal. Questa parte rappresenta il tempo che l'applicazione ha impiegato per stabilire la connessione di rete. Questo intervallo viene acquisito utilizzando l’API perfTiming del browser moderno. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    Tempo totale del caricamento della visualizzazione. Per i client web, ciò equivale a ""temo per il caricamento della pagina"". Questo intervallo viene acquisito utilizzando l’API perfTiming del browser moderno. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Una parte del tempo perTotal. Questa parte rappresenta il tempo dell'applicazione per ricevere la risposta richiesta. Questo intervallo viene acquisito utilizzando l’API perfTiming del browser moderno. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Una parte del tempo perTotal. Questa parte rappresenta il tempo che l'applicazione ha impiegato per inviare la richiesta al server. Questo intervallo viene acquisito utilizzando l’API perfTiming del browser moderno. 


## contesto

**applicationBuild**

    string context.application.build      Max: 100
* 
    Il numero di build dell'applicazione dell'app 

**applicationVersion**

    string context.application.version      Max: 100
* 
    La versione di applicazione dell'applicazione client 

    *Esempi*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Questo rappresenta il tipo telemetria per il record di fatturazione ed è utilizzato come una segmentazione degli elementi di telemetria fatturabile per un'app 

**dataId**

    string context.data.id      Max: 100
* 
    Un identificatore univoco dell'elemento di telemetria. Assegnato all'endpoint della raccolta dei dati. 

    *Derivazione:* UUID4 generato

    *Esempi*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    L'ora dell'evento telemetria registrato in formato UTC. In genere, questo viene inserito nel client. Se questo campo manca, viene popolato all'endpoint della raccolta dei dati. Il formato del campo AAAA-MM-DDTHH:MM:SS.sssZ.    

    *Esempi*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    La frequenza di campionamento del produttore di dati (SDK). Qui un valore diverso da 1 indica che la metrica associata all'elemento di telemetria rappresenta valori campionati. Pertanto, una frequenza campionata pari a 0,05 comporterebbe qualsiasi elemento di dati di telemetria 1 che rappresenta un conteggio pari a 20. 

**browser**

    string context.device.browser      Max: 100
* 
    Il browser del client 

    *Predefinito:* se Null, viene impostato in base all'elaborazione dell'agente utente. Vedere l'appendice per l’analisi dell’agente utente

    *Esempi*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    La versione del browser del client 

    *Predefinito:* se Null, viene impostato in base all'elaborazione dell'agente utente. Vedere l'appendice per l’analisi dell’agente utente

    *Esempi*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    L'id di distribuzione del server 

**deviceId**

    string context.device.id      Max: 100
* 
    Un’identificazione univoca del client. Un id generato e deve essere archiviato localmente nel dispositivo e non devono essere informazioni personali, ad esempio l'indirizzo MAC o un id non modificabile simile.   

**deviceModel**

    string context.device.devicemodel      Max: 100
* 
    Il devicemodel per il client di hardware per dispositivi mobili 

    *Esempi*<br/> Altri<br/>iPad<br/>Nokia 503s

**deviceName**

    string context.device.name      Max: 100
* 
    Il nome del dispositivo dell'app è in esecuzione 

**deviceType**

    string context.device.type      Max: 100
* 
    Il tipo di dispositivo dell'hardware del client 

    *Esempi*<br/> PC<br/>Dispositivo mobile<br/>Tablet

**language**

    string context.device.language      Max: 100
* 
    La lingua dell'applicazione nel client. Se non specificato in modo esplicito per l'elemento di telemetria, proviene dall'elaborazione del campo agente utente. 

**locale**

    string context.device.locale      Max: 100
* 
    Il locale dell'applicazione sul client. Se non specificato in modo esplicito per l'elemento di telemetria, proviene dall'elaborazione del campo agente utente. 

    *Esempi*<br/> ru<br/>it-IT<br/>de-DE<br/>sconosciuto

**machineName**

    string context.device.vmname      Max: 100
* 
    Il nome del computer del server. Per il calcolo virtualizzato, questo elemento di dati è equivalente all'host sottostante. Per il calcolo dedicato, questo è il nome del computer. 

**networkType**

    string context.device.network      Max: 100
* 
    Il tipo di rete del client 

**oemName**

    string context.device.oemname      Max: 100
* 
    Il nome dell'oem per il client di hardware per dispositivi mobili 

**operatingSystem**

    string context.device.os      Max: 100
* 
    Il sistema operativo del client 

    *Predefinito:* se Null, viene impostato in base all'elaborazione dell'agente utente. Vedere l'appendice per l’analisi dell’agente utente

    *Esempi*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    Versione del sistema operativo del client 

    *Predefinito:* se Null, viene impostato in base all'elaborazione dell'agente utente. Vedere l'appendice per l’analisi dell’agente utente

    *Esempi*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    L'istanza di calcolo del server. In uno scenario cloud/virtualizzato, questo è il nome virtuale dell'istanza di calcolo. In un'istanza di calcolo dedicato, questo è il nome del computer. Per i servizi Cloud di Azure, questo deve passare per impostazione predefinita al nome di istanza di ruolo PaaS o al nome della macchina virtuale IaaS  

**roleName**

    string context.device.rolename      Max: 100
* 
    Uno spazio dei nomi logico al server del gruppo di istanze di calcolo. Per i servizi ospitati di Azure, ruoli PaaS devono passare per impostazione predefinita al nome del ruolo PaaS. I ruoli IaaS devono passare per impostazione predefinita al nome della macchina virtuale.  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    L'altezza dello schermo dell'applicazione su hardware client nel momento in cui l'elemento di telemetria viene registrato. Se non è esplicitamente fornito proviene da una trasformazione dell'elemento di dati screenresolution. 

    *Derivazione:* analizzato da context.device.screenresolution, se presente

    *Esempi*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    La risoluzione dello schermo nel momento in cui l'elemento di telemetria è stato acquisito dall’app. Questo può passare da un ritratto a un paesaggio nel corso di una sessione. Quando questo attributo viene eseguito a livello di sessione, è la prima risoluzione dello schermo acquisita a rappresentare la sessione completa. 

    *Esempi*<br/> Altezza e larghezza per la risoluzione dello schermo<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    La larghezza dello schermo dell'applicazione sull’hardware del client nel momento in cui l'elemento di telemetria viene registrato. Se non è esplicitamente fornito proviene da una trasformazione dell'elemento di dati screenresolution. 

    *Derivazione:* analizzato da context.device.screenresolution, se presente

    *Esempi*<br/> 640<br/>800<br/>1080

**userAgentString**

    string context.device.useragent      Max: 1000
* 
    L'agente utente del browser del client 

    *Predefinito:* se Null, impostare sull'agente utente HTTP acquisito nell'endpoint della raccolta dati

    *Esempi*<br/> Opera/9.80 (Windows NT 5.1) Presto/2.12.388 Version/12.17<br/>Mozilla/5.0 (iPad; CPU OS 8\_3 come Mac OS X) AppleWebKit/600.1.4 (KHTML, come Gecko) Version/8.0 Mobile/12F69 Safari/600.1.4<br/>Chrome/37.0.2062.124 Safari/537.36<br/>Mozilla/4.0 (compatibile; MSIE 7.0; Windows NT 6.1; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)<br/>Safari/537.36<br/>+S89

**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    L’elemento dei dati e rappresenta la versione dell'agente SDK che ha generato l'elemento di telemetria 

**city**

    string context.location.city      Max: 100
* 
    La città della sessione di app. Può essere fornita direttamente sull'elemento di telemetria. Se non è presente, viene popolato in base a IPv4 sull’elemento di telemetria. Se non è presente alcuna IPv4 fornita, il campo viene lasciato vuoto. 

    *Esempi*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    L'indirizzo IPv4 del client nel formato xxx.xxx. xxx.xxx.   

    *Predefinito:* se Null, impostare l'indirizzo IP HTTP acquisito nell'endpoint della raccolta dati

    *Esempi*<br/> 0.123.63.143<br/>123.203.131.197

**continent**

    string context.location.continent      Max: 100
* 
    Il continente della sessione dell’app. Può essere fornita direttamente sull'elemento di telemetria. Se non è presente, viene popolato in base a IPv4 sull’elemento di telemetria. Se non è presente alcuna IPv4 fornita, il campo viene lasciato vuoto. 

    *Esempi*<br/> Europa<br/>America del Nord

**country**

    string context.location.country      Max: 100
* 
    Il paese della sessione dell’app. Può essere fornita direttamente sull'elemento di telemetria. Se non è presente, viene popolato in base a IPv4 sull’elemento di telemetria. Se non è presente alcuna IPv4 fornita, il campo viene lasciato vuoto. 

    *Esempi*<br/> Belarus<br/>Paesi Bassi<br/>Germania

**latitudine**

    long context.location.point.lat      
* 
    *Esempi*<br/> 53,9<br/>45,7788 

**longitudine**

    long context.location.point.lon      
* 
    *Esempi*<br/> 27,5667<br/>-119,529 

**state**

    string context.location.province      Max: 100
* 
    La provincia della sessione dell’app. Può essere fornita direttamente sull'elemento di telemetria. Se non è presente, viene popolato in base a IPv4 sull’elemento di telemetria. Se non è presente alcuna IPv4 fornita, il campo viene lasciato vuoto. 

    *Esempi*<br/> Minsk<br/>Oregon<br/>Serbia centrale<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    Il operation.id è un id di correlazione che può essere utilizzato tra gli elementi di telemetria. Ad esempio, un id di richiesta può essere compilato nella operation.id di tutti gli elementi di telemetria correlati, consentendo una correlazione tra gli elementi di dati di telemetria, come rdd, eccezione, eventi e così via.  

**operationName**

    string context.operation.name      Max: 100
* 
    Un nome di operazione leggibile dall’uomo. Vedere l’Id dell’operazione. Questo consente un raggruppamento di ID di operazioni simili come Acquisto Completo.   

**operationParentId**

     context.operation.parentid      
* 
    Una parent id to operation.id, che consente la nidificazione di ID per la correlazione di telemetria.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Se issynthetic = true, questo elemento di dati rappresenta l'origine dei dati sintetici. 

    *Predefinito:* se Null, nell'agente utente vengono cercate eventuali origini sintetiche note (agente di ricerca Web ecc.) e in base a queste informazioni può essere impostata l'origine.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Un elemento che indica che l'elemento di telemetria è stato generato da test sintetici e non da attività reali degli utenti. 

    *Predefinito:* se Null, viene effettuata la ricerca nell'agente utente rispetto a un elenco di agenti sintetici noto. Se viene trovata una corrispondenza, il valore è impostato su true.<br/>Se l'agente utente è Null, il valore viene impostato su false

**session.Id**

    String context.session.id      Max: 100
* 
    Un identificatore univoco di un'interazione con utenti reali con un'applicazione. Questa interazione è una "" sessione"". Tutta la telemetria che è generata dall'applicazione sottoposta allo stesso iKey deve contenere questo identificatore univoco. <br/><br/>Una sessione è definita dagli eventi consecutivi all'interno dell'interazione dello stesso utente. Un periodo di 30 minuti senza un evento di telemetria segnala la fine di una sessione.   

    *Predefinito:* non valido in MetricType, BillingType

    *Esempi*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate      
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Un identificatore univoco che definisce un utente anonimo all'interno dell'app. Quando viene utilizzato un SDK, questo viene generato automaticamente e mantenuto sul client. Mentre questo non distingue utenti reali quando si condivide un dispositivo con lo stesso account di accesso. Distingue tra utenti reali quando si utilizzano diversi account di accesso e il sistema operativo supporta i profili. Quando non esiste alcuna esperienza di autenticazione è il migliore proxy per gli utenti univoci. 

    *Esempi*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      
**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate      
**authUserId**

    string context.user.authId      Max: 100
* 
    Un identificatore univoco che definisce un utente autenticato all'interno dell'app. Si tratta di uno sviluppatore fornito. Il vantaggio di un utente autenticato è che l'identificatore può eseguire il roaming tra i dispositivi all'interno dell'app e mantenere comunque l'univocità. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    L'area di archiviazione in cui è stata generata l'applicazione. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Un identificatore univoco che definisce un account all'interno dell'applicazione. Si tratta di uno sviluppatore fornito. 

### Metriche personalizzate

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Indica se la chiamata remota di dipendenza era asincrona 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    Il nome del comando SQL della dipendenza remota, se la dipendenza remota è SQL 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    Il nome del tipo di dipendenza remota della dipendenza remota 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    Il nome nf della dipendenza remota 

    *Derivazione:* standardizzare su &lt;telemetryType.name&gt;

**remoteDependencyType**

    string remotedependency.remotedependencytype      Max: 100
* 
    Il tipo di dipendenza remota. I tipi supportati sono SQL, le risorse di AZURE (archiviazione, code e così via) e HTTP. 

**esito positivo**

    boolean remotedependency.success      
* 
    Indica se la chiamata di dipendenza remota ha esito positivo o negativo. 


## richiesta

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Un indicatore che identifica se sono presenti elementi di telemetria correlati in base alla operation.id 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    Il metodo HTTP utilizzato nella richiesta.   

**id**

    string request.id      Max: 100
* 
    Un identificatore univoco di una richiesta e viene generato dal SDK. È possibile popolare ulteriormente questo id per la proprietà id dell’operazione per correlare gli elementi di telemetria risultanti dalla stessa richiesta. 

**responseCode**

    long request.responsecode      
* 
    Il codice di risposta di una richiesta 

**esito positivo**

    boolean request.success      
* 
    Indica se la richiesta ha esito positivo. Il codice di risposta nel gruppo 200 viene considerato corretto. 

    *Predefinito:* se Null, impostare su true


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    Il tempo trascorso dall'ultima visita da questo identificatore utente anonimo. Nella prima visita, questo valore è vuoto. Ad ogni visita successiva, è il tempo tra le visite rappresentate nel giorno. Il valore 3 indica che sono trascorsi 3 giorni dall'istanza di sessione precedente a questa istanza di sessione 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    Il count di visita per l'utente anonimo. È un contatore incrementale della cronologia totale delle sessioni per l'identificatore univoco utente anonimo. Ogni sessione da questo identificatore incrementa il contatore. Questo contatore viene cancellato se l'identificatore dell'utente non viene visualizzato nell’arco di un periodo di 30 giorni, a quel punto il contatore viene reimpostato nuovamente e alla successiva visita l'identificatore utente verrà considerato come un nuovo utente. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    Il tempo trascorso dall'ultima visita da questo identificatore di account. Nella prima visita, questo valore è vuoto. Ad ogni visita successiva, è il tempo tra le visite rappresentate nel giorno. Il valore 3 indica che sono trascorsi 3 giorni dall'istanza di sessione precedente a questa istanza di sessione 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    Questo count di visita per l'identificatore di account autenticato. È un contatore incrementale della cronologia totale delle sessioni per questo identificatore univoco dell’account. Ogni sessione da questo identificatore incrementa il contatore. Questo contatore viene cancellato se l'identificatore dell'utente non viene visualizzato nell’arco di un periodo di 30 giorni, a quel punto il contatore viene reimpostato nuovamente e alla successiva visita l'identificatore utente verrà considerato come un nuovo utente. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    Il tempo trascorso dall'ultima visita da questo identificatore utente autenticato. Nella prima visita, questo valore è vuoto. Ad ogni visita successiva, è il tempo tra le visite rappresentate nel giorno. Il valore 3 indica che sono trascorsi 3 giorni dall'istanza di sessione precedente a questa istanza di sessione 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    Il count di visita per l'identificatore dell'utente autenticato. È un contatore incrementale della cronologia totale delle sessioni per questo identificatore univoco di utente autenticato. Ogni sessione da questo identificatore incrementa il contatore. Questo contatore viene cancellato se l'identificatore dell'utente non viene visualizzato nell’arco di un periodo di 30 giorni, a quel punto il contatore viene reimpostato nuovamente e alla successiva visita l'identificatore utente verrà considerato come un nuovo utente. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    Il numero di arresti anomali che si sono verificati durante l'istanza di sessione. 

**duration**

    timespan sessionmetric.duration      
* 
    La durata di un'istanza di sessione 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    Il primo evento della sessione. Questo viene originato dalla event.name ed è disponibile come una segmentazione/aggregazione per le metriche sessionMetric 

    *Derivazione:* originato da event.name

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    Il primo URL della sessione. Questo viene originato dalla urlData.base ed è disponibile come una segmentazione/aggregazione per le metriche sessionMetric 

    *Derivazione:* originato da &lt;telemetryType&gt;.Url

**eventCount**

    Long sessionmetric.eventcount      
* 
    Il numero di eventi verificatisi durante questa istanza di sessione 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    Il numero di eccezioni che si sono verificate durante l'istanza di sessione 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    L'ultimo evento della sessione. Questo viene originato dalla event.name ed è disponibile come una segmentazione/aggregazione per le metriche sessionMetric 

    *Derivazione:* originato da event.name

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    L'ultimo URL della sessione. Questo viene originato dalla urlData.base ed è disponibile come una segmentazione/aggregazione per le metriche sessionMetric 

    *Derivazione:* originato da &lt;telemetryType&gt;.Url

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    Il count di sessioni di rimbalzo che questo elemento di telemetria sessionMetric rappresenta. Una sessione di rimbalzo è una sessione che viene creata in base a un singolo elemento di telemetria di visualizzazione. 

    *Derivazione:* se sessionMetric.viewCount + sessionMetric.requestCount = 1, then 1 else 0

**pageCount**

    Long sessionmetric.pagecount      
* 
    Il count di visualizzazione che hanno avuto luogo durante l'istanza di sessione 

**requestCount**

    Long sessionmetric.requestcount      
* 
    Il count di richieste che si sono verificate durante l'istanza di sessione 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Un count di sessioni che rappresenta questa istanza sessionMetric di telemetria 


## trace

**contesto**

    string trace.context      Max: 100
* 
    Il contesto del capp in fase di analisi/eccezione 

**exception**:

    string trace.exception      Max: 10240
* 
    L'eccezione associata all'elemento di telemetria della traccia 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Un breve messaggio di testo di un elemento di telemetria di traccia 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    Il format del messaggio per l'elemento di telemetria di traccia 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    Il format del provider per l'elemento di telemetria di traccia 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    Indica se l'elemento di telemetria di traccia include un'analisi dello stack 

**level**

    string trace.level      Max: 100
* 
    Il livello del messaggio di traccia 

**loggerName**

    string trace.loggername      Max: 100
* 
    Il nome del logger di traccia 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    Il nome breve del logger 

**message**

    string trace.message      Max: 10240
* 
    Il messaggio di testo completo di un elemento di telemetria di traccia 

**messageId**

    string trace.messageId      Max: 100
* 
    Un identificatore univoco dell'elemento di telemetria di traccia 

**parameters**

    string trace.parameters      Max: 100
* 
    Questi sono i parametri forniti per la registrazione di traccia per l'elemento di telemetria di traccia 

**processId**

    string trace.processId      Max: 100
* 
    L'id del processo dell'applicazione alla registrazione dell'elemento di telemetria 

**sourceType**

    string trace.sourceType      Max: 100
* 
    Il Sourcetype di un elemento di telemetria di traccia 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Un identificatore di sequenza che potrebbe essere utilizzato da un provider di traccia per registrare la sequenza degli elementi di telemetria di traccia 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Una traccia dello stack acquisita per l'elemento di telemetria di traccia 

**threadId**

    string trace.threadId      Max: 100
* 
    Il threadid dell'applicazione al momento della registrazione dell'elemento di telemetria 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    Lo stack frame utente per l'elemento di telemetria di traccia 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    Il numero di frame dello stack utente per l'elemento di telemetria di traccia 


## visualizzazione

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    L’URL di riferimento del pageview. L'URL completo ed è supportato nella ricerca full-text e nell'esportazione. Questo campo può avere una cardinalità elevata ed è un attributo. È analizzato intl un set di elementi di dati referralData che può essere utilizzato per le aggregazioni in esplorazione di metrica. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Una parte dell'URL di riferimento di esclusione di host, parametri di query. È la radice URI. Questo valore può essere utilizzato per la segmentazione/aggregazione. 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    Il testo dell’hashtag dell’URL di riferimento 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    L'host dell'URL di riferimento. Se l'URL è un URI locale, allora è rappresentato come vuoto 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    La porta dell'URL di riferimento, se è rappresentata nell'URL completo. In caso contrario, è vuoto. 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    Il protocollo (HTTP, FTP e così via) dell'URL di riferimento 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

    *Esempi*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Una matrice dei nomi di parametro di query dell'URL di riferimento 

    *Derivazione:* vedere l'appendice per la trasformazione di URL

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Una matrice di valori del parametro di query analizzati dall'URL referringData. 

    *Derivazione:* vedere l'appendice per la trasformazione di URL



## Vedere anche

* [Application Insights](app-insights-overview.md) 
* [Esportazione continua](app-insights-export-telemetry.md)
* [Esempi di codice](app-insights-export-telemetry.md#code-samples)

<!---HONumber=Sept15_HO2-->