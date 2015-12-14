<properties 
	pageTitle="Mantenimento e archiviazione dei dati in Application Insights" 
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
	ms.date="11/18/2015" 
	ms.author="awills"/>

# Raccolta, conservazione e archiviazione di dati in Application Insights 

*Application Insights è disponibile in anteprima.*

## Panoramica

Questo articolo risponde a domande sui dati raccolti da [Visual Studio Application Insights][start] e sul modo in cui vengono elaborati e archiviati.

Application Insights è un servizio di Azure disponibile in anteprima. Durante la fase di anteprima Microsoft si impegna a proteggere i dati degli utenti in base ai criteri descritti nel [white paper su sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408).


## Raccolta

#### In che modo vengono raccolti i dati day Application Insights?

Application Insights SDK e gli agenti integrati con l'applicazione inviano dati al servizio Application Insights. I dati vengono elaborati dal servizio per fornire report, avvisi e altre funzionalità. L'elaborazione può includere dati che l'utente sceglie di acquisire usando l'API, ad esempio nelle proprietà e negli eventi personalizzati.

#### Quanti dati è possibile acquisire? 

**Al secondo**: fino a 500 punti dati al secondo per chiave di strumentazione (ovvero, per ogni applicazione). Per il [piano tariffario][pricing] gratuito, il limite è 100 dp/s.

Esistono tre bucket che vengono conteggiati separatamente:

