---
title: Monitoraggio del portale di gestione del carico di lavoro
description: Linee guida per il monitoraggio del portale di gestione del carico di lavoro in Azure sinapsi Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/14/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: fd9bd846beba718cb305907d4d0c5a613d2ef816
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029937"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Analisi delle sinapsi di Azure-monitoraggio portale di gestione del carico di lavoro (anteprima)
Questo articolo illustra come monitorare l'utilizzo delle risorse e l'attività di query del [gruppo di carico di lavoro](sql-data-warehouse-workload-isolation.md#workload-groups) . Per informazioni dettagliate su come configurare il Esplora metriche di Azure, vedere l'articolo [Introduzione ad azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md) .  Per informazioni dettagliate su come monitorare l'utilizzo delle risorse di sistema, vedere la sezione relativa all' [utilizzo delle risorse](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) nella documentazione di Azure SQL data warehouse Monitoring.
Sono disponibili due categorie diverse di metriche del gruppo di carico di lavoro per il monitoraggio della gestione del carico di lavoro: allocazione delle risorse e attività di query.  Queste metriche possono essere suddivise e filtrate in base al gruppo del carico di lavoro.  Le metriche possono essere suddivise e filtrate in base a se sono definite dal sistema (gruppi del carico di lavoro della classe di risorse) o definite dall'utente (create dall'utente con la sintassi di [creazione del gruppo del carico di lavoro](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) ).

## <a name="workload-management-metric-definitions"></a>Definizioni delle metriche di gestione del carico di lavoro

