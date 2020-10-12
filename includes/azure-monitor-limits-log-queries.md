---
title: includere file
description: includere file
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 83754842eeb4b5d609596045c11451e898960b9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064869"
---
### <a name="general-query-limits"></a>Limiti per le query generici

| Limite | Descrizione |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso [linguaggio di query Kusto](/azure/kusto/query/) di Esplora dati di Azure. Vedere [Differenze nel linguaggio di query di log di Monitoraggio di Azure](../articles/azure-monitor/log-query/data-explorer-difference.md) per gli elementi del linguaggio KQL non supportati in Monitoraggio di Azure. |
| Aree di Azure | Nelle query su log è possibile riscontrare un sovraccarico eccessivo quando i dati si estendono in più aree di Azure nelle aree di lavoro Log Analytics. Per informazioni dettagliate, vedere [Limiti per le query](../articles/azure-monitor/log-query/scope.md#query-limits). |
| Query su più risorse | Il numero massimo di risorse di Application Insights e di aree di lavoro Log Analytics che è possibile includere in una singola query è limitato a 100.<br>Le query su più risorse non sono supportate Progettazione visualizzazioni.<br>Le query su più risorse negli avvisi dei log sono supportate nella nuova API scheduledQueryRules.<br>Per informazioni dettagliate, vedere [Limiti per le query su più risorse](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits). |

### <a name="user-query-throttling"></a>Limitazione delle query utente
Monitoraggio di Azure prevede diversi limiti per evitare che gli utenti inviino un numero eccessivo di query. Questo comportamento può potenzialmente sovraccaricare le risorse back-end di sistema e compromettere la velocità di risposta del servizio. I limiti seguenti sono progettati per proteggere i clienti dalle interruzioni e garantire un livello di servizio uniforme. I limiti e la limitazione delle richieste utente sono pensati solo per scenari di utilizzo estremo e non devono essere considerati pertinenti nei casi di utilizzo standard.


| Measure | Limite per utente | Descrizione |
|:---|:---|:---|
| Query simultanee | 5 | Se per l'utente sono già in esecuzione cinque query, le nuove query vengono inserite in una coda di concorrenza per singolo utente. Al termine di una delle query in esecuzione, la query successiva verrà prelevata dalla coda e avviata. Sono escluse le query generate da regole di avviso.
| Tempo nella coda di concorrenza | 3 minuti | Se una query rimane nella coda per più di tre minuti senza essere avviata, verrà terminata con una risposta di errore HTTP con codice 429. |
| Totale query nella coda di concorrenza | 200 | Quando nella coda sono presenti 200 query, eventuali query aggiuntive vengono rifiutate e viene restituito un codice errore HTTP 429. Questo numero si aggiunge alle cinque query che possono essere eseguite contemporaneamente. |
| Frequenza delle query | 200 query ogni 30 secondi | Si tratta della velocità complessiva con cui le query possono essere inviate da un singolo utente a tutte le aree di lavoro.  Questo limite si applica alle query a livello di codice o alle query avviate da parti di visualizzazione, come i dashboard di Azure e la pagina di riepilogo dell'area di lavoro Log Analytics. |

- Ottimizzare le query come descritto in [Ottimizzare le query sui log in Monitoraggio di Azure](../articles/azure-monitor/log-query/query-optimization.md).
- I dashboard e le cartelle di lavoro possono contenere più query in una singola visualizzazione e generano un picco di query ogni volta che vengono caricati o aggiornati. Provare a suddividerli in più visualizzazioni che vengono caricate su richiesta. 
- In Power BI è consigliabile estrarre solo i risultati aggregati invece dei log non elaborati.
