<properties
   pageTitle="Indicazioni sulla rete per la distribuzione di contenuti (CDN) | Microsoft Azure"
   description="Indicazioni sulla rete per la distribuzione di contenuti (CDN) per fornire contenuto a larghezza di banda elevata ospitato in Azure."
   services="cdn"
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/17/2016"
   ms.author="masashin"/>

# Indicazioni sulla rete per la distribuzione di contenuti (CDN)

![Logo di Patterns and Practices](./media/best-practices-cdn/pnp-logo.png)

## Panoramica
La Rete di distribuzione dei contenuti di Microsoft Azure (CDN) offre agli sviluppatori una soluzione globale per distribuire contenuti a larghezza di banda elevata ospitati in Azure o in altre posizioni. Con la rete CDN è possibile memorizzare nella cache gli oggetti disponibili pubblicamente caricati dall'archivio BLOB di Azure un'applicazione Web, una macchina virtuale, una cartella dell'applicazione o altre posizioni HTTP/HTTPS. La cache della rete CDN può essere mantenuta in posizioni strategiche per fornire la larghezza di banda massima per la distribuzione di contenuto agli utenti. In genere, la rete CDN viene usata per recapitare contenuto statico come immagini, fogli di stile, documenti, file script sul lato client e pagine HTML.

È anche possibile usare la rete CDN come una cache per la distribuzione di contenuto dinamico, ad esempio un report PDF o un grafico sulla base di input specificati. Se gli stessi valori di input sono forniti da utenti diversi, il risultato sarà lo stesso.

I principali vantaggi dell'uso della rete CDN sono una latenza più bassa e la distribuzione più rapida di contenuto agli utenti, indipendentemente dalla loro posizione geografica in relazione al data center in cui è ospitata l'applicazione.

![Diagramma della rete CDN](./media/best-practices-cdn/CDN.png)

L'uso della rete CDN sarà utile anche per ridurre il carico sull'applicazione, perché diminuisce l'elaborazione richiesta per accedere al contenuto e distribuirlo. La riduzione del carico può contribuire a migliorare le prestazioni e la scalabilità dell'applicazione, nonché a diminuire i costi di hosting riducendo le risorse di elaborazione necessarie per ottenere un livello specifico di prestazioni e disponibilità.

## Come e perché si usa la rete CDN

Ecco alcuni usi tipici della rete CDN:

+ Distribuzione di risorse statiche per applicazioni client, spesso da un sito Web. Può trattarsi di immagini, fogli di stile, documenti, file, script sul lato client, pagine HTML, frammenti HTML o qualsiasi altro contenuto che il server non debba modificare per ogni richiesta. L'applicazione può creare gli elementi al runtime e renderli disponibili per la rete CDN (ad esempio, creando un elenco di titoli di notizie correnti), ma esegue queste operazioni per ogni richiesta.

+ Distribuzione di contenuto statico e condiviso pubblico a dispositivi quali telefoni cellulari e tablet PC. L'applicazione stessa è un servizio Web che offre un'API ai client in esecuzione sui vari dispositivi. La rete CDN può anche recapitare set di dati statici, tramite il servizio Web, che i client useranno, ad esempio, per generare la propria interfaccia utente. Ad esempio, la rete CDN può essere usata per distribuire documenti JSON o XML.

+ Distribuzione di interi siti Web costituiti da solo contenuto pubblico statico ai client, senza richiedere risorse di calcolo dedicate.

+ File video in streaming al client on demand. I video traggono vantaggio dalla latenza bassa e dalla connettività affidabile disponibili nei centri dati di tutto il mondo che offrono connessioni di rete CDN.

+ Miglioramento generale dell'esperienza per gli utenti, specialmente quelli che si trovano lontano dal data center che ospita l'applicazione, che altrimenti risentirebbero di una latenza più elevata. Gran parte della dimensione totale del contenuto in un'applicazione Web è spesso statica e l’utilizzo della rete CDN consente di mantenere le prestazioni e l’esperienza complessiva dell’utente eliminando la necessità di distribuire l'applicazione in più centri dati.

