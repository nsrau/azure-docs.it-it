---
title: Risoluzione dei problemi e domande su Application Insights
description: Questo articolo contiene le risposte ai dubbi più comuni su Application Insights di Visual Studio ai dubbi più comuni.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills

---
# Domande su Application Insights per ASP.NET
## Problemi di configurazione
*Problemi nella configurazione di:*

* [App .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'applicazione già in esecuzione](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnostica di Azure](app-insights-azure-diagnostics.md)
* [App Web Java](app-insights-java-troubleshoot.md)
* [Altre piattaforme](app-insights-platforms.md)

*Non sono disponibili dati dal server*

* [Impostare le eccezioni del firewall](app-insights-ip-addresses.md)
* [Configurare un server ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurare un server Java](app-insights-java-agent.md)

## Si può usare Application Insights con ...?
[Vedere le piattaforme.][platforms]

## È gratuito?
* Sì, se si sceglie il [piano tariffario](app-insights-pricing.md) gratuito. In tal modo, è possibile ottenere la maggior parte delle funzionalità e una quota dati soddisfacente.
* Per registrarsi in Microsoft Azure è necessario fornire i dati della propria carta di credito, ma verranno addebitati costi solo se si usa un altro servizio di Azure a pagamento o si effettua esplicitamente l'aggiornamento a un livello non gratuito.
* Se la propria app invia una quantità di dati superiore a quella consentita dalla quota mensile, la registrazione si interrompe. In tal caso, è possibile scegliere di pagare o attendere che la quota venga reimpostata alla fine del mese.
* I dati relativi alla sessione e all'utilizzo di base non sono soggetti a una quota.
* È anche previsto un periodo di valutazione gratuita di 30 giorni durante il quale è possibile usufruire delle funzionalità a pagamento senza alcun addebito.
* A ogni risorsa dell'applicazione è associata una quota separata ed è possibile impostare il relativo piano tariffario in modo indipendente rispetto alle altre.

#### Quali vantaggi si ottengono a pagamento?
* [Quota dati mensile](https://azure.microsoft.com/pricing/details/application-insights/) più elevata.
* Possibilità di pagare l'"eccedenza" per continuare a raccogliere dati se viene superata la quota mensile. Quando i dati superano la quota prevista, l'addebito dei costi avviene per MB.
* [Esportazione continua](app-insights-export-telemetry.md).

## <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?
Dipende dal tipo di progetto. Per un'applicazione Web:

* Aggiunge tre file al progetto:
  
  * ApplicationInsights.config.
  * ai.js
* Installa i pacchetti NuGet seguenti:
  
  * *Application Insights API*, ovvero l'API principale
  * *Application Insights API for Web Applications*, che consente di inviare i dati di telemetria dal server
  * *Application Insights API for JavaScript Applications*, che consente di inviare i dati di telemetria dal client
    
    I pacchetti includono gli assembly seguenti:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Inserire elementi in:
  
  * Web.config
  * packages.config
* (Solo per i nuovi progetti, se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente.) Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito nella pagina master Views/Shared/\_Layout.cshtml

## In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?
Vedere le [note sulla versione](app-insights-release-notes.md) dell'SDK appropriato per il tipo di applicazione.

## <a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

#### <a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Dare un'occhiata all'argomento relativo a [conservazione dei dati e privacy][data].

## Registrazione
#### <a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

## Sicurezza
#### I dati personali sono al sicuro nel portale? Per quanto tempo vengono conservati?
Vedere l'argomento relativo a [conservazione dei dati e privacy][data].

## <a name="q17"></a>In Application Insights sono state abilitate tutte le funzionalità?
<table border="1">
<tr><th>Elementi che dovrebbero essere visualizzati</th><th>Come ottenerli</th><th>Perché si vuole ottenerli</th></tr>
<tr><td>Grafici di disponibilità</td><td><a href="../app-insights-monitor-web-app-availability/">Test Web</a></td><td>Stabilire se l'app Web è attiva</td></tr>
<tr><td>Prestazioni dell'app server: tempi di risposta, ...
</td><td><a href="../app-insights-asp-net/">Aggiungere Application Insights al progetto</a><br/>oppure <br/><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights nel server</a> (o scrivere il proprio codice per <a href="../app-insights-api-custom-events-metrics/#track-dependency">il tracciamento delle dipendenze</a>)</td><td>Rilevare i problemi di prestazioni</td></tr>
<tr><td>Telemetria di dipendenza</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights nel server</a></td><td>Diagnosticare i problemi relativi a database o altri componenti esterni</td></tr>
<tr><td>Ricavare analisi dello stack dalle eccezioni</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserire chiamate TrackException nel codice</a> (ma alcune sono segnalate automaticamente)</td><td>Rilevare e diagnosticare le eccezioni</td></tr>
<tr><td>Eseguire la ricerca di tracce dei log</td><td><a href="../app-insights-search-diagnostic-logs/">Aggiungere un adattatore di registrazione</a></td><td>Diagnosticare le eccezioni, problemi di prestazioni</td></tr>
<tr><td>Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ...</td><td><a href="../app-insights-javascript/">Inizializzatore JavaScript nelle pagine Web</a></td><td>Analisi dell'utilizzo</td></tr>
<tr><td>Metriche personalizzate client</td><td><a href="../app-insights-api-custom-events-metrics/">Tenere traccia delle chiamate nelle pagine Web</a></td><td>Migliorare l'esperienza utente</td></tr>
<tr><td>Metriche personalizzate server</td><td><a href="../app-insights-api-custom-events-metrics/">Tenere traccia delle chiamate nel codice del server</a></td><td>Business intelligence</td></tr>
</table>


## Automazione
È possibile [scrivere script di PowerShell](app-insights-powershell.md) per creare e aggiornare le risorse di Application Insights.

## Altre risposte
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/it-IT/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!---HONumber=AcomDC_0824_2016-->