* [TrackTrace chiamate](app-insights-api-custom-events-metrics.md#track-trace) e [acquisiti log](app-insights-asp-net-trace-logs.md)
* [Eccezioni](app-insights-api-custom-events-metrics.md#track-exception), soggette a un limite inferiore pari a 50/s.
* Tutti gli altri dati di telemetria (visualizzazioni di pagina, richieste, dipendenze, metriche, eventi personalizzati e risultati dei test Web).

**Al mese**: tra 5 e 15 milioni di punti dati ogni mese, a seconda del [piano tariffario](http://azure.microsoft.com/pricing/details/application-insights/). Fatta eccezione per il [piano tariffario][pricing] gratuito, è possibile acquistare capacità aggiuntiva se si raggiunge il limite.


Un *punto dati* è un elemento di telemetria relativo all'app inviato al portale di Azure. Può essere inviato da:

* [Moduli di SDK](app-insights-configuration-with-applicationinsights-config.md) che raccolgono automaticamente i dati, ad esempio per segnalare una richiesta o un arresto anomalo oppure per misurare le prestazioni.
* Chiamate [API](app-insights-api-custom-events-metrics.md) `Track...` scritte dall'utente, ad esempio `TrackEvent` o `trackPageView`.
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md) configurati dall'utente.

I dati di telemetria includono:

* Ogni riga visibile nella [ricerca diagnostica](app-insights-diagnostic-search.md)
* Dati non elaborati da cui vengono aggregati i grafici in [Esplora metriche](app-insights-metrics-explorer.md). I dati di metrica, ad esempio i dati del contatore delle prestazioni, non vengono in genere visualizzati come punti dati singoli in Esplora metriche.
* Ogni risultato di test Web in un report di [disponibilità](app-insights-monitor-web-app-availability.md).

I conteggi relativi a utenti e sessioni non sono inclusi nella quota per motivi che riguardano i prezzi.

*Cosa accade se l'app supera la velocità al secondo.*

* Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. Alcune versioni dell’SDK tentano quindi di inviarle di nuovo, distribuendo un picco per diversi minuti; altre, ad esempio l’SDK JavaScript, semplicemente eliminano i dati rifiutati.

*Come è possibile sapere quanti punti dati vengono inviati dall'app?*

* Aprire Impostazioni/Quota e Prezzi per vedere il grafico Volume dati.
* Altrimenti, in Esplora metriche, aggiungere un nuovo grafico e selezionare **Volume punti dati** come metrica. Attivare il raggruppamento in base a **Tipo di dati**.

*Come è possibile ridurre la quantità di dati inviati dall'app personale?*

* Utilizzare [Campionamento](app-insights-sampling.md). Questa tecnologia riduce la frequenza dei dati senza deviare le metriche e senza compromettere la possibilità di spostarsi tra elementi correlati nella Ricerca. Da ASP.NET SDK 2.0.0-beta3, il campionamento adattivo è abilitato per impostazione predefinita.
* [Disattivare gli agenti di raccolta dei dati di telemetria](app-insights-configuration-with-applicationinsights-config.md) che non sono necessari.


#### Per quanto tempo vengono conservati i dati? 

Dipende dal [piano tariffario](http://azure.microsoft.com/pricing/details/application-insights/).

Punti dati non elaborati (cioè elementi di dati ispezionabili nella ricerca diagnostica): tra 7 e 30 giorni.

I dati aggregati, ovvero conteggi, medie e altri dati statistici visualizzati in Esplora metriche, vengono conservati con livello di dettaglio di un minuto per 30 giorni e con livello di dettaglio di un'ora o un giorno, a seconda del tipo, per almeno 13 mesi.

#### Quali limiti sono previsti per i diversi tipi di dati?

1.	Al massimo 200 nomi di metrica univoci e 200 nomi di proprietà univoci per l'applicazione. Le metriche includono l'invio di dati tramite TrackMetric, nonché le misurazioni di altri tipi di dati, ad esempio gli eventi. Le [metriche e nomi di proprietà][api] sono globali per una chiave di strumentazione, non definiti nell'ambito del tipo di dati.
2.	Le [proprietà][apiproperties] possono essere usate per le operazioni di filtro e di raggruppamento solo quando possiedono meno di 100 valori univoci per ogni proprietà. Superati i 100 valori univoci, la proprietà può essere ancora usata per la ricerca e il filtro ma non per i filtri.
3.	Proprietà standard, ad esempio Nome richiesta e URL pagina sono limitate a 1000 valori univoci alla settimana. Superati i 1000 valori univoci, i valori aggiuntivi vengono contrassegnati come "Altri valori". Il valore originale può essere ancora usato per la ricerca full-text e il filtro.


## Access

#### Chi può visualizzare i dati?

I dati sono visibili all'utente e, se si usa un account aziendale, ai membri del team dell'utente.

Possono essere esportati dall'utente e dai membri del team e possono essere copiati in altri percorsi e passati ad altri utenti.

#### In che modo Microsoft usa le informazioni inviate dall'app ad Application Insights?

Microsoft usa i dati solo al fine di fornire il servizio all'utente.


## Località

#### Dove vengono archiviati i dati? 

* Negli Stati Uniti. 

#### È possibile archiviarli altrove, ad esempio in Europa? 

* Per il momento no. 

## Sicurezza 

#### Quanto sono sicuri i dati? 

I dati vengono archiviati nei server di Microsoft Azure. Per gli account nel portale di Azure, le restrizioni relative all'account sono illustrate nel [Documento relativo a sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408). Per gli account nel portale di Visual Studio Team Services, è invece applicabile il documento sulla [protezione dei dati di Visual Studio Team Services](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf).

L'accesso ai dati da parte del personale Microsoft è limitato. Il personale Microsoft può accedere ai dati solo previa autorizzazione dell'utente e se deve fornire supporto per l'uso di Application Insights.

I dati in forma aggregata in tutte le applicazioni dei clienti, ad esempio le frequenze dei dati e le dimensioni medie delle tracce, vengono usati per migliorare Application Insights.

#### È possibile che la telemetria di altri utenti interferisca con i dati di Application Insights?

È possibile che altri utenti inviino telemetria aggiuntiva all'account dell'utente usando la chiave di strumentazione, disponibile nel codice delle pagine Web. Con una quantità sufficiente di dati aggiuntivi, è possibile che le metriche dell'utente non rappresentino correttamente le prestazioni e l'utilizzo dell'app.

Se si condivide codice con altri progetti, è necessario ricordare di rimuovere la chiave di strumentazione.

## Crittografia

#### I dati nei server di Application Insights vengono crittografati? 

Non all'interno dei server, attualmente.

Tutti i dati vengono crittografati durante lo spostamento tra data center.

#### I dati vengono crittografati durante il transito dall'applicazione ai server di Application Insights?

Sì, viene usato HTTPS per l'invio dei dati al portale da quasi tutti gli SDK, inclusi i server Web, i dispositivi e le pagine Web HTTPS. L'unica eccezione è costituita dai dati inviati da semplici pagine Web HTTP.

## Informazioni personali

#### È possibile che vengano inviate informazioni personali ad Application Insights? 

Sì.

Indicazioni generali:

* La maggior parte dei dati di telemetria standard, ovvero i dati di telemetria inviati senza scrittura di codice da parte dell'utente, non include informazioni personali esplicite. Potrebbe essere tuttavia possibile identificare individui tramite deduzione da raccolte di eventi.
* I messaggi di eccezione e di traccia potrebbero contenere informazioni personali
* I dati di telemetria personalizzati, ovvero chiamate quali TrackEvent scritte nel codice dall'utente mediante API o tracce del log, possono includere qualsiasi dato scelto dall'utente.


La tabella alla fine del documento include descrizioni più dettagliate dei dati raccolti.



#### La responsabilità relativa alla conformità dalle leggi e alle normative in ambito di informazioni personali è a carico dell'utente?

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
[Aggiungere JavaScript SDK a una pagina Web][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf
[Aggiungere SDK a un'app di Windows Store][windows]|DeviceContext<br/>Users<br/>Crash data
[Definire le proprietà predefinite][apiproperties]|**Properties** in tutti gli eventi standard e personalizzati
[Chiamare TrackMetric][api]|Valori numerici<br/>**Properties**
[Chiamare Track*][api]|Nome evento<br/>**Properties**
[Chiamare TrackException][api]|**Exceptions**<br/>Dump dello stack<br/>**Properties**
SDK non riesce a raccogliere dati. Ad esempio: <br/> - Non è possibile accedere ai contatori delle prestazioni<br/> - Eccezione nell'inizializzatore di telemetria | Diagnostica di SDK
 

Per informazioni sugli [SDK per altre piattaforme][platforms], vedere i relativi documenti.



#### Classi dei dati raccolti

Classe di dati raccolta | Include (elenco non completo) 
---|---
**Proprietà**|**Qualsiasi dato, in base al codice**
DeviceContext |ID, IP, Impostazioni locali, modello dispositivo, rete, tipo di rete, nome OEM, risoluzione dello schermo, istanza del ruolo, nome ruolo, tipo di dispositivo.
ClientContext |Sistema operativo, Impostazioni locali, lingua, rete, risoluzione della finestra.
Sessione | ID sessione
ServerContext |Nome computer, Impostazioni locali, sistema operativo, dispositivo, sessione utente, contesto utente, operazione. 
Inferred |Area geografica in base a indirizzo IP, timestamp, sistema operativo, browser.
Metrics | Nome e valore della metrica.
Events | Nome e valore dell'evento.
PageViews | URL e nome della pagina o della schermata.
Client perf | URL/nome pagina, tempo di caricamento del browser.
Richieste |URL, durata, codice di risposta.
Dipendenze|Tipo (SQL, HTTP, ...), stringa di connessione o URI, sincrono/asincrono, durata, esito positivo, istruzione SQL (con Monitoraggio stato)
**Eccezioni** | Tipo, **messaggio**, stack di chiamate, file di origine e numero di riga, ID thread.
Crashes | ID processo, ID processo padre, ID thread di arresto anomalo, patch applicazione, ID, compilazione, tipo di eccezione, indirizzo, motivo, simboli e registri offuscati, indirizzi di inizio e fine binari, nome e percorso binario, tipo di CPU.
Trace | **Messaggio** e livello di gravità.
Perf counters | Tempo processore, memoria disponibile, frequenza di richieste, frequenza di eccezioni, byte privati del processo, velocità di I/O, durata richiesta, lunghezza coda richiesta.
Disponibilità | Codice di risposta del test Web, durata di ogni passo del test, nome del test, timestamp, esito positivo, tempo di risposta, posizione del test
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

 

<!---HONumber=AcomDC_1203_2015-->