+ Gestione del carico crescente sulle applicazioni che supportano soluzioni IoT (Internet of Things). L'enorme numero di dispositivi e appliance interessati può facilmente sovraccaricare un'applicazione se fosse necessario elaborare i messaggi trasmessi e gestire direttamente la distribuzione degli aggiornamenti firmware a ogni dispositivo.

+ Gestione dei picchi e della crescita della domanda senza richiedere il ridimensionamento dell'applicazione, evitando il conseguente aumento dei costi di esecuzione. Ad esempio, quando viene rilasciato un aggiornamento del sistema operativo per un dispositivo hardware, come un modello specifico di router, o per un dispositivo di consumo come una smart TV, si verifica un grande picco nella domanda a causa del download eseguito da milioni di utenti e dispositivi in un breve periodo.

L'elenco seguente include esempi di tempi per il primo byte (TTFB), da diverse aree geografiche. Il ruolo Web di destinazione viene distribuito in Azure West US. Esiste una stretta correlazione tra un boost maggiore dovuto alla rete CDN e la vicinanza a un nodo della rete CDN. Per un elenco completo delle posizioni dei nodi della rete CDN, vedere [Posizione dei nodi nella rete per la distribuzione di contenuti (CDN) di Azure](./cdn/cdn-pop-locations.md/).


|| Tempi (ms) per il primo byte (origine) | Tempi (ms) per il primo byte (CDN) | % miglioramento tempi CDN|
|-------------|------------------------|--------------------|------------------|
|*San José, CA| 47.5 | 46.5 | 2 % |
|**Dulles, VA| 109 | 40.5 | 169% |
|Buenos Aires, AR| 210 | 151 | 39%|
|*Londra, UK| 195 | 44 | 343%|
|Shanghai, CN| 242 | 206 | 17% |
|*Singapore | 214 | 74 | 189 % |
|*Tokyo, JP | 163 | 48 | 204 % |
|Seoul, KR| 190 | 190 | 0% |


\* Ha un nodo di rete CDN di Azure nella stessa città.  
\*\* Ha un nodo di rete CDN di Azure in una città adiacente.  

## Sfide  

Esistono varie problematiche da considerare quando si intende utilizzare la rete CDN:

+ **Distribuzione**. È necessario decidere l'origine da cui la rete CDN recupererà il contenuto e se è necessario distribuire il contenuto in più di un sistema di archiviazione, ad esempio nella rete CDN e in una posizione alternativa.

  Il meccanismo di distribuzione delle applicazioni deve tener conto del processo per la distribuzione di contenuto statico e risorse, nonché dei file dell'applicazione, ad esempio le pagine ASPX. Ad esempio, potrebbe essere necessario implementare un passaggio separato per caricare il contenuto nell'archivio BLOB di Azure.

+ **Controllo delle versioni e controllo della cache**. È necessario stabilire come aggiornare il contenuto statico e distribuire nuove versioni. Il contenuto della rete CDN può essere [eliminato](./cdn/cdn-purge-endpoint.md) tramite il portale di Azure quando sono disponibili nuove versioni degli asset. Questo è un problema simile alla gestione della cache sul lato client, come quello che si verifica in un Web browser.

+ **Test**. Può essere difficile eseguire il test locale delle impostazioni della rete CDN durante lo sviluppo e la verifica di un'applicazione in locale o in un ambiente di staging.

+ **Ottimizzazione motore di ricerca (SEO)**. Il contenuto, come immagini e documenti, viene fornito da un dominio diverso quando si usa la rete CDN e questo approccio può avere un impatto su SEO per tale contenuto.

+ **Sicurezza del contenuto**. Molti servizi CDN come la rete CDN di Azure non offrono attualmente alcun tipo di controllo di accesso al contenuto.

+ **Sicurezza del client**. I client possono connettersi da un ambiente che non consente l'accesso alle risorse nella rete CDN. Potrebbe trattarsi di un ambiente con limitazioni di sicurezza che limita l'accesso solo a una serie di origini note oppure di un ambiente che impedisce il caricamento delle risorse da una posizione diversa dall'origine della pagina. Per gestire questi casi è necessaria un'implementazione del fallback.

