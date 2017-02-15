---
title: Risoluzione dei problemi e domande su Application Insights
description: "Questo articolo contiene le risposte ai dubbi più comuni su Azure Application Insights. Risposte ai problemi più comuni."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 0d860856b4c871c2c8e3622597ba3753bf62cf4f


---
# <a name="questions---application-insights-for-aspnet"></a>Domande su Application Insights per ASP.NET
## <a name="configuration-problems"></a>Problemi di configurazione
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

## <a name="can-i-use-application-insights-with-"></a>Si può usare Application Insights con ...?
[Vedere le piattaforme][platforms]

## <a name="is-it-free"></a>È gratuito?
* Sì, se si sceglie il [piano tariffario](app-insights-pricing.md)gratuito. In tal modo, è possibile ottenere la maggior parte delle funzionalità e una quota dati soddisfacente.
* Per registrarsi in Microsoft Azure è necessario fornire i dati della propria carta di credito, ma verranno addebitati costi solo se si usa un altro servizio di Azure a pagamento o si effettua esplicitamente l'aggiornamento a un livello non gratuito.
* Se la propria app invia una quantità di dati superiore a quella consentita dalla quota mensile, la registrazione si interrompe. In tal caso, è possibile scegliere di pagare o attendere che la quota venga reimpostata alla fine del mese.
* I dati relativi alla sessione e all'utilizzo di base non sono soggetti a una quota.
* È anche previsto un periodo di valutazione gratuita di 30 giorni durante il quale è possibile usufruire delle funzionalità a pagamento senza alcun addebito.
* A ogni risorsa dell'applicazione è associata una quota separata ed è possibile impostare il relativo piano tariffario in modo indipendente rispetto alle altre.

#### <a name="what-do-i-get-if-i-pay"></a>Quali vantaggi si ottengono a pagamento?
* [Quota dati mensile](https://azure.microsoft.com/pricing/details/application-insights/)più elevata.
* Possibilità di pagare l'"eccedenza" per continuare a raccogliere dati se viene superata la quota mensile. Quando i dati superano la quota prevista, l'addebito dei costi avviene per MB.
* [Esportazione continua](app-insights-export-telemetry.md).

## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Quali modifiche apporta Application Insights al progetto?
Dipende dal tipo di progetto. Per un'applicazione Web:

* Aggiunge tre file al progetto:
  
  * ApplicationInsights.config.
  * ai.js
* Installa i pacchetti NuGet seguenti:
  
  * *Application Insights API* , ovvero l'API principale
  * *Application Insights API for Web Applications* , che consente di inviare i dati di telemetria dal server
  * *Application Insights API for JavaScript Applications* , che consente di inviare i dati di telemetria dal client
    
    I pacchetti includono gli assembly seguenti:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Inserire elementi in:
  
  * Web.config
  * packages.config
* Solo per i nuovi progetti: se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente. Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito nella pagina master Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?
Vedere le [note sulla versione](app-insights-release-notes.md) dell'SDK appropriato per il tipo di applicazione.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Vedere [Conservazione e privacy dei dati][dati].

## <a name="logging"></a>Registrazione
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

## <a name="security"></a>Sicurezza
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>I dati personali sono al sicuro nel portale? Per quanto tempo vengono conservati?
Vedere [Conservazione e privacy dei dati][dati].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?
| Elementi che dovrebbero essere visualizzati | Come ottenerli | Perché si vuole ottenerli |
| --- | --- | --- |
| Grafici di disponibilità |[Test Web](app-insights-monitor-web-app-availability.md) |Stabilire se l'app Web è attiva |
| Prestazioni dell'app server: tempi di risposta, ... |[Aggiungere Application Insights al progetto](app-insights-asp-net.md) o [installare Status Monitor di Application Insights nel server](app-insights-monitor-performance-live-website-now.md) (o scrivere il codice per [tenere traccia delle dipendenze](app-insights-api-custom-events-metrics.md#track-dependency)) |Rilevare i problemi di prestazioni |
| Telemetria di dipendenza |[Installare Status Monitor di Application Insights nel server](app-insights-monitor-performance-live-website-now.md) |Diagnosticare i problemi relativi a database o altri componenti esterni |
| Ricavare analisi dello stack dalle eccezioni |[Inserire chiamate TrackException nel codice](app-insights-search-diagnostic-logs.md#exceptions) (ma alcune sono segnalate automaticamente) |Rilevare e diagnosticare le eccezioni |
| Eseguire la ricerca di tracce dei log |[Aggiungere un adattatore di registrazione](app-insights-search-diagnostic-logs.md) |Diagnosticare le eccezioni, problemi di prestazioni |
| Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ... |[Inizializzatore JavaScript nelle pagine Web](app-insights-javascript.md) |Analisi dell'utilizzo |
| Metriche personalizzate client |[Rilevamento delle chiamate nelle pagine Web](app-insights-api-custom-events-metrics.md) |Migliorare l'esperienza utente |
| Metriche personalizzate server |[Rilevamento delle chiamate nel server](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="automation"></a>Automazione
È possibile [scrivere script di PowerShell](app-insights-powershell.md) per creare e aggiornare le risorse di Application Insights.

## <a name="more-answers"></a>Altre risposte
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[dati]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Nov16_HO3-->


