<properties title="Data retention and storage in Application Insights" pageTitle="Mantenimento e archiviazione dei dati in Application Insights" description="Informativa sulla privacy e sui criteri di mantenimento dei dati" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-14" ms.author="awills" />

# Mantenimento e archiviazione dei dati in Application Insights 

*Application Insights è disponibile in anteprima.*


Questo articolo risponde alle domande sull'archiviazione dei dati di telemetria inviati da un'applicazione ad Application Insights.

Application Insights archivia i dati nei server di Microsoft Azure presenti negli Stati Uniti. Per informazioni dettagliate sui criteri adottati per Microsoft Azure, scaricare il [white paper su sicurezza, privacy e conformità](http://go.microsoft.com/fwlink/?LinkId=392408). Di seguito sono riportate le risposte ad alcune domande specifiche su Application Insights. 

#### Quanti dati vengono archiviati? 

Durante questa fase di anteprima gratuita di Application Insights, per ogni account vengono archiviati: 

* Un massimo di 500 messaggi di telemetria al secondo (o 30.000 al minuto). 

* Un massimo di 10 milioni di eventi o visualizzazioni di pagina al mese. 

*Che cosa accade se si superano questi limiti?* 

* Se si superano i limiti brevi, alcuni messaggi vengono rimossi dopo essere stati contati. Tali messaggi non saranno quindi visibili nell'area di ricerca diagnostica, ma il conteggio degli eventi sarà corretto. I valori medi delle metriche sono basati sugli eventi mantenuti e dovrebbero quindi essere validi. 

* Se si supera il limite mensile, gli eventi non vengono più registrati e quindi le metriche mostrano valori zero dopo una certa data. 

####Per quanto tempo vengono mantenuti i dati di telemetria? 

* Per 7 giorni nel caso di dati di singole istanze visibili nell'area di ricerca diagnostica, ovvero visualizzazioni di pagina, eventi, messaggi di log, analisi ed eccezioni. 

* Per 13 mesi nel caso di dati aggregati visibili nell'area di esplorazione delle metriche. Le statistiche su metriche, eventi ed eccezioni (conteggi di tipi, funzioni non riuscite e così via) vengono mantenute con un livello di dettaglio di 1 minuto (al massimo) per 30 giorni e di 1 ora per 13 mesi. 

####Dove vengono archiviati i dati? 

* Negli Stati Uniti. 

Esiste la possibilità di archiviarli in Europa o in qualche altra area geografica? 

* Per il momento no. 

####Quanto sono sicuri i dati di telemetria? 

I dati vengono archiviati nei server di Microsoft Azure e, per gli account nel portale di anteprima di Azure, le restrizioni sono descritte nel white paper su sicurezza, privacy e conformità di Azure. Per gli account nel portale di Visual Studio Online, è invece applicabile il documento sulla protezione dei dati di Visual Studio Online. 

L'accesso ai dati da parte del personale Microsoft è limitato. Il personale Microsoft può accedere ai dati solo previa autorizzazione dell'utente e se deve fornire supporto per l'uso di Application Insights. 

####I dati nei server di Application Insights vengono crittografati? 

Attualmente no. 

####È possibile che vengano inviate informazioni personali ad Application Insights? 

Sì. È possibile che vengano inviate sia informazioni personali dal codice nella telemetria personalizzata sia informazioni personali incluse nella telemetria standard. Ad Application Insights si applica l'informativa sulla privacy di Azure. 

I dati possono essere inviati al portale in diversi modi e possono quindi essere visualizzati nell'area di ricerca diagnostica. I membri di un'organizzazione potrebbero anche esportare e scaricare i dati. 

* Se si installa lo strumento di monitoraggio dello stato o si aggiunge Application Insights al progetto, quando si verificano eccezioni o avvisi sulle prestazioni vengono acquisite analisi dello stack, che possono includere dati di parametri effettivi, ad esempio dati SQL. 

* Se si inseriscono nel codice chiamate di telemetria, ad esempio TrackEvent, o si acquisiscono messaggi del framework di registrazione, le proprietà potrebbero includere informazioni personali. Si applicano anche le condizioni di Microsoft Online Services. Per Application Insights in particolare, è necessario rispettare le leggi e i regolamenti applicabili alla privacy e alla raccolta dei dati. Prima di raccogliere le informazioni è opportuno determinare se è necessario inviare una notifica o una richiesta di consenso. 


####Quali dati di telemetria vengono inviati quando si usa Application Insights? 

È possibile installare diversi componenti. Fare riferimento a [Application Insights - Guida introduttiva][start]. 

<table>
<tr><th>Che cosa configurare</th><th>Dati di telemetria inviati al portale</th><th>Possono contenere dati sensibili?</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Aggiungere Application Insights al progetto Web</a></td>
  <td>Metriche: numero di richieste del server, tempo di risposta del server</td>
  <td>No</td></tr>
<tr><td></td>
  <td>Report eccezioni dal server</td><td>I dump dello stack possono contenere valori di parametri</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Tenere traccia di eventi e metriche personalizzati</a></td>
  <td>Eventi con proprietà inclusi dal codice</td>
  <td>Sì, se il codice invia informazioni personali in proprietà o titoli</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">Registrare e tracciare i dati di telemetria</a></td><td>Messaggi di log e traccia</td><td>I messaggi di log possono contenere informazioni personali</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Inserire lo script AI nelle pagine Web</a></td>
  <td>ID account e utente resi anonimi</td><td>No</td></tr>
<tr><td></td><td>Fine e inizio di sessioni utente anonime</td><td>No</td></tr>
<tr><td></td><td>URI della pagina, tempi di caricamento, durata di sessione</td><td>No</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">Installare lo strumento di monitoraggio dello stato nel server</a></td>
  <td>Chiamate di dipendenza e durata</td>
  <td>I dati delle chiamate possono includere parametri, ad esempio campi SQL</td></tr>
<tr><td></td><td>CPU, memoria, rete e altri contatori di risorse</td><td>No</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Test Web</a></td><td>Disponibilità e tempi di risposta visualizzati dal Web</td><td>No</td></tr>
</table>

## <a name="video"></a>Video

#### Introduzione

> [AZURE.VIDEO application-insights-introduction]

#### Guida introduttiva

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
