---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072638"
---
### <a name="general-query-limits"></a>Limiti generali delle query

| Limite | Descrizione |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso linguaggio di query Kusto di Azure Data Explorer.Azure Monitor uses the same [Kusto query language](/azure/kusto/query/) as Azure Data Explorer. Vedere Differenze del [linguaggio](../articles/azure-monitor/log-query/data-explorer-difference.md) di query del log di Monitoraggio di Azure per gli elementi del linguaggio KQL non supportati in Monitoraggio di Azure.See Azure Monitor log query language differences for KQL language elements not supported in Azure Monitor. |
| Aree di Azure | Le query di log possono verificarsi un sovraccarico eccessivo quando i dati si estendono su aree di lavoro di Log Analytics in più aree di Azure.Log queries can experience excessive overhead when data spans Log Analytics workspaces in multiple Azure regions. Per informazioni dettagliate, vedere [Limiti delle query.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Query tra risorse | Numero massimo di risorse di Application Insights e aree di lavoro di Log Analytics in una singola query limitata a 100.Maximum number of Application Insights resources and Log Analytics workspaces in a single query limited to 100.<br>La query tra risorse non è supportata in Progettazione viste.<br>Le query su più risorse negli avvisi dei log sono supportate nella nuova API scheduledQueryRules.<br>Per informazioni [dettagliate,](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) vedere Limiti delle query tra risorse. |

### <a name="user-query-throttling"></a>Limitazione delle query utenteUser query throttling
Monitoraggio Azure include diversi limiti di limitazione per la protezione contro l'invio eccessivo di query da parte degli utenti. Tale comportamento può potenzialmente sovraccaricare le risorse back-end del sistema e compromettere la velocità di risposta del servizio. I limiti seguenti sono progettati per proteggere i clienti dalle interruzioni e garantire un livello di servizio coerente. Le limitazioni utente e i limiti sono progettati per influire solo sullo scenario di utilizzo estremo e non devono essere rilevanti per l'utilizzo tipico.


| Measure | Limite per utente | Descrizione |
|:---|:---|:---|
| Query simultanee | 5 | Se sono già in esecuzione 5 query per l'utente, tutte le nuove query vengono inserite in una coda di concorrenza per utente. Quando una delle query in esecuzione termina, la query successiva verrà estratta dalla coda e avviata. Non sono incluse le query dalle regole di avviso.
| Ora nella coda di concorrenzaTime in concurrency queue | 2,5 minuti | Se una query si trova nella coda per più di 2,5 minuti senza essere avviata, verrà terminata con una risposta di errore HTTP con codice 429. |
| Totale query nella coda di concorrenza | 40 | Una volta che il numero di query nella coda raggiunge 40, eventuali query aggiuntive lo saranno da rifiutato con un codice di errore HTTP 429. Questo numero si aggiunge alle 5 query che possono essere eseguite contemporaneamente. |
| Frequenza delle query | 200 query ogni 30 secondi | Si tratta della frequenza complessiva con cui le query possono essere inviate da un singolo utente a tutte le aree di lavoro.  Questo limite si applica alle query a livello di codice o alle query avviate da parti di visualizzazione, ad esempio dashboard di Azure e la pagina di riepilogo dell'area di lavoro di Log Analytics.This limit applies to programmatic queries or queries initiated by visualization parts such as Azure dashboards and the Log Analytics workspace summary page. |

- Ottimizzare le query come descritto in [Ottimizzare le query](../articles/azure-monitor/log-query/query-optimization.md)di log in Monitoraggio di Azure .
- I dashboard e le cartelle di lavoro possono contenere più query in un'unica visualizzazione che generano un burst di query ogni volta che vengono caricate o aggiornate. Valutare la possibilità di suddividerli in più visualizzazioni caricate su richiesta. 
- In Power BI è consigliabile estrarre solo i risultati aggregati anziché i log non elaborati.