+ **Resilienza**. La rete CDN è un singolo punto di guasto potenziale per un'applicazione. Presenta un contratto di servizio della disponibilità inferiore all’archiviazione BLOB (che può essere utilizzata per distribuire contenuto direttamente), per cui potrebbe essere necessario implementare un meccanismo di fallback per il contenuto critico.

  È possibile monitorare la disponibilità del contenuto della rete CDN, la larghezza di banda, i dati trasferiti, gli accessi, la percentuale di riscontri nella cache e la metrica della cache dal portale di Azure in [tempo reale](./cdn/cdn-real-time-stats.md) e [aggregare i report](./cdn/cdn-analyze-usage-patterns.md).

Scenari in cui la rete CDN può essere meno utile sono:

+ Se il contenuto ha una bassa percentuale di riscontri, è possibile che l'accesso avvenga solo poche volte durante la sua validità, in base dall'impostazione della durata (TTL). La prima volta che si scarica un elemento avviene l'addebito di due transazioni, dall'origine alla rete CDN e quindi dalla rete CDN al cliente.

+ Se i dati sono privati, ad esempio per aziende di grandi dimensioni o ecosistemi di supply chain.


## Linee guida generali e procedure consigliate

L’utilizzo della rete CDN è un ottimo modo per ridurre al minimo il carico sull'applicazione e ottimizzare disponibilità e prestazioni. È necessario valutare l'adozione di questa strategia per tutto il contenuto appropriato e le risorse usate dall'applicazione. Quando si progetta la strategia per l'uso della rete CDN, considerare i punti nelle sezioni seguenti:


### Origine

La distribuzione del contenuto attraverso la rete CDN richiede semplicemente di specificare un endpoint HTTP e/o HTTPS che il servizio della rete CDN userà per accedere al contenuto e memorizzarlo nella cache.

L'endpoint può specificare un contenitore di archiviazione BLOB di Azure che include il contenuto statico da recapitare attraverso la rete CDN. Il contenitore deve essere contrassegnato come pubblico. Solo i BLOB di un contenitore pubblico con accesso in lettura saranno disponibili tramite la rete CDN.

