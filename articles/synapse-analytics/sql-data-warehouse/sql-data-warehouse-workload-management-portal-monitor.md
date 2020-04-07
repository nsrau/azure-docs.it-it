---
title: Monitoraggio del portale di gestione del carico di lavoroWorkload management portal monitoring
description: Indicazioni per il monitoraggio del portale di gestione del carico di lavoro in Azure Synapse Analytics.Guidance for workload management portal monitoring in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a79e6fb2be717b5ecee243b26824039630e1a416
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744279"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Analisi synapse di Azure - Monitoraggio del portale di gestione del carico di lavoro (anteprima)Azure Synapse Analytics – Workload Management Portal Monitoring (Preview)

In questo articolo viene illustrato come monitorare l'utilizzo delle risorse del gruppo di carico di [lavoro](sql-data-warehouse-workload-isolation.md#workload-groups) e l'attività di query.
Per informazioni dettagliate su come configurare Azure Metrics Explorer, vedere l'articolo Introduzione a Azure Metrics Explorer.For details on how to configure the Azure Metrics Explorer see [the Getting started with Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) article.  Per informazioni dettagliate su come monitorare l'utilizzo delle risorse di sistema, vedere la sezione [Utilizzo delle risorse](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) nella documentazione relativa al monitoraggio dell'analisi di Azure Synapse.See the Resource utilization section in Azure Synapse Analytics Monitoring documentation for details on how to monitor system resource consumption.
Esistono due diverse categorie di metriche del gruppo di carico di lavoro fornite per il monitoraggio della gestione del carico di lavoro: allocazione delle risorse e attività di query.  Queste metriche possono essere suddivise e filtrate per gruppo di carico di lavoro.  Le metriche possono essere suddivise e filtrate in base se sono definite dal sistema (gruppi di carichi di lavoro della classe di risorse) o definite dall'utente (create dall'utente con la sintassi [CREATE WORKLOAD GROUP).](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Definizioni delle metriche di gestione del carico di lavoroWorkload management metric

|Nome misurazione                    |Descrizione  |Tipo di aggregazione |
|-------------------------------|-------------|-----------------|
|Percentuale di risorse limite efficace | *La percentuale* di risorse limite effettivo è un limite rigido alla percentuale di risorse accessibili dal gruppo di carico di lavoro, tenendo conto della percentuale di *risorse limite effettive* allocate per altri gruppi di carico di lavoro. La metrica *Percentuale risorsa* `CAP_PERCENTAGE_RESOURCE` limite effettivo viene configurata utilizzando il parametro nella sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Il valore effettivo è descritto qui.<br><br>Ad esempio, se `DataLoads` viene `CAP_PERCENTAGE_RESOURCE` creato un gruppo di carico di lavoro con 100 e un altro gruppo di `DataLoads` carico di lavoro con una percentuale di risorsa min effettiva del 25%, la *percentuale* di risorsa limite effettivo per il gruppo di carico di lavoro è 75%.<br><br>La *percentuale* di risorse limite effettivo determina il limite superiore della concorrenza (e quindi della velocità effettiva potenziale) che un gruppo di carico di lavoro può raggiungere.  Se è necessaria una velocità effettiva aggiuntiva oltre a quella attualmente `CAP_PERCENTAGE_RESOURCE`segnalata `MIN_PERCENTAGE_RESOURCE` dalla metrica *Percentuale risorse limite effettivo,* aumentare la , ridurre la velocità di produzione di altri gruppi di carico di lavoro o aumentare l'istanza per aggiungere altre risorse.  Diminuendo la concorrenza può aumentare la `REQUEST_MIN_RESOURCE_GRANT_PERCENT` concorrenza, ma non può aumentare la velocità effettiva complessiva.| Min, Media, Max |
|Percentuale effettiva risorsa min |*Percentuale minima risorse effettiva* è la percentuale minima di risorse riservate e isolate per il gruppo di carico di lavoro tenendo conto del livello minimo di servizio.  La metrica Percentuale risorsa min effettiva viene configurata utilizzando il `MIN_PERCENTAGE_RESOURCE` parametro nella sintassi CREATE WORKLOAD [GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Il valore effettivo è descritto [qui](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values).<br><br>Usare il tipo di aggregazione Somma quando questa metrica non è filtrata e non viene divisa per monitorare l'isolamento totale del carico di lavoro configurato nel sistema.<br><br>La *percentuale di risorsa minima effettiva* determina il limite inferiore della concorrenza garantita (e quindi della velocità effettiva garantita) che un gruppo di carico di lavoro può raggiungere.  Se sono necessarie ulteriori risorse garantite oltre a quelle attualmente segnalate `MIN_PERCENTAGE_RESOURCE` dalla metrica *Percentuale risorsa min effettiva,* aumentare il parametro configurato per il gruppo del carico di lavoro.  Diminuendo la concorrenza può aumentare la `REQUEST_MIN_RESOURCE_GRANT_PERCENT` concorrenza, ma non può aumentare la velocità effettiva complessiva. |Min, Media, Max|
|Query attive del gruppo del carico di lavoroWorkload group active queries  |Questa metrica segnala le query attive all'interno del gruppo del carico di lavoro.  L'utilizzo di questa metrica non filtrata e non divisa consente di visualizzare tutte le query attive in esecuzione nel sistema.|SUM         |
|Allocazione del gruppo di carico di lavoro per percentuale di risorse max |Questa metrica visualizza l'allocazione percentuale delle risorse rispetto alla *percentuale* di risorse limite effettivo per gruppo di carico di lavoro.  Questa metrica fornisce l'utilizzo efficace del gruppo del carico di lavoro.<br><br>Si consideri `DataLoads` un gruppo di carico di lavoro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` con una *percentuale* di risorsa limite effettivo del 75% e configurato al 25%.  *L'allocazione* del gruppo di `DataLoads` carico di lavoro per il valore percentuale massimo di risorse filtrato per sarebbe 33% (25% / 75%) se una singola query fosse in esecuzione in questo gruppo di carico di lavoro.<br><br>Utilizzare questa metrica per identificare l'utilizzo di un gruppo di carico di lavoro.  Un valore vicino al 100% indica che vengono utilizzate tutte le risorse disponibili per il gruppo di carico di lavoro.  Inoltre, la *metrica* delle query del gruppo di carico di lavoro in coda per lo stesso gruppo di carico di lavoro che mostra un valore maggiore di zero indicherebbe che il gruppo di carico di lavoro utilizzerebbe risorse aggiuntive se allocato.  Al contrario, se questa metrica è costantemente bassa e le *query attive del gruppo Carico* di lavoro sono basse, il gruppo di carico di lavoro non viene utilizzato.  Questa situazione è particolarmente problematica se *la percentuale di risorse limite effettivo* è maggiore di zero in quanto indicherebbe l'isolamento del carico di lavoro [sottoutilizzato.](#underutilized-workload-isolation)|Min, Media, Max |
|Allocazione del gruppo di carico di lavoro in base alla percentuale di sistemaWorkload group allocation by system | Questa metrica visualizza l'allocazione percentuale delle risorse rispetto all'intero sistema.<br><br>Si consideri `DataLoads` un `REQUEST_MIN_RESOURCE_GRANT_PERCENT` gruppo di carico di lavoro con un configurato al 25%.  *L'allocazione del gruppo* `DataLoads` di carico di lavoro in base al valore percentuale di sistema è 25% (25% / 100%) se una singola query fosse in esecuzione in questo gruppo di carico di lavoro.|Min, Media, Max |
|Timeout delle query del gruppo di carico di lavoroWorkload group query timeouts |Query per il gruppo del carico di lavoro con timeout.  I timeout delle query segnalati da questa metrica sono solo una volta avviata l'esecuzione della query (non include il tempo di attesa a causa del blocco o delle attese delle risorse).<br><br>Il timeout delle `QUERY_EXECUTION_TIMEOUT_SEC` query viene configurato utilizzando il parametro nella sintassi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  L'aumento del valore potrebbe ridurre il numero di timeout delle query.<br><br>Valutare la `REQUEST_MIN_RESOURCE_GRANT_PERCENT` possibilità di aumentare il parametro per il gruppo di carico di lavoro per ridurre la quantità di timeout e allocare più risorse per ogni query.  Si noti che l'aumento `REQUEST_MIN_RESOURCE_GRANT_PERCENT` riduce la quantità di concorrenza per il gruppo di carico di lavoro. |SUM |
|Query in coda del gruppo di carico di lavoroWorkload group queued queries | Query per il gruppo del carico di lavoro attualmente in coda per avviare l'esecuzione.  Le query possono essere in coda perché sono in attesa di risorse o blocchi.<br><br>Le query potrebbero essere in attesa di numerosi motivi.  Se il sistema è sottoposto a overload e la domanda di concorrenza è maggiore di quella disponibile, le query verranno accodate.<br><br>Valutare la possibilità di aggiungere altre `CAP_PERCENTAGE_RESOURCE` risorse al gruppo di carico di lavoro aumentando il parametro nell'istruzione [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Se `CAP_PERCENTAGE_RESOURCE` è maggiore della metrica *Percentuale risorse limite effettivo,* l'isolamento del carico di lavoro configurato per altri gruppi di carichi di lavoro influisce sulle risorse allocate a questo gruppo di carico di lavoro.  Valutare la `MIN_PERCENTAGE_RESOURCE` possibilità di ridurre i gruppi del carico di lavoro o di aumentare la scalabilità dell'istanza per aggiungere altre risorse. |SUM |

## <a name="monitoring-scenarios-and-actions"></a>Scenari e azioni di monitoraggio

Di seguito è riportata una serie di configurazioni del grafico per evidenziare l'utilizzo della metrica di gestione del carico di lavoro per la risoluzione dei problemi insieme alle azioni associate per risolvere il problema.

### <a name="underutilized-workload-isolation"></a>Isolamento del carico di lavoro sottoutilizzato

Si consideri la configurazione del gruppo di `wgPriority` carico di lavoro e del classificatore seguenti in cui viene creato un gruppo di carico di lavoro denominato e *theCEO* `membername` viene mappato ad esso usando il classificatore del `wcCEOPriority` carico di lavoro.  Il `wgPriority` gruppo del carico di lavoro ha`MIN_PERCENTAGE_RESOURCE` il 25% di isolamento del carico di lavoro configurato per esso (25 USD).  A ogni query inviata da *TheCEO* viene`REQUEST_MIN_RESOURCE_GRANT_PERCENT` assegnato il 5% delle risorse di sistema (n. 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Il grafico seguente è configurato come segue:<br>
Metrica 1: *percentuale effettiva della risorsa min* (aggregazione media, `blue line`)<br>
Metrica 2: allocazione del gruppo di *carico di lavoro in base alla percentuale* di sistema (aggregazione media, `purple line`)<br>
Filtro: [Gruppo di carico di lavoro]`wgPriority`<br>
![sottoutilizzato-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) Il grafico mostra che con il 25% di isolamento del carico di lavoro, solo il 10% viene utilizzato in media.  In questo caso, il valore del `MIN_PERCENTAGE_RESOURCE` parametro può essere ridotto a 10 o 15 e consentire ad altri carichi di lavoro nel sistema di utilizzare le risorse.

### <a name="workload-group-bottleneck"></a>Collo di bottiglia del gruppo di carico di lavoroWorkload group

Si consideri la configurazione del gruppo di `wgDataAnalyst` carico di lavoro e del classificatore `wcDataAnalyst` seguenti in cui viene creato un gruppo di carico di lavoro denominato e il *dataAnalyst* `membername` viene mappato ad esso utilizzando il classificatore del carico di lavoro.  Il `wgDataAnalyst` gruppo del carico di lavoro`MIN_PERCENTAGE_RESOURCE` dispone di un isolamento del carico`CAP_PERCENTAGE_RESOURCE` di lavoro del 6% configurato al suo nome (-6) e di un limite di risorse del 9% (sezione 9).  A ogni query inviata da *DataAnalyst* viene`REQUEST_MIN_RESOURCE_GRANT_PERCENT` assegnato il 3% delle risorse di sistema (n. 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Il grafico seguente è configurato come segue:<br>
Metrica 1: *percentuale di* risorse `blue line`limite effettivo (aggregazione media, )<br>
Metrica 2: *allocazione del gruppo del carico di lavoro in base alla percentuale di risorse max* (aggregazione media, `purple line`)<br>
Metrica 3: *Query in coda del gruppo di carico* di lavoro (aggregazione Sum, `turquoise line`)<br>
Filtro: [Gruppo di carico di lavoro]`wgDataAnalyst`<br>
![bottiglia collo-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) Il grafico mostra che con un limite del 9% sulle risorse, il gruppo di carico di lavoro viene utilizzato al 90% (dal gruppo del carico di lavoro allocazione in base alla *metrica percentuale massima delle risorse).*  È disponibile un'accodamento costante delle query come mostrato dalla *metrica Query accodate gruppo carico*di lavoro .  In questo caso, `CAP_PERCENTAGE_RESOURCE` l'aumento di a un valore superiore al 9% consentirà l'esecuzione simultanea di più query.  L'aumento dei `CAP_PERCENTAGE_RESOURCE` presupposti che siano disponibili risorse sufficienti e non isolate da altri gruppi di carico di lavoro.  Verificare il limite aumentato controllando la *metrica Percentuale risorsa limite effettivo*.  Se si desidera una maggiore velocità `REQUEST_MIN_RESOURCE_GRANT_PERCENT` effettiva, è consigliabile aumentare anche il valore a un valore maggiore di 3.If more throughput is desired, also consider increasing the to a value greater than 3.  Aumentando `REQUEST_MIN_RESOURCE_GRANT_PERCENT` il potrebbe consentire l'esecuzione più rapida delle query.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Configurare l'isolamento del carico di lavoro tramite T-SQLQuickstart: Configure workload isolation using T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [Monitoraggio dell'utilizzo delle risorseMonitoring resource utilization](sql-data-warehouse-concept-resource-utilization-query-activity.md)
