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
### <a name="general-query-limits"></a>Limiti di query generali

| Limite | Descrizione |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso [linguaggio di query di Kusto](/azure/kusto/query/) di Azure Esplora dati. Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](../articles/azure-monitor/log-query/data-explorer-difference.md) per gli elementi del linguaggio KQL non supportati in monitoraggio di Azure. |
| Aree di Azure | Le query di log possono avere un sovraccarico eccessivo quando i dati si estendono Log Analytics aree di lavoro in più aree di Azure. Per informazioni dettagliate, vedere [limiti della query](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Query tra risorse | Il numero massimo di risorse di Application Insights e di Log Analytics aree di lavoro in una singola query è limitato a 100.<br>La query tra risorse non è supportata in Progettazione viste.<br>Le query su più risorse negli avvisi dei log sono supportate nella nuova API scheduledQueryRules.<br>Per informazioni dettagliate, vedere [limiti di query tra risorse](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |

### <a name="user-query-throttling"></a>Limitazione query utente
Monitoraggio di Azure presenta diversi limiti di limitazione per la protezione dagli utenti che inviano un numero eccessivo di query. Questo comportamento può potenzialmente sovraccaricare le risorse back-end del sistema e compromettere la velocità di risposta del servizio. I limiti seguenti sono progettati per proteggere i clienti dalle interruzioni e garantire un livello di servizio coerente. La limitazione e i limiti dell'utente sono progettati per influito solo sullo scenario di utilizzo estremo e non devono essere rilevanti per l'utilizzo tipico.


| Measure | Limite per utente | Descrizione |
|:---|:---|:---|
| Query simultanee | 5 | Se per l'utente sono già in esecuzione 5 query, le nuove query vengono inserite in una coda di concorrenza per singolo utente. Al termine di una delle query in esecuzione, la query successiva verrà ritirata dalla coda e avviata. Non sono incluse le query dalle regole di avviso.
| Tempo nella coda di concorrenza | 2,5 minuti | Se una query si trova nella coda per più di 2,5 minuti senza essere avviata, verrà terminata con una risposta di errore HTTP con codice 429. |
| Totale query nella coda di concorrenza | 40 | Quando il numero di query nella coda raggiunge il 40, eventuali query aggiuntive vengono rifiutate con un codice di errore HTTP 429. Questo numero è in aggiunta alle 5 query che possono essere eseguite contemporaneamente. |
| Frequenza query | 200 query per 30 secondi | Si tratta della velocità complessiva con cui le query possono essere inviate da un singolo utente a tutte le aree di lavoro.  Questo limite si applica alle query a livello di codice o alle query avviate da parti di visualizzazione come i dashboard di Azure e la pagina di riepilogo dell'area di lavoro Log Analytics. |

- Ottimizzare le query come descritto in [ottimizzare le query del log in monitoraggio di Azure](../articles/azure-monitor/log-query/query-optimization.md).
- I dashboard e le cartelle di lavoro possono contenere più query in una singola visualizzazione che generano un picchi di query ogni volta che vengono caricati o aggiornati. Prendere in considerazione la suddivisione in più visualizzazioni che vengono caricate su richiesta. 
- In Power BI, è consigliabile estrarre solo i risultati aggregati anziché i log non elaborati.