L'endpoint può specificare una cartella denominata **cdn** nella radice di uno dei livelli di calcolo dell'applicazione (ad esempio un ruolo Web o una macchina virtuale). I risultati dalle richieste di risorse, tra cui le risorse dinamiche, ad esempio le pagine ASPX, verranno memorizzati nella cache nella rete CDN. Il periodo minimo di memorizzazione nella cache è 300 secondi. Un periodo più breve impedirà la distribuzione del contenuto nella rete CDN. Per altre informazioni, vedere la sezione [Controllo cache](#cache-control).

Se si usa App Web di Azure, l'endpoint viene impostato sulla cartella radice del sito selezionando il sito quando si crea l'istanza di rete CDN. Tutto il contenuto per il sito sarà disponibile tramite la rete CDN.

Nella maggior parte dei casi, puntando l'endpoint della rete CDN a una cartella in uno dei livelli di calcolo dell'applicazione si ottiene flessibilità e controllo maggiori. Ad esempio, se semplifica la gestione dei requisiti di routing attuali e futuri e la generazione dinamica di contenuto statico, ad esempio delle anteprime delle immagini.

È possibile usare [stringhe di query](./cdn/cdn-query-string) per differenziare gli oggetti nella cache quando il contenuto viene fornito da origini dinamiche, come le pagine ASPX. Tuttavia, questo comportamento può essere disabilitato da un'impostazione nel portale di Azure quando si specifica l'endpoint della rete CDN. Durante la distribuzione di contenuti dall'archiviazione BLOB, le stringhe di query vengono trattate come valori letterali stringa in modo che due elementi che hanno lo stesso nome ma stringhe di query diverse verranno archiviati come elementi separati sulla rete CDN.

È possibile usare la riscrittura URL per le risorse, ad esempio script e altro contenuto, per evitare di spostare i file nella cartella di origine della rete CDN.

Quando si utilizza il BLOB di archiviazione di Azure per memorizzare il contenuto per la rete CDN, l'URL delle risorse nei BLOB riconosce la differenza tra maiuscole e minuscole per il nome del contenitore e del BLOB.

Quando si usano origini personalizzate o App Web di Azure, è necessario specificare il percorso dell'istanza della rete CDN nei collegamenti alle risorse. Ad esempio, di seguito è specificato un file di immagine nella cartella **Images** del sito che verrà distribuito tramite la rete CDN:

```XML
<img src="http://[your-cdn-endpoint].azureedge.net/Images/image.jpg" />
```

### Distribuzione

È possibile che debba essere effettuato il provisioning e sia necessario distribuire il contenuto indipendentemente dall'applicazione se non viene incluso nel processo o nel pacchetto di distribuzione dell'applicazione. Si consideri come tale eventualità influirà sull’approccio del controllo delle versioni utilizzato per gestire i componenti dell'applicazione e il contenuto stativo della risorsa.

Valutare la modalità di gestione dell’aggregazione in bundle (combinazione di diversi file in un solo file) e minimizzazione (rimozione di caratteri non necessari, ad esempio spazi, caratteri di nuova riga, commenti e così via) per i file CSS e script. Queste tecniche sono comunemente usate per ridurre i tempi di caricamento per i client e sono compatibili con la distribuzione del contenuto tramite la rete CDN. Per ulteriori informazioni, vedere [Creazione di bundle e minimizzazione](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

Se è necessario distribuire il contenuto in un'altra posizione, è necessario un passaggio aggiuntivo nel processo di distribuzione. Se l'applicazione aggiorna il contenuto per la rete CDN, ad esempio a intervalli regolari o in risposta a un evento, è necessario archiviare il contenuto aggiornato in tutti i percorsi aggiuntivi, nonché l'endpoint per la rete CDN.

Non è possibile configurare un endpoint della rete CDN per un'applicazione nell'emulatore di Azure locale in Visual Studio. Questa operazione influirà su unit test, test funzionali e test pre-distribuzione finali. È necessario consentirla implementando un meccanismo alternativo. Ad esempio, è possibile pre-distribuire il contenuto nella rete CDN usando un'applicazione o un'utilità personalizzata ed eseguire i test durante il periodo in cui è memorizzato nella cache. In alternativa, usare direttive di compilazione o costanti globali per controllare l'origine da cui l'applicazione carica le risorse. Ad esempio, durante l'esecuzione in modalità di debug potrebbe caricare risorse quali i bundle di script lato client e altri contenuti da una cartella locale e utilizzare la rete CDN durante l'esecuzione in modalità di rilascio.

Determinare quale approccio di compressione dovrà supportare la rete CDN:

+ È possibile [abilitare la compressione](./cdn/cdn-improve-performance) nel server di origine, nel qual caso la rete CDN supporterà la compressione per impostazione predefinita e distribuirà file compressi ai client, in un formato come ZIP o GZIP. Quando si usa una cartella dell'applicazione come endpoint della rete CDN, il server può comprimere il contenuto automaticamente come quando viene distribuito direttamente a un Web browser o a un altro tipo di client. Il formato dipende dal valore dell'intestazione **Accept-Encoding** nella richiesta inviata dal client. In Azure il meccanismo predefinito prevede la compressione automatica del contenuto quando l'utilizzo della CPU è inferiore al 50%. Se si usa un servizio cloud per ospitare l'applicazione, la modifica delle impostazioni può richiedere l'uso di un'attività di avvio per attivare la compressione dell'output dinamico in IIS. Per al,altre informazioni, vedere il blog relativo all'[abilitazione della compressione GZIP con la rete CDN di Microsoft Azure tramite un ruolo Web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

+ È possibile abilitare la compressione direttamente sui server edge CDN, nel qual caso la rete CDN comprimerà i file e li fornirà agli utenti finali. Per altre informazioni, vedere [Compressione nella rete CDN di Azure](./cdn/cdn-improve-performance.md/).

### Routing e controllo delle versioni

Potrebbe essere necessario usare istanze diverse della rete CDN in momenti diversi. Ad esempio, quando si distribuisce una nuova versione dell'applicazione, è consigliabile usare una nuova rete CDN e quella precedente, mantenendo il contenuto in un formato meno recente, per le versioni precedenti. Se si utilizza l'archiviazione BLOB di Azure come origine di contenuto, è sufficiente creare un account di archiviazione separato o un contenitore separato e puntare l'endpoint della rete CDN ad esso. Se si utilizza la cartella radice *cdn* all'interno dell'applicazione principale come endpoint della rete CDN è possibile utilizzare tecniche di riscrittura degli URL per indirizzare le richieste a una cartella diversa.

Non usare la stringa di query per indicare le diverse versioni dell'applicazione nei collegamenti alle risorse sulla rete CDN, perché quando si recupera il contenuto dall'archivio BLOB di Azure la stringa di query fa parte del nome della risorsa, ovvero il nome del BLOB. Questo approccio può anche influire sul modo in cui il client memorizza le risorse nella cache.

La distribuzione di nuove versioni di contenuto statico quando si aggiorna un'applicazione può essere problematica se le risorse precedenti vengono memorizzate nella cache nella rete CDN. Per altre informazioni, vedere la sezione [Controllo cache](#cache-control").

È consigliabile limitare l'accesso al contenuto della rete CDN in base al paese. La rete CDN di Azure consente di filtrare le richieste in base al paese di origine e limitare il contenuto recapitato. Per altre informazioni, vedere [Limitare l'accesso al contenuto in base al paese](./cdn/cdn-restrict-access-by-country/).

###Controllo cache

Considerare come gestire la memorizzazione nella cache all'interno del sistema. Ad esempio, quando si usa una cartella come origine della rete CDN, è possibile specificare la possibilità di memorizzazione nella cache delle pagine che generano il contenuto e la scadenza del contenuto per tutte le risorse in una cartella specifica. È inoltre possibile specificare le proprietà della cache per la rete CDN e per il client utilizzando le intestazioni HTTP standard. Anche se occorre gestire la memorizzazione nella cache sul server e sul client, l’utilizzo della rete CDN contribuisce a rendere gli utenti più consapevoli della modalità e dell’ubicazione in cui il contenuto viene memorizzato nella cache.

Per impedire che gli oggetti siano disponibili nella rete CDN, è possibile eliminarli dall'origine, ovvero contenitore BLOB o cartella radice *cdn* dell'applicazione, rimuovere o eliminare l'endpoint della rete CDN o, nel caso dell'archivio BLOB, rendere privato il contenitore o il BLOB. Gli elementi saranno tuttavia rimossi dalla rete CDN solo alla scadenza della durata (TTL). Se non viene specificato alcun periodo di scadenza della cache, ad esempio quando il contenuto viene caricato dall'archivio BLOB, il periodo di memorizzazione massimo nella rete CDN è di 7 giorni. È anche possibile [Ripulire un endpoint della rete CDN di Azure](./cdn/cdn-purge-endpoint.md) manualmente.

In un'applicazione Web è possibile impostare la memorizzazione nella cache e la scadenza per tutto il contenuto usando l'elemento *clientCache* nella sezione *system.webServer/staticContent* di un file web.config. Tenere presente che quando si inserisce un file web.config in una cartella, verrà applicato ai file contenuti nella cartella e in tutte le sottocartelle.

Se si crea il contenuto per la rete CDN in modo dinamico, ad esempio nel codice dell'applicazione, assicurarsi di specificare la proprietà *Cache.SetExpires* in ogni pagina. La rete CDN non memorizza nella cache l'output di pagine che usano l'impostazione predefinita di memorizzazione nella cache, ovvero *pubblica*. Impostare il periodo di scadenza della cache su un valore appropriato per garantire che il contenuto non venga eliminato e ricaricato dall'applicazione a intervalli molto brevi.

### Sicurezza

La rete CDN può distribuire il contenuto tramite HTTPS (SSL) usando il certificato fornito dalla rete CDN, ma sarà disponibile anche tramite HTTP. Non è possibile bloccare l'accesso HTTP agli elementi nella rete CDN. Potrebbe essere necessario utilizzare HTTPS per richiedere contenuto statico visualizzato nelle pagine caricate tramite HTTPS (ad esempio un carrello acquisti) per evitare avvisi del browser circa contenuto misto.

La rete CDN di Azure non fornisce funzionalità per il controllo di accesso per proteggere l'accesso al contenuto. Non è possibile utilizzare le firme di accesso condiviso (SAS) con la rete CDN.

Se di distribuiscono script lato client utilizzando la rete CDN, si potrebbero verificare problemi se tali script utilizzano una chiamata *XMLHttpRequest* per effettuare richieste HTTP per altre risorse, ad esempio dati, immagini o caratteri in un dominio diverso. Molti browser impediscono la condivisione di risorse tra origini (CORS), a meno che il server Web non è configurato per impostare le intestazioni di risposta appropriate. È possibile configurare la rete CDN per il supporto di CORS:

+ Se l'origine da cui si distribuisce il contenuto è l'archivio BLOB di Azure, è possibile aggiungere *CorsRule* alle proprietà del servizio. La regola può specificare le origini consentite per le richieste CORS, i metodi consentiti, ad esempio GET, e la durata massima in secondi per la regola (il periodo entro cui il client deve richiedere le risorse collegate dopo il caricamento del contenuto originale). Per altre informazioni, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per i servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

+ Se l'origine da cui si distribuisce il contenuto è una cartella all'interno dell'applicazione, ad esempio la cartella radice *cdn*, è possibile configurare le regole in uscita nel file di configurazione dell'applicazione per impostare un’intestazione *Access-Control-Allow-Origin* in tutte le risposte. Per ulteriori informazioni sull'utilizzo delle regole di riscrittura, vedere [Modulo di riscrittura URL](http://www.iis.net/learn/extensions/url-rewrite-module). Si noti che questa tecnica non è consentita quando si utilizza Siti Web di Azure.

### Domini personalizzati

La rete CDN di Azure consente di specificare un [nome di dominio personalizzato](./cdn/cdn-map-content-to-custom-domain.md) e usarlo per accedere alle risorse tramite la rete CDN. È inoltre possibile impostare un nome di sottodominio personalizzato utilizzando un record *CNAME* nel sistema DNS. L’utilizzo di questo approccio fornisce un ulteriore livello di astrazione e controllo.

Se si usa un *CNAME*, non è possibile usare anche SSL, perché la rete CDN usa il proprio certificato SSL singolo, che non corrisponderà ai nomi di dominio o sottodominio personalizzati.

### Fallback della rete CDN

È necessario considerare come l'applicazione gestirà un errore o l'indisponibilità temporanea della rete CDN. Le applicazioni client potrebbero riuscire a usare copie delle risorse memorizzate nella cache locale del client durante le richieste precedenti oppure è possibile includere codice che rilevi gli errori e richieda invece risorse dall'origine, ovvero la cartella dell'applicazione o il contenitore BLOB di Azure che contiene le risorse, se la rete CDN non è disponibile.

### Ottimizzazione motore di ricerca

Se SEO è un fattore importante nell'applicazione, eseguire queste attività:

+ Includere un'intestazione canonica *Rel* in ogni pagina o risorsa.

+ Utilizzare un recird di sottodominio *CNAME* per accedere alle risorse utilizzando questo nome.

+ Valutare l'impatto dell’eventuale mancata corrispondenza tra paese/regione dell'indirizzo IP della rete CDN e quello dell'applicazione stessa.

+ Quando si utilizza l'archiviazione BLOB di Azure come origine, è necessario mantenere per le risorse nella rete CDN la stessa struttura di file delle cartelle delle applicazioni.


### Monitoraggio e registrazione

Includere la rete CDN come parte della strategia di monitoraggio dell'applicazione per rilevare e misurare gli errori o le occorrenze di latenza estesa. Il monitoraggio è disponibile in Gestione profili della rete CDN che si trova nel sito del portale di Azure.

Abilitare la registrazione per la rete CDN e monitorare il log come parte delle operazioni quotidiane.

Prendere in considerazione l'analisi del traffico della rete CDN per i modelli di utilizzo. Il portale di Azure fornisce strumenti che consentono di monitorare:
+ Larghezza di banda
+ Dati trasferiti
+ Riscontri (codici di stato)
+ Stato della cache
+ Percentuale di riscontri nella cache
+ Percentuale di richieste IPV4/IPV6.

Per altre informazioni, vedere [Analizzare i modelli di utilizzo della rete CDN](./cdn/cdn-analyze-usage-patterns.md/).

### Implicazioni relative ai costi

Vengono addebitati i trasferimenti di dati in uscita dalla rete CDN. Inoltre, se si usa l'archiviazione BLOB per ospitare gli asset, vengono addebitate le transazioni di archiviazione quando la rete CDN carica i dati dall'applicazione. Impostare periodi di scadenza della cache realistici per il contenuto in modo da garantire l’aggiornamento, ma non troppo brevi da causare il ricaricamento ripetuto del contenuto dall'applicazione o dall’archiviazione BLOB nella rete CDN.

Gli elementi che vengono scaricati raramente comporteranno addebiti di due transazioni senza fornire una riduzione significativa del carico del server.

### Creazione di bundle e minimizzazione

Usare la creazione di bundle e la minimizzazione per ridurre le dimensioni delle risorse, ad esempio il codice JavaScript e le pagine HTML archiviati nella rete CDN. Questa strategia può contribuire a ridurre i tempi di download di questi elementi nel client.

La creazione di bundle e la minimizzazione possono essere gestite da ASP.NET. In un progetto MVC, i bundle vengono definiti in *BundleConfig.cs*. Viene creato un riferimento al bundle di script minimizzato chiamando il metodo *Script.Render*, in genere nel codice nella classe di visualizzazione. Questo riferimento contiene una stringa di query che include un hash basato sul contenuto del bundle. Se il contenuto del bundle cambia, verrà modificato anche il valore hash generato.

Per impostazione predefinita, le istanze della rete CDN di Azure hanno l’impostazione dello *stato di stringa query* disabilitata. Affinché i bundle di script aggiornati siano gestiti correttamente dalla rete CDN, è necessario abilitare l’impostazione dello *stato di stringa query* per l'istanza di rete CDN. Per rendere effettiva l'impostazione, potrebbe essere necessaria più di un'ora.


## Codice di esempio
In questa sezione sono riportati alcuni esempi di codice e tecniche per l'utilizzo della rete CDN.


### Riscrittura URL
Il seguente estratto dal file Web.config nella radice di un'applicazione ospitata da Servizi cloud dimostra come eseguire la [riscrittura dell'URL](https://technet.microsoft.com/library/ee215194.aspx) quando si usa la rete CDN. Le richieste di contenuto da memorizzare nella cache provenienti dalla rete CDN vengono reindirizzate a cartelle specifiche nella radice dell'applicazione in base al tipo di risorsa, ad esempio script e immagini.


```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Queste regole di riscrittura eseguono i reindirizzamenti seguenti:

+ La prima regola consente di incorporare una versione nel nome del file di una risorsa, ma viene ignorata. Ad esempio, *Filename\_v123.jpg *viene riscritto come *Filename.jpg*.

+ Le quattro regole successive illustrano come reindirizzare le richieste se non si vogliono archiviare le risorse in una cartella denominata *cdn** nella radice del ruolo Web. Le regole mappano gli URL *cdn/Images*, *cdn/Content*, *cdn/Scripts* e *cdn/bundles* alle rispettive cartelle radice nel ruolo Web.

L'uso della riscrittura di URL richiede di apportare alcune modifiche alla creazione di bundle delle risorse.

## Altre informazioni


+ [Rete CDN di Azure](https://azure.microsoft.com/services/cdn/)
+ [Documentazione sulla rete per la distribuzione di contenuti (CDN) di Azure](https://azure.microsoft.com/documentation/services/cdn/)
+ [Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web](./cdn/cdn-serve-content-from-cdn-in-your-web-application/)
+ [Integrare un servizio cloud con la rete CDN di Azure](./cdn/cdn-cloud-service-with-cdn.md/)
+ [Procedure consigliate per la rete per la distribuzione di contenuti di Microsoft Azure](https://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=AcomDC_0323_2016-->