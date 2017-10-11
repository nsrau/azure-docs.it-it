---
title: Conservazione e archiviazione dei dati in Azure Application Insights | Microsoft Docs
description: Informativa sulla conservazione e sulla privacy
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: bwren
ms.openlocfilehash: ddb9fa516da66da0484619439848583a29e1f5c1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Raccolta, conservazione e archiviazione di dati in Application Insights


Quando si installa [Azure Application Insights][start] SDK nell'app, vengono inviati i dati di telemetria sull'app nel cloud. Naturalmente, gli sviluppatori responsabili vogliono sapere esattamente quali dati vengono inviati, cosa accade ai dati e come possono mantenerne il controllo. In particolare, devono sapere se possono essere inviati dati sensibili, dove vengono archiviati e quale livello di sicurezza viene applicato. 

Innanzitutto, chiariamo alcuni aspetti:

* È improbabile che i moduli di telemetria standard che seguono un comportamento predefinito possano inviare dati sensibili al servizio. I dati di telemetria riguardano metriche di carico, prestazioni e utilizzo, report di eccezioni e altri dati di diagnostica. I principali dati utente visibili nei report di diagnostica sono URL, ma l'app non deve in ogni caso inserire dati sensibili in testo normale in un URL.
* È possibile scrivere codice che invia dati di telemetria personalizzati aggiuntivi per agevolare la diagnostica e il monitoraggio dell'utilizzo. Questa flessibilità è un'eccellente funzionalità di Application Insights. Sarebbe possibile, per errore, scrivere il codice in modo che includa dati personali e altri dati sensibili. Se quindi l'applicazione usa questo tipo di dati, è consigliabile applicare un processo di revisione completo a tutto il codice creato.
* Durante lo sviluppo e il test dell'app, è facile controllare ciò che viene inviato dall’SDK. I dati vengono visualizzati nelle finestre di output del debug dell’IDE e del browser. 
* I dati vengono archiviati nei server di [Microsoft Azure](http://azure.com) negli Stati Uniti o in Europa. L'app può essere eseguita ovunque. Azure offre [processi di sicurezza efficaci e soddisfa una vasta gamma di standard di conformità](https://azure.microsoft.com/support/trust-center/). Solo lo sviluppatore dell’app e il team designato hanno accesso ai dati. Il personale Microsoft può avere accesso limitato ai dati solo in determinate circostanze con il consenso dello sviluppatore. I dati sono crittografati durante il transito, anche se non lo sono nel server.

Nella parte restante di questo articolo verranno elaborate ulteriormente queste risposte. Questa parte è progettata per essere indipendente dal resto, pertanto è possibile mostrarla ai colleghi che non fanno parte del proprio team.

## <a name="what-is-application-insights"></a>Informazioni su Azure Application Insights
[Azure Application Insights][start] è un servizio Microsoft che consente di migliorare le prestazioni e l'usabilità di un'applicazione live. Esegue il monitoraggio dell'applicazione per tutto il tempo che è in esecuzione, sia durante il test che dopo la pubblicazione o la distribuzione. Application Insights crea grafici e tabelle che illustrano, ad esempio, in quali ore del giorno si ottengono più utenti, i tempi di risposta dell'app e come funzionano i servizi esterni da cui dipende. Se sono presenti arresti anomali del sistema, errori o problemi di prestazioni, è possibile cercare i dati di telemetria in dettaglio per diagnosticare la causa. Inoltre, il servizio invierà messaggi di posta elettronica in caso di modifiche della disponibilità e delle prestazioni dell'app.

Per ottenere questa funzionalità, installare Application Insights SDK nell'applicazione, che diventa parte del codice. Quando l'app è in esecuzione, l’SDK monitora il funzionamento e invia i dati di telemetria al servizio Application Insights. Si tratta di un servizio cloud ospitato da [Microsoft Azure](http://azure.com). Application Insights funziona tuttavia per tutte le applicazioni, non solo quelle ospitate in Azure.

![L’SDK nell'app invia dati di telemetria al servizio Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Il servizio Application Insights archivia e analizza i dati di telemetria. Per visualizzare l'analisi o eseguire una ricerca nei dati di telemetria archiviati, accedere con il proprio account Azure e aprire la risorsa Application Insights per l'applicazione. È anche possibile condividere l'accesso ai dati con altri membri del team o con determinati sottoscrittori di Azure.

È possibile fare in modo che i dati vengano esportati dal servizio Application Insights, ad esempio in un database o in strumenti esterni. A ogni strumento è possibile assegnare una chiave speciale ottenuta dal servizio, che può essere revocata se necessario. 

Sono disponibili Application Insights SDK per una gamma di tipi di applicazioni: servizi Web ospitati nei server J2EE o ASP.NET o in Azure; client Web, ovvero il codice in esecuzione in una pagina Web; app desktop e servizi; app per dispositivi, ad esempio Windows Phone, iOS e Android. Tutti inviano i dati di telemetria allo stesso servizio.

## <a name="what-data-does-it-collect"></a>Quali dati raccoglie?
### <a name="how-is-the-data-is-collected"></a>Come vengono raccolti?
Esistono tre origini dati:

* L'SDK, che viene integrato nell'app [in fase di sviluppo](app-insights-asp-net.md) o [in esecuzione](app-insights-monitor-performance-live-website-now.md). Sono disponibili diversi SDK per diversi tipi di applicazione. È inoltre disponibile un [SDK per le pagine Web](app-insights-javascript.md), che viene caricato nel browser dell'utente finale insieme alla pagina.
  
  * Ogni SDK include un numero di [moduli](app-insights-configuration-with-applicationinsights-config.md), che utilizzano tecniche diverse per raccogliere tipi diversi di dati di telemetria.
  * Se si installa l’SDK in fase di sviluppo, è possibile utilizzare l'API per inviare dati di telemetria personalizzati, oltre ai moduli standard. Tali dati di telemetria personalizzati possono includere qualsiasi tipo di dati si voglia inviare.
* In alcuni server Web sono disponibili anche agenti che vengono eseguiti insieme all'applicazione e inviano dati di telemetria su CPU, memoria e occupazione della rete. Ad esempio, macchine virtuali di Azure, host Docker e [server J2EE](app-insights-java-agent.md) possono disporre di tali agenti.
* [test di disponibilità](app-insights-monitor-web-app-availability.md) sono processi eseguiti da Microsoft che inviano richieste all'app Web a intervalli regolari. I risultati vengono inviati al servizio Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quali tipi di dati vengono raccolti?
Le categorie principali sono:

* [Dati di telemetria del server Web](app-insights-asp-net.md): richieste HTTP,  URI, tempo impiegato per elaborare la richiesta, codice di risposta, indirizzo IP del client, ID sessione.
* [Pagine Web](app-insights-javascript.md): numero di pagine, utenti e sessioni, tempo di caricamento della pagina, eccezioni, chiamate AJAX.
* Contatori delle prestazioni: memoria, CPU, IO, occupazione della rete.
* Contesto client e server: sistema operativo, impostazioni locali, tipo di dispositivo, browser, risoluzione dello schermo.
* [Eccezioni](app-insights-asp-net-exceptions.md) e arresti anomali: **dump dello stack**, ID compilazione, tipo di CPU. 
* [Dipendenze](app-insights-asp-net-dependencies.md): chiamate ai servizi esterni, ad esempio REST, SQL, AJAX; URI o stringa di connessione, durata, esito positivo, comando.
* [Test di disponibilità](app-insights-monitor-web-app-availability.md) : durata del test e passaggi, risposte.
* [Log di traccia](app-insights-asp-net-trace-logs.md) e [telemetria personalizzata](app-insights-api-custom-events-metrics.md) - **tutto ciò che viene codificato nei log o nella telemetria**.

[Maggiori dettagli](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Come è possibile verificare cosa viene raccolto?
Se si sviluppa l'app con Visual Studio, eseguire l'app in modalità di debug (F5). I dati di telemetria vengono visualizzati nella finestra di output. Da qui, è possibile copiarli e formattarli come JSON per semplificare l'ispezione. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

È inoltre disponibile una visualizzazione più leggibile nella finestra di diagnostica.

Per le pagine Web, aprire la finestra di debug del browser.

![Premere F12 e aprire la scheda Rete.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>È possibile scrivere codice per filtrare i dati di telemetria prima che vengano inviati?
Questo sarebbe possibile scrivendo un [plug-in del processore di telemetria](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Per quanto tempo vengono conservati i dati?
I punti dati non elaborati, ovvero gli elementi di dati su cui è possibile eseguire query in Analytics e ispezionabili in Ricerca, vengono conservati per un massimo di 90 giorni. Per conservare i dati più a lungo, è possibile usare l' [esportazione continua](app-insights-export-telemetry.md) per copiarli in un account di archiviazione.

I dati aggregati, ovvero conteggi, medie e altri dati statistici visualizzati in Esplora metriche, vengono conservati con livello di dettaglio di 1 minuto per 90 giorni.

## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
I dati sono visibili all'utente e, se si usa un account aziendale, ai membri del team dell'utente. 

Possono essere esportati dall'utente e dai membri del team e possono essere copiati in altri percorsi e passati ad altri utenti.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>In che modo Microsoft usa le informazioni inviate dall'app ad Application Insights?
Microsoft usa i dati solo al fine di fornire il servizio all'utente.

## <a name="where-is-the-data-held"></a>Dove vengono archiviati i dati?
* Negli USA o in Europa. È possibile selezionare la località quando si crea una nuova risorsa di Application Insights. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-or-europe"></a>Significa che l'app deve essere ospitata negli Stati Uniti o in Europa?
* No. L'applicazione può essere eseguita ovunque, nel proprio host locale o nel Cloud.

## <a name="how-secure-is-my-data"></a>Quanto sono sicuri i dati?
Application Insights è un servizio di Azure. I criteri di sicurezza sono descritti nel [white paper su sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408).

I dati vengono archiviati nei server di Microsoft Azure. Per gli account nel portale di Azure, le restrizioni relative all'account sono illustrate nel [documento relativo a sicurezza, privacy e conformità di Azure](http://go.microsoft.com/fwlink/?linkid=392408).

L'accesso ai dati da parte del personale Microsoft è limitato. Il personale Microsoft può accedere ai dati solo previa autorizzazione dell'utente e se deve fornire supporto per l'uso di Application Insights. 

I dati in forma aggregata in tutte le applicazioni dei clienti, ad esempio le frequenze dei dati e le dimensioni medie delle tracce, vengono usati per migliorare Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>È possibile che i dati di telemetria di altri utenti interferiscano con i dati di Application Insights?
È possibile che altri utenti inviino dati di telemetria aggiuntivi all'account dell'utente usando la chiave di strumentazione, disponibile nel codice delle pagine Web. Con una quantità sufficiente di dati aggiuntivi, è possibile che le metriche dell'utente non rappresentino correttamente le prestazioni e l'utilizzo dell'app.

Se si condivide codice con altri progetti, è necessario ricordare di rimuovere la chiave di strumentazione.

## <a name="is-the-data-encrypted"></a>I dati vengono crittografati?
Non all'interno dei server, attualmente.

Tutti i dati vengono crittografati durante lo spostamento tra data center.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>I dati vengono crittografati durante il transito dall'applicazione ai server di Application Insights?
Sì, viene usato HTTPS per l'invio dei dati al portale da quasi tutti gli SDK, inclusi i server Web, i dispositivi e le pagine Web HTTPS. L'unica eccezione è costituita dai dati inviati da semplici pagine Web HTTP. 

## <a name="personally-identifiable-information"></a>Informazioni personali
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>È possibile che vengano inviate informazioni personali ad Application Insights?
Sì, è possibile. 

Indicazioni generali:

* La maggior parte dei dati di telemetria standard, ovvero i dati di telemetria inviati senza scrittura di codice da parte dell'utente, non include informazioni personali esplicite. Potrebbe essere tuttavia possibile identificare individui tramite deduzione da raccolte di eventi.
* I messaggi di eccezione e di traccia potrebbero contenere informazioni personali
* I dati di telemetria personalizzati, ovvero chiamate quali TrackEvent scritte nel codice dall'utente mediante API o tracce del log, possono includere qualsiasi dato scelto dall'utente.

La tabella alla fine del documento include descrizioni più dettagliate dei dati raccolti.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>La responsabilità relativa alla conformità alle leggi e alle normative in ambito di informazioni personali è a carico dell'utente?
Sì. È responsabilità dell'utente assicurare che la raccolta e l'uso dei dati siano conformi alle leggi e alle normative e alle condizioni di Microsoft Online Services.

È consigliabile informare i clienti in modo appropriato in merito ai dati raccolti dall'applicazione e al relativo uso.

#### <a name="can-my-users-turn-off-application-insights"></a>Gli utenti possono disattivare Application Insights?
Non direttamente. Non viene fornita alcuna opzione che gli utenti possono usare per disattivare Application Insights.

È tuttavia possibile implementare una funzionalità di questo tipo nell'applicazione. Tutti gli SDK includono un'impostazione dell'API che disattiva la raccolta di dati di telemetria. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>L'applicazione raccoglie involontariamente informazioni riservate. Application Insights può eliminare questi dati in modo che non vengano conservati?
Application Insights non filtra o elimina i dati. È consigliabile gestire i dati in modo appropriato ed evitare di inviare tali dati ad Application Insights.

## <a name="data-sent-by-application-insights"></a>Dati inviati da Application Insights
Gli SDK sono diversi per le piattaforme specifiche e sono disponibili vari componenti da installare. Vedere [Application Insights: panoramica][start]. Ogni componente invia dati diversi.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classi di dati inviati nei diversi scenari
| Azione | Classi di dati raccolte (vedere la tabella seguente) |
| --- | --- |
| [Aggiungere Application Insights SDK a un progetto Web .NET][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Eccezioni**<br/>Session<br/>users |
| [Installare Status Monitor in IIS][redfield] |Dipendenze<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Aggiungere Application Insights SDK a un'app Web Java][java] |ServerContext<br/>Inferred<br/>Richiesta<br/>Sessione<br/>users |
| [Aggiungere JavaScript SDK a una pagina Web][client] |ClientContext  <br/>Inferred<br/>Page<br/>ClientPerf<br/>Ajax |
| [Definire le proprietà predefinite][apiproperties] |**Properties** in tutti gli eventi standard e personalizzati |
| [Chiamare TrackMetric][api] |Valori numerici<br/>**Proprietà** |
| [Chiamare Track*][api] |Nome evento<br/>**Proprietà** |
| [Chiamare TrackException][api] |**Eccezioni**<br/>Dump dello stack<br/>**Proprietà** |
| SDK non riesce a raccogliere dati. Ad esempio: <br/> - non riesce ad accedere ai contatori delle prestazioni<br/> - si è verificata un'eccezione nell'inizializzatore della telemetria |Diagnostica di SDK |

Per informazioni sugli [SDK per altre piattaforme][platforms], vedere i relativi documenti.

#### <a name="the-classes-of-collected-data"></a>Classi dei dati raccolti
| Classe di dati raccolti | Include (elenco non completo) |
| --- | --- |
| **Properties** |**Qualsiasi dato, in base al codice** |
| DeviceContext |ID, IP, impostazioni locali, modello dispositivo, rete, tipo di rete, nome OEM, risoluzione dello schermo, istanza del ruolo, nome ruolo, tipo di dispositivo. |
| ClientContext  |Sistema operativo, impostazioni locali, lingua, rete, risoluzione della finestra. |
| Session |ID sessione |
| ServerContext |Nome computer, impostazioni locali, sistema operativo, dispositivo, sessione utente, contesto utente, operazione. |
| Inferred |Area geografica in base a indirizzo IP, timestamp, sistema operativo, browser. |
| Metrica |Nome e valore della metrica. |
| Eventi |Nome e valore dell'evento. |
| PageViews |URL e nome della pagina o della schermata. |
| Client perf |URL/nome pagina, tempo di caricamento del browser. |
| Ajax |Chiamate HTTP dalla pagina Web al server |
| Requests |URL, durata, codice di risposta. |
| Dependencies |Tipo (SQL, HTTP, ...), stringa di connessione o URI, sincrono/asincrono, durata, esito positivo, istruzione SQL (con Monitoraggio stato) |
| **Eccezioni** |Tipo, **messaggio**, stack di chiamate, file di origine e numero di riga, ID thread. |
| Crashes |ID processo, ID processo padre, ID thread di arresto anomalo, patch applicazione, ID, compilazione, tipo di eccezione, indirizzo, motivo, simboli e registri offuscati, indirizzi di inizio e fine binari, nome e percorso binario, tipo di CPU |
| Trace |**Messaggio** e livello di gravità. |
| Perf counters |Tempo processore, memoria disponibile, frequenza di richieste, frequenza di eccezioni, byte privati del processo, velocità di I/O, durata richiesta, lunghezza coda richiesta. |
| Disponibilità |Codice di risposta del test Web, durata di ogni passo del test, nome del test, timestamp, esito positivo, tempo di risposta, posizione del test |
| Diagnostica di SDK |Messaggio di traccia o eccezione |

È possibile [disattivare alcuni dei dati modificando ApplicationInsights.config][config]

## <a name="credits"></a>Credits
Questo prodotto include dati GeoLite2 creati da MaxMind, disponibile nel sito [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

