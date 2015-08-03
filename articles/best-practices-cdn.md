<properties
   pageTitle="Indicazioni sulla rete di distribuzione dei contenuti (CDN) | Microsoft Azure"
   description="Indicazioni sulla rete di distribuzione dei contenuti (CDN) per fornire contenuto a larghezza di banda elevata ospitato in Azure."
   services=""
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
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Indicazioni sulla rete di distribuzione dei contenuti (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Panoramica

La rete di distribuzione dei contenuti (CDN) di Microsoft Azure offre agli sviluppatori una soluzione globale per distribuire contenuti a larghezza di banda elevata ospitati in Azure. La rete CDN memorizza nella cache gli oggetti disponibili pubblicamente caricati dall'archiviazione BLOB di Azure o da una cartella dell'applicazione in posizioni strategiche per fornire la larghezza di banda massima per il recapito dei contenuti agli utenti. In genere viene utilizzata per recapitare contenuto strategico come immagini, fogli di stile, documenti, file script lato client e pagine HTML.

I principali vantaggi derivanti dall'utilizzo della rete CDN sono una latenza più bassa e una distribuzione dei contenuti più veloce agli utenti indipendentemente dalla loro ubicazione geografica in relazione al centro dati in cui è ospitata l'applicazione, oltre a una riduzione del carico sull'applicazione stessa poiché diminuisce dell'elaborazione necessaria per accedere e distribuire il contenuto. La riduzione del carico può aiutare a migliorare le prestazioni e la scalabilità dell'applicazione, nonché a diminuire il costo di hosting riducendo le risorse di elaborazione necessarie per ottenere uno specifico livello di prestazioni e disponibilità.

È possibile utilizzare altri sistemi di rete di distribuzione dei contenuti non implementate da Azure nelle applicazioni se la rete CDN di Azure non soddisfa le proprie esigenze. In alternativa, è possibile utilizzare la rete CDN di Azure per le applicazioni ospitate con altri provider esponendo il contenuto statico nell’archiviazione di Azure o in istanze di calcolo di Azure. ![](media/best-practices-cdn/CDN.png)

## Come e perché viene utilizzata la rete CDN

Tipici utilizzi della rete CDN sono:

- Distribuzione di risorse statiche per applicazioni client, spesso da un sito Web. Può trattarsi di immagini, fogli di stile, documenti, file, script lato client, pagine HTML, frammenti HTML o qualsiasi altro contenuto che il server non deve modificare per ogni richiesta. L'applicazione può creare gli elementi al runtime e renderli disponibili per la rete CDN (ad esempio, creando un elenco di titoli di notizie correnti), ma esegue queste operazioni per ogni richiesta.

- Distribuzione di contenuto pubblico statico e condiviso pubblico a dispositivi quali telefoni cellulari e PC tablet in cui l'applicazione stessa è un servizio Web che offre un'API ai client. Oltre ad altri contenuti che il server non deve modificare per ogni richiesta, la rete CDN può distribuire set di dati statici utilizzabili dal client, ad esempio per generare l'interfaccia utente del client. Ad esempio, può essere utilizzato per recapitare documenti JSON o XML.

- Distribuzione di interi siti Web costituiti da solo contenuto pubblico statico ai client, senza richiedere risorse di calcolo dedicate.

- File video in streaming al client on demand. I video traggono vantaggio dalla latenza bassa e dalla connettività affidabile disponibili nei centri dati di tutto il mondo che offrono connessioni di rete CDN.

- Un generale miglioramento dell'esperienza per gli utenti, specialmente quelli che si trovano lontano dal cento dati dell'applicazione che altrimenti risentirebbero di una latenza più elevata. Gran parte della dimensione totale del contenuto in un'applicazione Web è spesso statica e l’utilizzo della rete CDN consente di mantenere le prestazioni e l’esperienza complessiva dell’utente eliminando la necessità di distribuire l'applicazione in più centri dati.

- Gestione del carico crescente sulle applicazioni che servono dispositivi fissi e mobili che fanno parte di Internet delle cose (IoT). L’enorme numero di tali dispositivi e applicazioni potrebbe facilmente sovraccaricare l’applicazione se occorre elaborare i messaggi trasmessi e gestire direttamente la distribuzione degli aggiornamenti firmware.

- Gestione dei picchi di domanda senza richiedere che l'applicazione venga scalata, evitando il conseguente incremento dei costi di esecuzione. Ad esempio, quando viene rilasciato un aggiornamento di un sistema operativo, per un dispositivo hardware come un modello specifico di router o per un dispositivo consumer come un televisore smart, si verifica un grande picco nella domanda a causa del download eseguito da milioni di utenti e dispositivi in un breve periodo.

- Nella tabella seguente vengono illustrati esempi di tempo medio per il primo byte da diverse posizioni geografiche. Il ruolo Web di destinazione viene distribuito in Azure West US. Esiste una stretta correlazione tra un boost maggiore dovuto alla rete CDN e la vicinanza a un nodo della rete CDN. Per un elenco di tutte le ubicazioni dei nodi della rete CDN, vedere [Località POP della rete per la distribuzione di contenuti (CDN) di Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Tempi per il primo byte (origine)</p></th><th><p>Tempi per il primo byte (CDN)</p></th><th><p>% più veloce per CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* Londra, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Ha un nodo della rete CDN di Azure nella stessa città. * Ha un nodo della rete CDN di Azure in una città adiacente.


## Problematiche  

Esistono varie problematiche da considerare quando si intende utilizzare la rete CDN:

- **Distribuzione**: è necessario decidere dove caricare il contenuto per la rete CDN (l'origine da cui la rete CDN recupererà il contenuto) e se è necessario distribuire il contenuto in più di un sistema di archiviazione (ad esempio nella rete CDN e in una posizione alternativa).

- Il meccanismo di distribuzione di applicazioni deve tener conto della distribuzione di contenuto statico e risorse, nonché dei file di applicazione, ad esempio pagine ASPX. Ad esempio, potrebbe essere necessario un passaggio separato per caricare il contenuto nell'archiviazione BLOB di Azure.

- **Controllo delle versioni e controllo della cache**: è necessario stabilire come aggiornare il contenuto statico e distribuire nuove versioni. La rete CDN attualmente non fornisce un meccanismo per lo svuotamento del contenuto in modo che sino disponibili nuove versioni. Questo è un problema simile alla gestione della cache lato client, ad esempio un web browser.

- **Test**: può essere difficile eseguire la verifica locale delle impostazioni di rete CDN durante lo sviluppo e la verifica di un'applicazione in locale o in gestione temporanea.

- **SEO**: contenuti come immagini e documenti vengono serviti da un dominio diverso quando utilizzano la rete CDN con conseguente effetto sul SEO per questo contenuto.

- **Protezione**: molti servizi CDN come la rete CDN di Azure attualmente non offrono alcun tipo di controllo di accesso per il contenuto.

- **Resilienza**: la rete CDN è un potenziale singolo punto di errore per un'applicazione. Presenta un contratto di servizio della disponibilità inferiore all’archiviazione BLOB (che può essere utilizzata per distribuire contenuto direttamente), per cui potrebbe essere necessario implementare un meccanismo di fallback per il contenuto critico.

- I client potrebbero connettersi da un ambiente che non consente l'accesso alle risorse nella rete CDN. Potrebbe trattarsi di un ambiente con limitazioni di sicurezza che limita l'accesso solo a una serie di origini note oppure di un ambiente che impedisce il caricamento delle risorse da una posizione diversa dall'origine della pagina. Pertanto, sarà necessaria un'implementazione del fallback.

- È necessario implementare un meccanismo per monitorare la disponibilità del contenuto attraverso la rete CDN.

Scenari in cui la rete CDN può essere meno utile sono:

- Quando il contenuto ha una bassa frequenza di riscontri e pertanto è possibile accedervi poche volte, o solo una, durante il periodo di validità TTL. La prima volta che viene scaricato un elemento si incorre in due costi di transazione (dall'origine alla rete CDN e quindi dalla rete CDN al cliente).

- Quando i dati sono privati, ad esempio per aziende di grandi dimensioni o ecosistemi di catene di fornitura.


## Linee guida generali e procedure consigliate

L’utilizzo della rete CDN è un ottimo modo per ridurre al minimo il carico sull'applicazione e ottimizzare disponibilità e prestazioni. È necessario valutare questo mezzo per tutti i contenuti e le risorse utilizzate. Quando si progetta la strategia di utilizzo della rete CDN, tenere presente quanto segue:

- **Origine ** La semplice distribuzione del contenuto attraverso la rete CDN richiede di specificare un endpoint HTTP (porta 80) che il servizio CDN utilizzerà per accedere al contenuto e memorizzarlo nella cache . + L'endpoint può specificare un contenitore di archiviazione BLOB di Azure che contiene il contenuto statico da recapitare attraverso la rete CDN. Il contenitore deve essere contrassegnato come pubblico. Solo i BLOB di un contenitore pubblico con accesso in lettura saranno disponibili tramite la rete CDN. 
- L'endpoint può specificare una cartella denominata **cdn** nella radice di uno dei livelli di calcolo dell'applicazione (ad esempio un ruolo Web o una macchina virtuale). I risultati dalle richieste di risorse, tra cui le risorse dinamiche, ad esempio le pagine ASPX, verranno memorizzati nella cache nella rete CDN. Il periodo minimo nella cache è 300 secondi. Qualsiasi periodo più breve impedirà la distribuzione del contenuto nella rete CDN (per ulteriori informazioni, vedere la sezione ""<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Controllo della cache</a>").

- Se si utilizza Siti Web di Azure, l'endpoint viene impostato sulla cartella radice del sito selezionando il sito quando si crea l'istanza di rete CDN. Tutto il contenuto per il sito sarà disponibile tramite la rete CDN.

- Nella maggior parte dei casi, puntando l'endpoint della rete CDN a una cartella in uno dei livelli di calcolo dell'applicazione si ottiene flessibilità e controllo maggiori. Ad esempio, se semplifica la gestione dei requisiti di routing attuali e futuri e la generazione dinamica di contenuto statico, ad esempio delle anteprime delle immagini.

- È possibile utilizzare stringhe di query per differenziare gli oggetti nella cache quando il contenuto viene fornito da origini dinamiche, ad esempio pagine ASPX. Tuttavia, questo comportamento può essere disabilitato da un'impostazione nel portale di gestione quando si specifica l'endpoint della rete CDN. Durante la distribuzione di contenuti dall'archiviazione BLOB, le stringhe di query vengono trattate come valori letterali stringa in modo che due elementi che hanno lo stesso nome ma stringhe di query diverse verranno archiviati come elementi separati sulla rete CDN.

- È possibile utilizzare la riscrittura URL per le risorse, ad esempio script e altri contenuti, per evitare di spostare i file nella cartella di origine della rete CDN.

- Quando si utilizza il BLOB di archiviazione di Azure per memorizzare il contenuto per la rete CDN, l'URL delle risorse nei BLOB riconosce la differenza tra maiuscole e minuscole per il nome del contenitore e del BLOB.

- Quando si utilizza Siti Web di Azure, è necessario specificare il percorso dell'istanza della rete CDN nei collegamenti alle risorse. Ad esempio, di seguito è specificato un file di immagine nella cartella **Images** del sito che verrà distribuito tramite la rete CDN:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Distribuzione**: è possibile che il contenuto statico debba essere predisposto e distribuito in modo indipendente dall'applicazione se non viene incluso nel processo o pacchetto di distribuzione applicazione. Si consideri come tale eventualità influirà sull’approccio del controllo delle versioni utilizzato per gestire i componenti dell'applicazione e il contenuto stativo della risorsa.

- Valutare la modalità di gestione dell’aggregazione in bundle (combinazione di diversi file in un solo file) e minimizzazione (rimozione di caratteri non necessari, ad esempio spazi, caratteri di nuova riga, commenti e così via) per i file CSS e script. Queste tecniche comunemente utilizzate consentono di ridurre i tempi di caricamento per i client e sono compatibili con la distribuzione di contenuto tramite la rete CDN. Per ulteriori informazioni, vedere [Creazione di bundle e minimizzazione](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Se è necessario distribuire il contenuto in un'altra posizione, è necessario un passaggio aggiuntivo nel processo di distribuzione. Se l'applicazione aggiorna il contenuto per la rete CDN, ad esempio a intervalli regolari o in risposta a un evento, è necessario archiviare il contenuto aggiornato in tutti i percorsi aggiuntivi, nonché l'endpoint per la rete CDN.

- Non è possibile configurare un endpoint della rete CDN per un'applicazione distribuita nella gestione temporanea di Azure o nell'emulatore di Azure locale in Visual Studio. Questa operazione influirà su unit test, test funzionali e test di pre-distribuzione finale. È necessario consentirla implementando un meccanismo alternativo. Ad esempio, è possibile pre-distribuire il contenuto nella rete CDN utilizzando un'applicazione o utilità personalizzata temporanea ed effettuare un test durante il periodo di memorizzazione nella cache. In alternativa, utilizzare direttive di compilazione o costanti globali per controllare la posizione da cui l'applicazione carica le risorse. Ad esempio, durante l'esecuzione in modalità di debug potrebbe caricare risorse quali i bundle di script lato client e altri contenuti da una cartella locale e utilizzare la rete CDN durante l'esecuzione in modalità di rilascio.

- La rete CDN non supporta tutte le funzionalità native di compressione. Tuttavia, offrirà contenuto già compresso, ad esempio i file zip e gzip. Quando si utilizza una cartella dell'applicazione come endpoint della rete CDN, il server può comprimere alcuni contenuti per impostazione predefinita come quando vengono distribuiti direttamente a un browser Web o altro tipo di client. Questo comportamento si basa sul valore **Accept-Encoding** inviato dal client. In Azure l'impostazione predefinita prevede la compressione automatica del contenuto quando l'utilizzo della CPU è inferiore al 50%. La modifica delle impostazioni potrebbe richiedere l'utilizzo di un'attività di avvio per attivare la compressione dell'output dinamico in IIS se si utilizza Servizi cloud per ospitare l'applicazione. Per ulteriori informazioni, vedere [Enabling gzip compression with Azure CDN through a Web Role](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

- **Routing e controllo delle versioni**: potrebbe essere necessario utilizzare istanze diverse della rete CDN. Ad esempio, quando si distribuisce una nuova versione dell'applicazione è possibile utilizzare una rete CDN diversa. Se si utilizza l'archiviazione BLOB di Azure come origine di contenuto, è sufficiente creare un account di archiviazione separato o un contenitore separato e puntare l'endpoint della rete CDN ad esso. Se si utilizza la cartella radice **cdn** all'interno dell'applicazione principale come endpoint della rete CDN è possibile utilizzare tecniche di riscrittura degli URL per indirizzare le richieste a una cartella diversa.

- Non utilizzare la stringa di query per indicare le diverse versioni dell'applicazione nei collegamenti alle risorse nella rete CDN perché quando si estrae contenuto dall'archiviazione BLOB di Azure la stringa di query è parte del nome della risorsa (il nome del BLOB). Inoltre si potrebbe sul modo in cui il client memorizza le risorse nella cache.

- La distribuzione di nuove versioni di contenuto statico quando si aggiorna un'applicazione può essere problematica se le risorse precedenti vengono memorizzate nella cache nella rete CDN. Per ulteriori informazioni, vedere la sezione "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Controllo della cache</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Controllo della cache**+ Stabilire come gestire la memorizzazione nella cache e a quali livelli dell’applicazione. Ad esempio, quando si utilizza una cartella come origine della rete CDN è possibile specificare la possibilità di memorizzazione nella cache delle pagine che generano contenuto e la scadenza del contenuto per tutte le risorse in una cartella specifica. È inoltre possibile specificare le proprietà della cache per la rete CDN e per il client utilizzando le intestazioni HTTP standard. Anche se occorre gestire la memorizzazione nella cache sul server e sul client, l’utilizzo della rete CDN contribuisce a rendere gli utenti più consapevoli della modalità e dell’ubicazione in cui il contenuto viene memorizzato nella cache.

- Per impedire che gli oggetti siano disponibili nella rete CDN è possibile eliminarli dall'origine (contenitore BLOB o cartella radice **cdn** dell’applicazione), rimuovere o eliminare endpoint della rete CDN o, nel caso dell’archiviazione BLOB, rendere privato il contenitore o BLOB. Tuttavia, gli elementi verranno rimossi dalla rete CDN solo quando il time-to-live (TTL) scade.

- Se non viene specificato alcun periodo di scadenza della cache (ad esempio quando il contenuto viene caricato dall'archiviazione BLOB), il periodo di memorizzazione massimo nella rete CDN è 72 ore.

- In un'applicazione Web, è possibile impostare la memorizzazione nella cache e la scadenza per tutto il contenuto utilizzando l’elemento **clientCache** nella sezione **system.webServer/staticContent** di un file web. config. È possibile inserire un file web.config in qualsiasi cartella in modo che venga applicato ai file di tale cartella e ai file di tutte le sottocartelle.

- Se si utilizza una tecnica dinamica, ad esempio ASP.NET, per creare il contenuto per la rete CDN, assicurarsi di specificare la proprietà **Cache.SetExpires** in ogni pagina. La rete CDN non memorizza nella cache l'output di pagine che utilizzano l'impostazione predefinita di memorizzazione nella cache, ossia pubblica. Impostare il periodo di scadenza della cache su un valore appropriato per garantire che il contenuto non venga eliminato e ricaricato dall'applicazione a intervalli molto brevi.

- **Protezione**: la rete CDN può distribuire i contenuti tramite HTTPS (SSL) utilizzando il certificato fornito dalla rete CDN, ma sarà disponibile anche tramite HTTP. Non è possibile bloccare l'accesso HTTP agli elementi nella rete CDN. Potrebbe essere necessario utilizzare HTTPS per richiedere contenuto statico visualizzato nelle pagine caricate tramite HTTPS (ad esempio un carrello acquisti) per evitare avvisi del browser circa contenuto misto.

- Molti servizi CDN, ad esempio la rete CDN di Azure, attualmente non offrono funzionalità per il controllo di accesso per proteggere l'accesso al contenuto. Non è possibile utilizzare le firme di accesso condiviso (SAS) con la rete CDN.

- Se di distribuiscono script lato client utilizzando la rete CDN, si potrebbero verificare problemi se tali script utilizzano una chiamata **XMLHttpRequest** per effettuare richieste HTTP per altre risorse, ad esempio dati, immagini o caratteri in un dominio diverso. Molti browser impediscono la condivisione di risorse tra origini (CORS), a meno che il server Web non è configurato per impostare le intestazioni di risposta appropriate. Per ulteriori informazioni sulla condivisione CORS, vedere la sezione "Attenuazione delle minacce" nella guida <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Considerazioni sulla protezione di API</span>. È possibile configurare la rete CDN per il supporto di CORS: + se l'origine da cui si trasferisce il contenuto è l’archiviazione BLOB di Azure, è possibile aggiungere **CorsRule** alle proprietà del servizio. La regola può specificare le origini consentite per le richieste CORS, i metodi consentiti, ad esempio GET, e la durata massima in secondi per la regola (il periodo entro cui il client deve richiedere le risorse collegate dopo il caricamento del contenuto originale). Per altre informazioni, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per i servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Se l'origine da cui si distribuisce il contenuto è una cartella all'interno dell'applicazione, ad esempio la cartella radice **cdn**, è possibile configurare le regole in uscita nel file di configurazione dell'applicazione per impostare un’intestazione **Access-Control-Allow-Origin** in tutte le risposte. Per ulteriori informazioni sull'utilizzo delle regole di riscrittura, vedere [Modulo di riscrittura URL](http://www.iis.net/learn/extensions/url-rewrite-module). Si noti che questa tecnica non è consentita quando si utilizza Siti Web di Azure.

- **Domini personalizzati**:+ la maggior parte delle reti CDN, tra cui la rete CDN di Azure, consente di specificare un nome di dominio personalizzato e di utilizzarlo per accedere alle risorse tramite la rete CDN. È inoltre possibile impostare un nome di sottodominio personalizzato utilizzando un record **CNAME** nel sistema DNS. L’utilizzo di questo approccio fornisce un ulteriore livello di astrazione e controllo.

- Se si utilizza un **CNAME**, non è possibile (al momento in cui è stata scritta questa guida) utilizzare anche SSL perché la rete CDN utilizza il proprio certificato SSL singolo, che non corrisponderà a nomi di dominio o sottodominio personalizzati.

- **Fallback CDN** è necessario considerare come l'applicazione farà fronte a un errore o all'indisponibilità temporanea della rete CDN. Le applicazioni client possono essere in grado di utilizzare copie delle risorse memorizzate nella cache locale (sul client) durante le richieste precedenti oppure di utilizzare il codice che rileva gli errori e invece richiede risorse dall’origine (la cartella dell'applicazione o il contenitore BLOB di Azure che contiene le risorse) se non è disponibile la rete CDN.

- **SEO**: se SEO è un aspetto importante nell’applicazione, accertarsi di includere un’intestazione canonica **Rel** in ogni pagina o risorsa.

- Utilizzare un recird di sottodominio **CNAME** per accedere alle risorse utilizzando questo nome.

- Valutare l'impatto dell’eventuale mancata corrispondenza tra paese/regione dell'indirizzo IP della rete CDN e quello dell'applicazione stessa.

- Quando si utilizza l'archiviazione BLOB di Azure come origine, è necessario mantenere per le risorse nella rete CDN la stessa struttura di file delle cartelle delle applicazioni.


- **Monitoraggio e registrazione in log**: includere la rete CDN come parte della strategia di monitoraggio dell’applicazione per rilevare e misurare gli errori o le ricorrenze di latenza estese.

- Abilitare la registrazione in logo per la rete CDN e includerla come parte delle operazioni quotidiane.

- **Implicazioni relative ai costi**: vengono addebitati costi sia per i trasferimenti di dati in uscita dalla rete CDN sia per le transazioni di archiviazione quando la rete CDN carica i dati dall'applicazione. Impostare periodi di scadenza della cache realistici per il contenuto in modo da garantire l’aggiornamento, ma non troppo brevi da causare il ricaricamento ripetuto del contenuto dall'applicazione o dall’archiviazione BLOB nella rete CDN. Tuttavia, periodi di scadenza molto lunghi rendono più difficile rimuovere gli elementi dalla rete CDN poiché è necessario attenderne la scadenza.

- Gli elementi che vengono scaricati raramente comporteranno addebiti di due transazioni senza fornire una riduzione significativa del carico del server.



## Codice di esempio
In questa sezione sono riportati alcuni esempi di codice e tecniche per l'utilizzo della rete CDN.


## Riscrittura URL
Il seguente frammento di file Web.config nella directory radice di un’applicazione ospitata da Servizi cloud dimostra come eseguire la riscrittura URL quando si utilizza la rete CDN. Le richieste di contenuto da memorizzare nella cache provenienti dalla rete CDN vengono reindirizzate a cartelle specifiche all'interno della radice dell'applicazione in base al tipo di risorsa (ad esempio script e immagini).

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*).(.*)" ignoreCase="true" />
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

L'aggiunta di regole di riscrittura esegue i reindirizzamenti seguenti:

- La prima regola consente di incorporare una versione nel nome del file di una risorsa, ma viene ignorata. Ad esempio, **Filename_v123.jpg ** viene riscritto come **Filename.jpg**. 
- Le quattro regole successive illustrano come reindirizzare le richieste se non si desidera archiviare le risorse in una cartella denominata **cdn** nella radice del ruolo Web. Le regole mappano gli URL **cdn/Images**, **cdn/Content**, **cdn/Scripts** e **cdn/bundles** alle rispettive cartelle radice nel ruolo Web. L’utilizzo della riscrittura URL richiede di apportare alcune modifiche per la creazione di bundle delle risorse.


## Creazione di bundle e minimizzazione ##

La creazione di bundle e la minimizzazione possono essere gestite da ASP.NET. In un progetto MVC, i bundle vengono definiti in **BundleConfig.cs**. Viene creato un riferimento al bundle di script minimizzato chiamando il metodo **Script.Render**, in genere nel codice nella classe di visualizzazione. Questo riferimento contiene una stringa di query che include un hash basato sul contenuto del bundle. Se il contenuto del bundle cambia, verrà modificato anche il valore hash generato. Per impostazione predefinita, le istanze della rete CDN di Azure hanno l’impostazione dello **stato di stringa query** disabilitata. Affinché i bundle di script aggiornati siano gestiti correttamente dalla rete CDN, è necessario abilitare l’impostazione dello **stato di stringa query** per l'istanza di rete CDN. Si noti che per la creazione della rete CDN e l’applicazione delle modifiche delle impostazioni potrebbe essere necessaria un'ora o più.


## Ulteriori informazioni
+ [Rete CDN di Azure](http://azure.microsoft.com/services/cdn/)
+ [Panoramica della Rete per la distribuzione di contenuti (rete CDN) di Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Integrare un servizio cloud con la rete CDN di Azure](cdn-cloud-service-with-cdn.md)
+ [Procedure consigliate per la rete di distribuzione dei contenuti di Azure](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=July15_HO4-->