|Nome misurazione                    |Description  |Tipo di aggregazione |
|-------------------------------|-------------|-----------------|
|Percentuale di risorse limite effettivo | La percentuale *effettiva delle* risorse limite è un limite rigido per la percentuale di risorse accessibili dal gruppo di carico di lavoro, prendendo in considerazione la *percentuale di risorse min valida* allocata per altri gruppi di carico di lavoro. La metrica di *percentuale effettiva della risorsa Cap* viene configurata utilizzando il parametro `CAP_PERCENTAGE_RESOURCE` nella sintassi di [creazione del gruppo del carico di lavoro](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Il valore effettivo è descritto qui.<br><br>Se, ad esempio, viene creato un gruppo di carico di lavoro `DataLoads` con `CAP_PERCENTAGE_RESOURCE` = 100 e viene creato un altro gruppo di carico di lavoro con una percentuale di risorse minima valida del 25%, la percentuale di *risorse limite effettivo* per il gruppo di carico di lavoro `DataLoads` è 75%.<br><br>La *percentuale effettiva della risorsa Cap* determina il limite superiore della concorrenza (e quindi una potenziale velocità effettiva) che un gruppo di carico di lavoro può raggiungere.  Se è necessaria una velocità effettiva aggiuntiva superiore a quella attualmente segnalata dalla metrica *effettiva di percentuale delle risorse CAP* , aumentare la `CAP_PERCENTAGE_RESOURCE`, ridurre la `MIN_PERCENTAGE_RESOURCE` di altri gruppi di carico di lavoro o aumentare le prestazioni dell'istanza per aggiungere altre risorse.  La riduzione del `REQUEST_MIN_RESOURCE_GRANT_PERCENT` può aumentare la concorrenza, ma potrebbe non aumentare la velocità effettiva complessiva.| Min, AVG, max |
|Percentuale effettiva risorse min |Percentuale di risorse *min effettive* è la percentuale minima di risorse riservate e isolate per il gruppo di carico di lavoro che prende in considerazione il livello di servizio minimo.  La metrica valida per la percentuale di risorse min viene configurata utilizzando il parametro `MIN_PERCENTAGE_RESOURCE` nella sintassi [Crea gruppo del carico di lavoro](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Il valore effettivo è descritto [qui](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Utilizzare il tipo di aggregazione Sum quando questa metrica non è filtrata e viene divisa per monitorare l'isolamento totale del carico di lavoro configurato nel sistema.<br><br>La *percentuale effettiva minima delle risorse* determina il limite inferiore della concorrenza garantita (e quindi della velocità effettiva garantita) che un gruppo di carico di lavoro può raggiungere.  Se sono necessarie risorse garantite aggiuntive oltre a quelle attualmente segnalate dalla metrica *effettiva di percentuale minima delle risorse* , aumentare il `MIN_PERCENTAGE_RESOURCE` parametro configurato per il gruppo del carico di lavoro.  La riduzione del `REQUEST_MIN_RESOURCE_GRANT_PERCENT` può aumentare la concorrenza, ma potrebbe non aumentare la velocità effettiva complessiva. |Min, AVG, max|
|Query attive del gruppo di carico di lavoro  |Questa metrica riporta le query attive all'interno del gruppo di carico di lavoro.  Utilizzando questa metrica non filtrata e unsplit, vengono visualizzate tutte le query attive in esecuzione nel sistema.|SUM         |
|Allocazione gruppo del carico di lavoro per percentuale massima risorse |Questa metrica Visualizza la percentuale di allocazione delle risorse rispetto alla *percentuale effettiva delle risorse CAP* per gruppo di carico di lavoro.  Questa metrica consente di utilizzare in modo efficace il gruppo del carico di lavoro.<br><br>Si consideri un gruppo di carico di lavoro `DataLoads` con una *percentuale di risorse limite valida* pari al 75% e una `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurata al 25%.  L' *allocazione del gruppo di carico di lavoro per valore percentuale massimo risorse* filtrata per `DataLoads` sarebbe 33% (25%/75%) Se è stata eseguita una singola query in questo gruppo del carico di lavoro.<br><br>Usare questa metrica per identificare l'utilizzo di un gruppo di carico di lavoro.  Un valore vicino al 100% indica che vengono utilizzate tutte le risorse disponibili per il gruppo del carico di lavoro.  Inoltre, il gruppo del *carico di lavoro la metrica delle query in coda* per lo stesso gruppo di carico di lavoro che mostra un valore maggiore di zero indicherebbe che il gruppo di carico di lavoro utilizzerebbe risorse aggiuntive se allocato.  Viceversa, se questa metrica è costantemente bassa e le *query attive del gruppo di carico di lavoro* sono basse, il gruppo del carico di lavoro non viene utilizzato.  Questa situazione è particolarmente problematica se la *percentuale di risorse CAP efficace* è maggiore di zero, in quanto ciò indicherebbe un [isolamento del carico di lavoro sottoutilizzato](#underutilized-workload-isolation).|Min, AVG, max |
|Allocazione gruppo del carico di lavoro per percentuale sistema | Questa metrica Visualizza la percentuale di allocazione delle risorse rispetto all'intero sistema.<br><br>Si consideri un gruppo di carico di lavoro `DataLoads` con una `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurata al 25%.  L' *allocazione del gruppo di carico di lavoro in base al valore percentuale del sistema* è filtrata per `DataLoads` sarebbe 25% (25%/100%) Se è stata eseguita una singola query in questo gruppo del carico di lavoro.|Min, AVG, max |
|Timeout query gruppo di carico di lavoro |Query per il gruppo del carico di lavoro di cui si è verificato il timeout.  I timeout delle query segnalati da questa metrica sono solo dopo l'avvio dell'esecuzione della query (non è incluso il tempo di attesa a causa del blocco o delle attese delle risorse).<br><br>Il timeout della query viene configurato utilizzando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` nella sintassi [Crea gruppo del carico di lavoro](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  L'aumento del valore potrebbe ridurre il numero di timeout delle query.<br><br>Prendere in considerazione l'aumento del parametro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` per il gruppo del carico di lavoro per ridurre la quantità di timeout e allocare più risorse per ogni query.  Si noti che, aumentando `REQUEST_MIN_RESOURCE_GRANT_PERCENT` si riduce la quantità di concorrenza per il gruppo del carico di lavoro. |SUM |
|Query in coda del gruppo di carico di lavoro | Query per il gruppo del carico di lavoro attualmente in coda in attesa di avvio dell'esecuzione.  Le query possono essere accodate perché sono in attesa di risorse o blocchi.<br><br>È possibile che le query attendano diversi motivi.  Se il sistema è sottoposto a overload e la richiesta di concorrenza è superiore a quella disponibile, le query vengono accodate.<br><br>Si consiglia di aggiungere altre risorse al gruppo di carico di lavoro aumentando il `CAP_PERCENTAGE_RESOURCE` parametro nell'istruzione [create workload Group](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Se `CAP_PERCENTAGE_RESOURCE` è maggiore della metrica di *percentuale effettiva della risorsa Cap* , l'isolamento del carico di lavoro configurato per altro gruppo di carico di lavoro influirà sulle risorse allocate a questo gruppo di carico di lavoro.  Valutare la possibilità di ridurre `MIN_PERCENTAGE_RESOURCE` di altri gruppi di carico di lavoro o di aumentare il numero di istanze per aggiungere altre risorse. |SUM |

## <a name="monitoring-scenarios-and-actions"></a>Scenari e azioni di monitoraggio
Di seguito sono riportate una serie di configurazioni di grafico per evidenziare l'utilizzo della metrica di gestione del carico di lavoro per la risoluzione dei problemi, insieme alle azioni associate.

### <a name="underutilized-workload-isolation"></a>Isolamento del carico di lavoro sottoutilizzato
Si considerino il gruppo di carico di lavoro e la configurazione di classificazione seguenti in cui viene creato un gruppo di carico di lavoro denominato `wgPriority` e viene eseguito il mapping di *TheCEO* `membername` utilizzando il classificatore del carico di lavoro `wcCEOPriority`  Per il gruppo di carico di lavoro `wgPriority` è stato configurato il 25% di isolamento del carico di lavoro (`MIN_PERCENTAGE_RESOURCE` = 25).  A ogni query inviata da *TheCEO* viene assegnato il 5% delle risorse di sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
Il grafico seguente viene configurato come segue:<br>
Metrica 1: *percentuale effettiva minima di risorse* (Media aggregazione, `blue line`)<br>
Metrica 2: *allocazione del gruppo di carico di lavoro per percentuale del sistema* (Media aggregazione, `purple line`)<br>
Filter: [gruppo di carico di lavoro] = `wgPriority`<br>
![underutilized-WG. png](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) il grafico mostra che con l'isolamento del carico di lavoro del 25% viene usato in media solo il 10%.  In questo caso, il valore del parametro `MIN_PERCENTAGE_RESOURCE` può essere ridotto a un valore compreso tra 10 o 15 e consentire ad altri carichi di lavoro nel sistema di utilizzare le risorse.

### <a name="workload-group-bottleneck"></a>Collo di bottiglia gruppo di carico di lavoro
Si considerino il gruppo di carico di lavoro e la configurazione di classificazione seguenti in cui viene creato un gruppo di carico di lavoro denominato `wgDataAnalyst` e viene eseguito il mapping del `membername` *Dataanalystt* usando il classificatore del carico di lavoro `wcDataAnalyst`.  Il gruppo del carico di lavoro `wgDataAnalyst` dispone del 6% di isolamento del carico di lavoro configurato (`MIN_PERCENTAGE_RESOURCE` = 6) e un limite di risorse pari al 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  A ogni query inviata da *Dataanalystt* viene assegnato il 3% delle risorse di sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
Il grafico seguente viene configurato come segue:<br>
Metrica 1: *percentuale di risorse limite effettivo* (Media aggregazione, `blue line`)<br>
Metrica 2: *allocazione del gruppo di carico di lavoro per percentuale massima di risorse* (Media aggregazione, `purple line`)<br>
Metrica 3: *query in coda del gruppo di carico di lavoro* (aggregazione Sum, `turquoise line`)<br>
Filter: [gruppo di carico di lavoro] = `wgDataAnalyst`<br>
![bottle-Collared-WG](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) grafico mostra che con un limite del 9% sulle risorse, il gruppo del carico di lavoro è pari al 90% + usato (dal *gruppo di carico di lavoro allocato per la metrica massima percentuale di risorse*).  Viene staziona un accodamento delle query, come illustrato dalla *metrica delle query in coda del gruppo di carico di lavoro*.  In questo caso, l'aumento della `CAP_PERCENTAGE_RESOURCE` a un valore superiore al 9% consentirà l'esecuzione simultanea di più query.  Aumentando il `CAP_PERCENTAGE_RESOURCE` si presuppone che siano disponibili risorse sufficienti e non isolate da altri gruppi del carico di lavoro.  Verificare che il limite sia aumentato controllando la *metrica di percentuale delle risorse CAP valida*.  Se si desidera una maggiore velocità effettiva, provare anche ad aumentare il `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a un valore maggiore di 3.  L'aumento del `REQUEST_MIN_RESOURCE_GRANT_PERCENT` potrebbe consentire una maggiore velocità di esecuzione delle query.

## <a name="next-steps"></a>Passaggi successivi
[Guida introduttiva: configurare l'isolamento del carico di lavoro tramite T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[CREAZIONE di un gruppo di carico di lavoro (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[Crea CLASSIFICATOre del carico di lavoro (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Monitoraggio dell'utilizzo delle risorse](sql-data-warehouse-concept-resource-utilization-query-activity.md)

