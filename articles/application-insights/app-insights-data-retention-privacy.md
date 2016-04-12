<properties 
	pageTitle="Conservazione e archiviazione dei dati in Application Insights" 
	description="Informativa sulla conservazione e sulla privacy" 
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
	ms.date="03/15/2016" 
	ms.author="awills"/>

# Raccolta, conservazione e archiviazione di dati in Application Insights 

*Application Insights è disponibile in anteprima.*

Quando si installa [Visual Studio Application Insights][start] SDK nell'app, vengono inviati i dati di telemetria sull'app nel cloud. Naturalmente, gli sviluppatori responsabili vogliono sapere esattamente quali dati vengono inviati, cosa accade ai dati e come possono mantenerne il controllo. In particolare, devono sapere se possono essere inviati dati sensibili, dove vengono archiviati e quale livello di sicurezza viene applicato.


Innanzitutto, chiariamo alcuni aspetti:

* È improbabile che i moduli di telemetria standard che seguono un comportamento predefinito possano inviare dati sensibili al servizio. I dati di telemetria riguardano metriche di carico, prestazioni e utilizzo, report di eccezioni e altri dati di diagnostica. I principali dati utente visibili nei report di diagnostica sono URL, ma l'app non deve in ogni caso inserire dati sensibili in testo normale in un URL.
* È possibile scrivere codice che invia dati di telemetria personalizzati aggiuntivi per agevolare la diagnostica e il monitoraggio dell'utilizzo. Questa flessibilità è un'eccellente funzionalità di Application Insights. Sarebbe possibile, per errore, scrivere il codice in modo che includa dati personali e altri dati sensibili. Pertanto, se l'applicazione utilizza questo tipo di dati, è consigliabile applicare processi di revisione efficaci a tutto il codice creato.
* Durante lo sviluppo e il test dell'app, è facile controllare ciò che viene inviato dall’SDK. I dati vengono visualizzati nelle finestre di output del debug dell’IDE e del browser. 
* I dati vengono archiviati nei server di [Microsoft Azure](http://azure.com) negli Stati Uniti. Azure offre [processi di sicurezza efficaci e soddisfa una vasta gamma di standard di conformità](https://azure.microsoft.com/support/trust-center/). Solo lo sviluppatore dell’app e il team designato hanno accesso ai dati. Il personale Microsoft può avere accesso limitato ai dati solo in determinate circostanze con il consenso dello sviluppatore. I dati sono crittografati durante il transito, anche se non lo sono nel server.

Nella parte restante di questo articolo verranno elaborate ulteriormente queste risposte. Questa parte è progettata per essere indipendente dal resto, pertanto è possibile mostrarla ai colleghi che non fanno parte del proprio team.


## Informazioni su Azure Application Insights

[Visual Studio Application Insights][start] è un servizio Microsoft che consente di migliorare le prestazioni e la facilità d’uso di un'applicazione live. Esegue il monitoraggio dell'applicazione per tutto il tempo che è in esecuzione, sia durante il test che dopo la pubblicazione o la distribuzione. Application Insights crea grafici e tabelle che illustrano, ad esempio, in quali ore del giorno si ottengono più utenti, i tempi di risposta dell'app e come funzionano i servizi esterni da cui dipende. Se sono presenti arresti anomali del sistema, errori o problemi di prestazioni, è possibile cercare i dati di telemetria in dettaglio per diagnosticare la causa. Inoltre, il servizio invierà messaggi di posta elettronica in caso di modifiche della disponibilità e delle prestazioni dell'app.

Per ottenere questa funzionalità, installare Application Insights SDK nell'applicazione, che diventa parte del codice. Quando l'app è in esecuzione, l’SDK monitora il funzionamento e invia i dati di telemetria al servizio Application Insights. Si tratta di un servizio cloud ospitato da [Microsoft Azure](http://azure.com). Application Insights funziona tuttavia per tutte le applicazioni, non solo quelle ospitate in Azure.

![L’SDK nell'app invia dati di telemetria al servizio Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Il servizio Application Insights archivia e analizza i dati di telemetria. Per visualizzare l'analisi o eseguire una ricerca nei dati di telemetria archiviati, accedere con il proprio account Azure e aprire la risorsa Application Insights per l'applicazione. È anche possibile condividere l'accesso ai dati con altri membri del team o con determinati sottoscrittori di Azure.

È possibile fare in modo che i dati vengano esportati dal servizio Application Insights, ad esempio in un database o in strumenti esterni. A ogni strumento è possibile assegnare una chiave speciale ottenuta dal servizio, che può essere revocata se necessario.

Sono disponibili Application Insights SDK per una gamma di tipi di applicazioni: servizi Web ospitati nei server J2EE o ASP.NET o in Azure; client Web, ovvero il codice in esecuzione in una pagina Web; app desktop e servizi; app per dispositivi, ad esempio Windows Phone, iOS e Android. Tutti inviano i dati di telemetria allo stesso servizio.

## Quali dati raccoglie?

### Come vengono raccolti?

Esistono tre origini dati:

* L’SDK, che viene integrato nell'app [in fase di sviluppo](app-insights-asp-net.md) o [in fase di esecuzione](app-insights-monitor-performance-live-website-now.md). Sono disponibili diversi SDK per diversi tipi di applicazione. È inoltre disponibile un [SDK per le pagine Web](app-insights-javascript.md), che viene caricato nel browser dell'utente finale insieme alla pagina.

 * Ogni SDK include un numero di [moduli](app-insights-configuration-with-applicationinsights-config.md), che utilizzano tecniche diverse per raccogliere tipi diversi di dati di telemetria.
 * Se si installa l’SDK in fase di sviluppo, è possibile utilizzare l'API per inviare dati di telemetria personalizzati, oltre ai moduli standard. Tali dati di telemetria personalizzati possono includere qualsiasi tipo di dati si voglia inviare.
* In alcuni server Web sono disponibili anche agenti che vengono eseguiti insieme all'applicazione e inviano dati di telemetria su CPU, memoria e occupazione della rete. Ad esempio, macchine virtuali di Azure, host Docker e [server J2EE](app-insights-java-agent.md) possono disporre di tali agenti.
* I [test di disponibilità](app-insights-monitor-web-app-availability.md) sono processi eseguiti da Microsoft che inviano richieste all'app Web a intervalli regolari. I risultati vengono inviati al servizio Application Insights.

### Quali tipi di dati vengono raccolti?

Le categorie principali sono:

* [Dati di telemetria del server Web](app-insights-asp-net.md): richieste HTTP, URI, tempo impiegato per elaborare la richiesta, codice di risposta, indirizzo IP del client, ID sessione.
* [Pagine Web](app-insights-javascript.md): numero di pagine, utenti e sessioni, tempo di caricamento della pagina, eccezioni, chiamate AJAX.
* Contatori delle prestazioni: memoria, CPU, IO, occupazione della rete.
* Contesto client e server: sistema operativo, impostazioni locali, tipo di dispositivo, browser, risoluzione dello schermo.
* [Eccezioni](app-insights-asp-net-exceptions.md) e arresti anomali: **dump dello stack**, ID compilazione, tipo di CPU. 
* [Dipendenze](app-insights-asp-net-dependencies.md): chiamate ai servizi esterni, ad esempio REST, SQL, AJAX; URI o stringa di connessione, durata, esito positivo, comando.
* [Test di disponibilità](app-insights-monitor-web-app-availability.md): durata del test e passaggi, risposte.
* [Log di traccia](app-insights-search-diagnostic-logs.md) e [dati di telemetria personalizzati](app-insights-api-custom-events-metrics.md): **tutto ciò che viene registrato nei log o i dati di telemetria**.

[Maggiori dettagli](#data-sent-by-application-insights).

## Come è possibile verificare cosa viene raccolto?

Se si sviluppa l'app con Visual Studio, eseguire l'app in modalità di debug (F5). I dati di telemetria vengono visualizzati nella finestra di output. Da qui, è possibile copiarli e formattarli come JSON per semplificare l'ispezione.

![](./media/app-insights-data-retention-privacy/06-vs.png)

È inoltre disponibile una visualizzazione più leggibile nella finestra di diagnostica.

Per le pagine Web, aprire la finestra di debug del browser.

![Premere F12 e aprire la scheda Rete.](./media/app-insights-data-retention-privacy/08-browser.png)

### È possibile scrivere codice per filtrare i dati di telemetria prima che vengano inviati?

Questo sarebbe possibile scrivendo un [plug-in del processore di telemetria](app-insights-api-filtering-sampling.md).



## Per quanto tempo vengono conservati i dati? 

Dipende dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/).

I punti dati non elaborati, ovvero gli elementi ispezionabili nella ricerca diagnostica, vengono conservati per un periodo di 7 giorni. Se si desidera mantenere i dati più lungo, è possibile usare l'[esportazione continua](app-insights-export-telemetry.md) per copiarli in un account di archiviazione.

I dati aggregati, ovvero conteggi, medie e altri dati statistici visualizzati in Esplora metriche, vengono conservati con livello di dettaglio di un minuto per 30 giorni e con livello di dettaglio di un'ora o un giorno, a seconda del tipo, per almeno 90 giorni.


## Chi può accedere ai dati?

I dati sono visibili all'utente e, se si usa un account aziendale, ai membri del team dell'utente.

Possono essere esportati dall'utente e dai membri del team e possono essere copiati in altri percorsi e passati ad altri utenti.

#### In che modo Microsoft usa le informazioni inviate dall'app ad Application Insights?

Microsoft usa i dati solo al fine di fornire il servizio all'utente.


## Dove vengono archiviati i dati? 

* Negli Stati Uniti. 

#### È possibile archiviarli altrove, ad esempio in Europa? 

* Attualmente no. 

## Quanto sono sicuri i dati?  

Application Insights è un servizio di Azure disponibile in anteprima. Durante la fase di anteprima Microsoft si impegna a proteggere i dati degli utenti in base ai criteri descritti nel [white paper su sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408).


I dati vengono archiviati nei server di Microsoft Azure. Per gli account nel portale di Azure, le restrizioni relative all'account sono illustrate nel [documento relativo a sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408). Per gli account nel portale di Visual Studio Team Services, è invece applicabile il documento sulla [protezione dei dati di Visual Studio Team Services](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf).

L'accesso ai dati da parte del personale Microsoft è limitato. Il personale Microsoft può accedere ai dati solo previa autorizzazione dell'utente e se deve fornire supporto per l'uso di Application Insights.

I dati in forma aggregata in tutte le applicazioni dei clienti, ad esempio le frequenze dei dati e le dimensioni medie delle tracce, vengono usati per migliorare Application Insights.

#### È possibile che i dati di telemetria di altri utenti interferiscano con i dati di Application Insights?

È possibile che altri utenti inviino dati di telemetria aggiuntivi all'account dell'utente usando la chiave di strumentazione, disponibile nel codice delle pagine Web. Con una quantità sufficiente di dati aggiuntivi, è possibile che le metriche dell'utente non rappresentino correttamente le prestazioni e l'utilizzo dell'app.

Se si condivide codice con altri progetti, è necessario ricordare di rimuovere la chiave di strumentazione.

## I dati vengono crittografati? 

Non all'interno dei server, attualmente.

Tutti i dati vengono crittografati durante lo spostamento tra data center.

#### I dati vengono crittografati durante il transito dall'applicazione ai server di Application Insights?

Sì, viene usato HTTPS per l'invio dei dati al portale da quasi tutti gli SDK, inclusi i server Web, i dispositivi e le pagine Web HTTPS. L'unica eccezione è costituita dai dati inviati da semplici pagine Web HTTP.

## Informazioni personali

#### È possibile che vengano inviate informazioni personali ad Application Insights? 

Sì, è possibile.

Indicazioni generali:

* La maggior parte dei dati di telemetria standard, ovvero i dati di telemetria inviati senza scrittura di codice da parte dell'utente, non include informazioni personali esplicite. Potrebbe essere tuttavia possibile identificare individui tramite deduzione da raccolte di eventi.
* I messaggi di eccezione e di traccia potrebbero contenere informazioni personali
* I dati di telemetria personalizzati, ovvero chiamate quali TrackEvent scritte nel codice dall'utente mediante API o tracce del log, possono includere qualsiasi dato scelto dall'utente.


La tabella alla fine del documento include descrizioni più dettagliate dei dati raccolti.



#### La responsabilità relativa alla conformità alle leggi e alle normative in ambito di informazioni personali è a carico dell'utente?

Sì. È responsabilità dell'utente assicurare che la raccolta e l'uso dei dati siano conformi alle leggi e alle normative e alle condizioni di Microsoft Online Services.

È consigliabile informare i clienti in modo appropriato in merito ai dati raccolti dall'applicazione e al relativo uso.

#### Gli utenti possono disattivare Application Insights?

Non direttamente. Non viene fornita alcuna opzione che gli utenti possono usare per disattivare Application Insights.

È tuttavia possibile implementare una funzionalità di questo tipo nell'applicazione. Tutti gli SDK includono un'impostazione dell'API che disattiva la raccolta di dati di telemetria.

#### L'applicazione raccoglie involontariamente informazioni riservate. Application Insights può eliminare questi dati in modo che non vengano conservati?

Application Insights non filtra o elimina i dati. È consigliabile gestire i dati in modo appropriato ed evitare di inviare tali dati ad Application Insights.



## Dati inviati da Application Insights

Gli SDK sono diversi per le piattaforme specifiche e sono disponibili vari componenti da installare. Fare riferimento a [Application Insights - Guida introduttiva][start]. Ogni componente invia dati diversi.

#### Classi di dati inviati nei diversi scenari

Azione | Classi di dati raccolte (vedere la tabella seguente)
---|---
[Aggiungere Application Insights SDK a un progetto Web .NET][greenbrown] | ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>users
[Installare Monitoraggio dello stato su IIS][redfield]<br/>[Aggiungere l'estensione AI a una VM di Azure o a un'app Web][azure]|Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters
[Aggiungere Application Insights SDK a un'app Web Java][java]|ServerContext<br/>Inferred<br/>Request<br/>Session<br/>users
[Aggiungere JavaScript SDK a una pagina Web][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf<br/>Ajax
[Aggiungere SDK a un'app di Windows Store][windows]|DeviceContext<br/>Users<br/>Crash data
[Definire le proprietà predefinite][apiproperties]|**Properties** in tutti gli eventi standard e personalizzati
[Chiamare TrackMetric][api]|Valori numerici<br/>**Properties**
[Chiamare Track*][api]|Nome evento<br/>**Properties**
[Chiamare TrackException][api]|**Exceptions**<br/>Dump dello stack<br/>**Properties**
SDK non riesce a raccogliere dati. Ad esempio: <br/> - Non è possibile accedere ai contatori delle prestazioni<br/> - Eccezione nell'inizializzatore di telemetria | Diagnostica di SDK
 

Per informazioni sugli [SDK per altre piattaforme][platforms], vedere i relativi documenti.



#### Classi dei dati raccolti

Classe di dati raccolti | Include (elenco non completo) 
---|---
**Proprietà**|**Qualsiasi dato, in base al codice**
DeviceContext |ID, IP, impostazioni locali, modello dispositivo, rete, tipo di rete, nome OEM, risoluzione dello schermo, istanza del ruolo, nome ruolo, tipo di dispositivo.
ClientContext |Sistema operativo, impostazioni locali, lingua, rete, risoluzione della finestra.
Session | ID sessione
ServerContext |Nome computer, impostazioni locali, sistema operativo, dispositivo, sessione utente, contesto utente, operazione. 
Inferred |Area geografica in base a indirizzo IP, timestamp, sistema operativo, browser.
Metrics | Nome e valore della metrica.
Events | Nome e valore dell'evento.
PageViews | URL e nome della pagina o della schermata.
Client perf | URL/nome pagina, tempo di caricamento del browser.
Ajax | Chiamate HTTP dalla pagina Web al server
Richieste |URL, durata, codice di risposta.
Dependencies|Tipo (SQL, HTTP, ...), stringa di connessione o URI, sincrono/asincrono, durata, esito positivo, istruzione SQL (con Monitoraggio stato)
**Eccezioni** | Tipo, **messaggio**, stack di chiamate, file di origine e numero di riga, ID thread.
Crashes | ID processo, ID processo padre, ID thread di arresto anomalo, patch applicazione, ID, compilazione, tipo di eccezione, indirizzo, motivo, simboli e registri offuscati, indirizzi di inizio e fine binari, nome e percorso binario, tipo di CPU.
Trace | **Messaggio** e livello di gravità.
Perf counters | Tempo processore, memoria disponibile, frequenza di richieste, frequenza di eccezioni, byte privati del processo, velocità di I/O, durata richiesta, lunghezza coda richiesta.
Availability | Codice di risposta del test Web, durata di ogni passo del test, nome del test, timestamp, esito positivo, tempo di risposta, posizione del test
Diagnostica di SDK | Messaggio di traccia o eccezione 

È possibile [disattivare alcuni dei dati modificando ApplicationInsights.config][config]


## Crediti

Questo prodotto include dati GeoLite2 creati da MaxMind, disponibile nel sito [http://www.maxmind.com](http://www.maxmind.com).

## <a name="video"></a>Video

#### Introduzione

> [AZURE.VIDEO application-insights-introduction]

#### Introduzione

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0316_2016-->