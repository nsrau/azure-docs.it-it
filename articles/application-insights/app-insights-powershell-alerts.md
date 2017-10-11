---
title: Usare PowerShell per impostare gli avvisi in Application Insights | Microsoft Docs
description: Automatizzare la configurazione di Application Insights per ricevere messaggi di posta elettronica sulle modifiche delle metriche.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: bwren
ms.openlocfilehash: 64675c51abf80daa3a55220f910aa8fdee1042ca
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Usare PowerShell per impostare gli avvisi in Application Insights
È possibile automatizzare la configurazione degli [avvisi](app-insights-alerts.md) in [Application Insights](app-insights-overview.md).

È inoltre possibile [impostare webhook per automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Se si vuole creare risorse e avvisi allo stesso tempo, considerare l'[uso di un modello di Azure Resource Manager](app-insights-powershell.md).
>
>

## <a name="one-time-setup"></a>Installazione singola
Se non si è mai usato PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure Powershell nel computer in cui verranno eseguiti gli script.

* Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
* Usarla per installare Microsoft Azure PowerShell.

## <a name="connect-to-azure"></a>Connettersi ad Azure
Avviare Azure PowerShell e [connettersi alla sottoscrizione](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Ottenere gli avvisi
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Aggiungere un avviso
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Esempio 1
Invia un messaggio di posta elettronica se la risposta del server alle richieste HTTP, calcolate su una media di 5 minuti, richiede più di 1 secondo. La risorsa di Application Insights è denominata IceCreamWebApp e si trova nel gruppo di risorse Fabrikam. Sono il proprietario della sottoscrizione di Azure.

Il GUID è l'ID sottoscrizione (non la chiave di strumentazione dell'applicazione).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Esempio 2
Ho un'applicazione in cui uso [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) per segnalare una metrica denominata "salesPerHour". Invia un messaggio di posta elettronica ai miei colleghi, se "salesPerHour" scende sotto il valore 100, calcolato su una media di 24 ore.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La stessa regola può essere utilizzata per la metrica riportata utilizzando il [parametro misura](app-insights-api-custom-events-metrics.md#properties) di un altra chiamata di rilevamento come ad esempio TrackEvent o trackPageView.

## <a name="metric-names"></a>Nomi delle metriche
| Nome metrica | Nome schermata | Descrizione |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Eccezioni del browser |Conteggio delle eccezioni non rilevate generate nel browser. |
| `basicExceptionServer.count` |Eccezioni del server |Conteggio delle eccezioni non gestite generate dall'applicazione |
| `clientPerformance.clientProcess.value` |Tempo di elaborazione client |Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione. |
| `clientPerformance.networkConnection.value` |Tempo di connessione alla rete per il caricamento della pagina |Tempo impiegato dal browser per connettersi alla rete. Può essere 0 se memorizzato nella cache. |
| `clientPerformance.receiveRequest.value` |Tempo per la ricezione della risposta |Tempo tra l'invio di richiesta del browser e l'inizio della ricezione della risposta. |
| `clientPerformance.sendRequest.value` |Tempo per l'invio della richiesta |Tempo impiegato dal browser per inviare la richiesta. |
| `clientPerformance.total.value` |Tempo di caricamento della pagina del browser |Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini. |
| `performanceCounter.available_bytes.value` |Memoria disponibile |Memoria fisica immediatamente disponibile per l'allocazione a un processo o utilizzabile dal sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Velocità di elaborazione I/O |Numero totale di byte al secondo letti e scritti in file, rete e dispositivi. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |Frequenza di eccezioni |Eccezioni generate al secondo. |
| `performanceCounter.percentage_processor_time.value` |CPU processo |Percentuale di tempo trascorso di tutti i thread di processo usati dal processore per le istruzioni di esecuzione relative al processo dell'applicazione. |
| `performanceCounter.percentage_processor_total.value` |Tempo processore |Percentuale di tempo che il processore dedica a thread non inattivi. |
| `performanceCounter.process_private_bytes.value` |Byte privati processo |Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata. |
| `performanceCounter.request_execution_time.value` |Tempo di esecuzione della richiesta di ASP.NET |Tempo di esecuzione della richiesta più recente. |
| `performanceCounter.requests_in_application_queue.value` |Richieste ASP.NET nella coda di esecuzione |Lunghezza della coda di richieste dell'applicazione. |
| `performanceCounter.requests_per_sec.value` |Percentuale di richieste ASP.NET |Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo. |
| `remoteDependencyFailed.durationMetric.count` |Errori di dipendenze |Conteggio delle chiamate non riuscite effettuate dall'applicazione server alle risorse esterne. |
| `request.duration` |Tempo di risposta del server |Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta. |
| `request.rate` |Frequenza di richieste |Percentuale di tutte le richieste effettuate all'applicazione in un secondo. |
| `requestFailed.count` |Richieste non riuscite |Conteggio delle richieste HTTP per cui è stato restituito un codice di risposta maggiore o uguale a 400 |
| `view.count` |Visualizzazioni pagina |Conteggio delle richieste utente del client per una pagina Web. Il traffico sintetico è filtrato. |
| {nome metrica personalizzato} |{nome metrica} |Il valore della metrica segnalato da [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) o il [parametro delle misurazioni di una chiamata di rilevamento](app-insights-api-custom-events-metrics.md#properties). |

Le metriche vengono inviate da moduli di telemetria diversi:

| Gruppo metrica | Modulo dell'agente di raccolta |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>view |[JavaScript browser](app-insights-javascript.md) |
| performanceCounter |[Prestazioni](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Dipendenza](app-insights-configuration-with-applicationinsights-config.md) |
| request,<br/>requestFailed |[Richiesta server](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhook
È possibile [automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure richiamerà l'indirizzo Web specificato quando viene generato un avviso.

## <a name="see-also"></a>Vedere anche
* [Script per configurare Application Insights](app-insights-powershell-script-create-resource.md)
* [Creare risorse Application Insights e test web da modelli](app-insights-powershell.md)
* [Automatizzare l'accoppiamento tra Diagnostica di Microsoft Azure e Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
