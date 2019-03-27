---
title: Procedure consigliate per Query Store in Database di Azure per PostgreSQL
description: Questo articolo illustra le procedure consigliate per Query Store in Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: af68d5cdc891a8c924ca493d8e798edaa4670ab5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483996"
---
# <a name="best-practices-for-query-store"></a>Procedure consigliate per Query Store

**Si applica a:** Database di Azure per PostgreSQL 9.6 e 10

Questo articolo illustra le procedure consigliate per usare Query Store in Database di Azure per PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Impostare la modalità di acquisizione query ottimale
Consentire a Query Store di acquisire i dati importanti. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Tutti_  |Analizzare l'intero carico di lavoro esaminando tutte le query, la rispettiva frequenza di esecuzione e altre statistiche. Identificare le nuove query nel carico di lavoro. Rilevare se le query ad hoc vengono usate per identificare le opportunità di parametrizzazione automatica o dell'utente. La modalità _All_ comporta un costo maggiore per il consumo delle risorse. |
|_Top_  |Concentrare l'attenzione sulle query più frequenti, ovvero quelle emesse dai client.
|_Nessuno_ |Sono già stati acquisiti un set di query e un intervallo di tempo da analizzare e si vogliono eliminare eventuali distrazioni causate da altre query. La modalità _None_ è adatta agli ambienti di test e di benchmark. La modalità _None_ deve essere usata con attenzione perché potrebbe impedire di tenere traccia di nuove query importanti e di ottimizzarle. Non è possibile ripristinare i dati negli intervalli di tempo trascorsi. |

Query Store include anche un archivio per le statistiche di attesa. Esiste un'altra modalità di acquisizione delle query che regola le statistiche di attesa: **pgms_wait_sampling.query_capture_mode** può essere impostata su _none_ o su _all_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** prevale su **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode è _none_, l'impostazione di pgms_wait_sampling.query_capture_mode non ha effetto. 


## <a name="keep-the-data-you-need"></a>Conservare i dati necessari
Il parametro **pg_qs.retention_period_in_days** specifica il periodo di conservazione dati, in giorni, per Query Store. I dati delle query e delle statistiche meno recenti vengono eliminati. Per impostazione predefinita, Query Store è configurato per conservare i dati per 7 giorni. Evitare di conservare i dati cronologici che non si intende usare. Aumentare il valore se è necessario conservare i dati più a lungo.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Impostare la frequenza di campionamento delle statistiche di attesa 
Il parametro **pgms_wait_sampling.history_period** specifica (in millisecondi) ogni quanto vengono campionati gli eventi di attesa. Più è breve il periodo, più frequente è il campionamento. Vengono recuperate più informazioni, ma il costo del consumo delle risorse è maggiore. Se il server è in condizioni di carico o la granularità non è necessaria, prolungare questo periodo


## <a name="get-quick-insights-into-query-store"></a>Informazioni rapide su Query Store
È possibile usare [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure per ottenere rapidamente informazioni sui dati in Query Store. Le visualizzazioni includono le query in esecuzione da più tempo e gli eventi di attesa più lunghi.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come ottenere o impostare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).