---
title: Soluzione Azure SQL Analytics in Log Analytics | Microsoft Docs
description: La soluzione Azure SQL Analytics consente di gestire i database SQL di Azure.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 503cf4afba4575492984891a681c187a8683a553
ms.openlocfilehash: 9d8fd7ec594671e1ea7bf06459494f1c3a1adbdf
ms.lasthandoff: 02/23/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorare un database SQL di Azure usando Azure SQL Analytics (anteprima) in Log Analytics

La soluzione di monitoraggio di Azure SQL in Azure Log Analytics raccoglie e visualizza importanti metriche sulle prestazioni di SQL Azure. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. È anche possibile monitorare il database SQL di Azure e le metriche dei pool elastici in più sottoscrizioni e pool elastici di Azure e visualizzarli. La soluzione consente anche di identificare i problemi a ogni livello dello stack di applicazioni.  Usa le [metriche di Diagnostica di Azure](log-analytics-azure-storage.md) con le viste di Log Analytics per presentare i dati su tutti i database SQL di Azure e i pool elastici in un'unica area di lavoro di Log Analytics.

Questa soluzione in anteprima supporta attualmente fino a 150.000 database SQL di Azure e 5.000 pool elastici SQL per area di lavoro.

La soluzione di monitoraggio di Azure SQL, come altre disponibili per Log Analytics, consente di monitorare e ricevere notifiche sull'integrità delle risorse di Azure, in questo caso il database SQL di Azure. Il database SQL di Microsoft Azure è un servizio di database relazionale scalabile che fornisce le comuni funzionalità di tipo SQL Server alle applicazioni in esecuzione nel cloud di Azure. Log Analytics consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

## <a name="connected-sources"></a>Origini connesse

Diversamente dalla maggior parte delle altre soluzioni di Log Analytics, la soluzione di monitoraggio di Azure SQL non usa agenti per connettersi al servizio Log Analytics.

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supporto | Descrizione |
| --- | --- | --- |
| [Agenti di Windows](log-analytics-windows-agents.md) | No | Gli agenti Windows diretti non vengono usati dalla soluzione. |
| [Agenti Linux](log-analytics-linux-agents.md) | No | Gli agenti Linux diretti non vengono usati dalla soluzione. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | No | Una connessione diretta dall'agente SCOM a Log Analytics non viene usata dalla soluzione. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | Sì | I dati relativi alle metriche di Azure vengono inviati a Log Analytics usando l'account di archiviazione. |

## <a name="prerequisites"></a>Prerequisiti

1. Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile crearne una [gratuitamente](https://azure.microsoft.com/free/).
2. Un'area di lavoro di Log Analytics. È possibile usarne una esistente o [crearne una nuova](log-analytics-get-started.md) prima di iniziare a usare questa soluzione.
3. Abilitare Diagnostica di Azure per i database SQL di Azure SQL e i pool elastici e [configurarli per l'invio dei dati a Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configurazione

Eseguire questa procedura per aggiungere la soluzione di monitoraggio di Azure SQL all'area di lavoro.

1. Aggiungere la soluzione di monitoraggio di Azure SQL all'area di lavoro usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Nel portale di Azure fare clic su **Nuovo** (il simbolo +), quindi nell'elenco di risorse selezionare **Monitoraggio e gestione**.  
    ![Monitoraggio e gestione](./media/log-analytics-azure-sql/monitoring-management.png)
3. Nell'elenco **Monitoraggio e gestione** fare clic su **Visualizza tutto**.
4. Nell'elenco **Consigliati** fare clic su **Altro** e quindi nel nuovo elenco trovare **Azure SQL Analytics (anteprima)** e selezionarlo.  
    ![Soluzione Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Nel pannello **Azure SQL Analytics (anteprima)** fare clic su **Crea**.  
    ![Creare](./media/log-analytics-azure-sql/portal-create.png)
6. Nel pannello **Crea nuova soluzione** selezionare l'area di lavoro che si vuole aggiungere alla soluzione e quindi fare clic su **Crea**.  
    ![Aggiunta all'area di lavoro](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Per configurare più sottoscrizioni di Azure

Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell). Specificare l'ID risorsa dell'area di lavoro come parametro quando si esegue lo script per inviare i dati di diagnostica dalle risorse in una sottoscrizione di Azure a un'area di lavoro in un'altra sottoscrizione di Azure.

**Esempio**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione all'area di lavoro, il riquadro Azure SQL Analytics viene aggiunto all'area di lavoro e visualizzato in Panoramica. Il riquadro mostra il numero di database SQL di Azure e di pool elastici SQL a cui la soluzione è connessa.

![Riquadro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-monitoring-data"></a>Visualizzazione dei dati di monitoraggio di Azure SQL

Fare clic sul riquadro **Azure SQL Monitoring** (Monitoraggio di Azure SQL) per aprire il dashboard Azure SQL Analytics. Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca fino a dieci elementi corrispondenti ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

Vedere [Opzioni e prestazioni del database SQL per i livelli di servizio](../sql-database/sql-database-service-tiers.md).



![Dashboard Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-dash-01.png)



![Dashboard Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-dash-02.png)

| Colonna | Descrizione |
| --- | --- |
| **Azure SQL Database Analytics** | &nbsp; |
| Top N Databases by DTU Utilization &gt; 90% (Primi N database per utilizzo DTU > 90%) | Questo pannello visualizza il numero di database SQL di Azure con un utilizzo DTU superiore al 90% per il periodo selezionato. Il riquadro superiore visualizza il numero di database che hanno utilizzato più del 90% della disponibilità DTU allocata totale durante lo stesso periodo specificato di tutti i database monitorati in Log Analytics.  Fare clic sul nome di un database per eseguire una ricerca log che visualizza un grafico a linee che confronta l'utilizzo DTU del database con tutti gli altri monitorati dall'area di lavoro. |
| Top N Databases by CPU Utilization &gt; 90% (Primi N database per utilizzo CPU > 90%) | Questo pannello visualizza il numero di database SQL di Azure con un utilizzo CPU superiore al 90% per il periodo selezionato. Il riquadro superiore visualizza il numero di database che hanno utilizzato più del 90% della disponibilità CPU allocata totale durante lo stesso periodo specificato di tutti i database monitorati in Log Analytics.  Fare clic sul nome di un database per eseguire una ricerca log che visualizza un grafico a linee che confronta l'utilizzo CPU del database con tutti gli altri monitorati dall'area di lavoro. |
| Top N Databases by Storage Consumption &gt; 90% (Primi N database per consumo della memoria > 90%) | Questo pannello visualizza il numero di database SQL di Azure che hanno utilizzato più del 90% dell'allocazione della memoria per il periodo selezionato. Il riquadro superiore visualizza il numero di database che hanno superato la soglia del 90% durante lo stesso periodo specificato di tutti i database monitorati in Log Analytics.  Fare clic sul nome di un database per eseguire una ricerca log che visualizza un grafico a linee che confronta il consumo della memoria del database con tutti gli altri monitorati dall'area di lavoro. |
| **Pool elastici SQL di Azure** | &nbsp; |
| Top N Elastic Pools by DTU &gt; 90% (Primi N pool elastici per DTU > 90%) | Questo pannello visualizza il numero di pool elastici SQL di Azure che hanno utilizzato più del 90% dell'allocazione DTU totale per il periodo selezionato. Il riquadro superiore visualizza il numero di pool elastici che hanno superato la soglia del 90% durante lo stesso periodo specificato di tutti i pool elastici SQL di Azure monitorati in Log Analytics.  Fare clic sul nome di un pool elastico per eseguire una ricerca log che visualizza un grafico a linee che confronta il consumo della memoria del pool elastico con tutti gli altri monitorati dall'area di lavoro. |
| Top N Elastic Pools by CPU &gt; 90% (Primi N pool elastici per CPU > 90%) | Questo pannello visualizza il numero di pool elastici SQL di Azure con un utilizzo CPU superiore al 90% per il periodo selezionato. Il riquadro superiore visualizza il numero di pool elastici che hanno superato la soglia del 90% durante lo stesso periodo specificato di tutti i pool elastici SQL di Azure monitorati in Log Analytics.  Fare clic sul nome di un pool elastico per eseguire una ricerca log che visualizza un grafico a linee che confronta l'utilizzo CPU del pool elastico con tutti gli altri monitorati dall'area di lavoro. |
| Top N Elastic Pools by Storage Consumption &gt; 90% (Primi N pool elastici per consumo della memoria > 90%) | Questo pannello visualizza il numero di pool elastici SQL di Azure che hanno utilizzato più del 90% dell'allocazione della memoria per il periodo selezionato. Il riquadro superiore visualizza il numero di pool elastici che hanno superato la soglia del 90% durante lo stesso periodo specificato di tutti i pool elastici monitorati in Log Analytics.  Fare clic sul nome di un pool elastico per eseguire una ricerca log che visualizza un grafico a linee che confronta il consumo della memoria del pool elastico con tutti gli altri monitorati dall'area di lavoro. |
| **Log attività di Azure SQL** | &nbsp; |
| SQL Azure Activity Audit (Audit delle attività di SQL Azure) | Questo pannello visualizza il numero di record di attività di Azure correlati ad Azure SQL per il periodo selezionato. Fare clic su un elemento per eseguire una ricerca log che visualizza dettagli aggiuntivi sull'elemento. |



### <a name="analyze-data-and-create-alerts"></a>Analizzare i dati e creare avvisi

La soluzione include query utili per l'analisi dei dati. Se si scorre verso destra, il dashboard elencherà diverse query comuni che è possibile selezionare per eseguire una [ricerca log](log-analytics-log-searches.md) per dati di Azure SQL.

![query](./media/log-analytics-azure-sql/azure-sql-queries.png)

La soluzione include alcune *query basate su avvisi*, come illustrato sopra, che è possibile usare per creare avvisi per soglie specifiche sia per i database SQL di Azure che per i pool elastici.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Per configurare un avviso per l'area di lavoro

1. Aprire il [portale di OMS](http://mms.microsoft.com/) e accedere.
2. Aprire l'area di lavoro configurata per la soluzione.
3. Nella pagina Panoramica fare clic sul riquadro **Azure SQL Analytics (anteprima)**.
4. Scorrere verso destra e fare clic su una query per iniziare a creare un avviso.  
![Query di avviso](./media/log-analytics-azure-sql/alert-query.png)
5. In Ricerca log fare clic su **Avviso**.  
![Creare un avviso nella ricerca](./media/log-analytics-azure-sql/create-alert01.png)
6. Nella pagina **Aggiungi regola di avviso** configurare le proprietà necessarie e le soglie specifiche desiderate e quindi fare clic su **Salva**.  
![Aggiungere una regola di avviso](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-monitoring-data"></a>Eseguire operazioni sui dati di monitoraggio di Azure SQL

Ad esempio, una delle query più utili che è possibile eseguire è confrontare l'utilizzo DTU per tutti i pool elastici SQL di Azure in tutte le sottoscrizioni di Azure. L'unità elaborata di database (DTU, Database Throughput Unit) consente di descrivere la capacità relativa di un livello delle prestazioni dei database e dei pool Basic, Standard e Premium. Le unità DTU sono basate su una misura combinata di CPU, memoria, operazioni di lettura e di scrittura. Quando le unità DTU aumentano, aumenta anche la potenza offerta dal livello delle prestazioni. Un livello delle prestazioni con 5 DTU, ad esempio, ha cinque volte la potenza di un livello delle prestazioni con 1 DTU. A ogni server e pool elastico viene applicata una quota massima di DTU.

Eseguendo la query di ricerca log seguente, è possibile capire facilmente se si sta sottoutilizzando o sovrautilizzando i pool elastici SQL Azure.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

Nell'esempio seguente è possibile osservare che un pool elastico ha un utilizzo elevato, prossimo al 100% dell'unità DTU, mentre gli altri hanno un utilizzo molto basso. È possibile eseguire un'indagine più approfondita per risolvere i problemi delle potenziali modifiche recenti dell'ambiente usando i log attività di Azure.

![Risultati della ricerca log: utilizzo elevato](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>Vedere anche

- Usare le [ricerche log](log-analytics-log-searches.md) in Log Analytics per visualizzare i dati dettagliati per Azure SQL.
- [Creare dashboard personalizzati](log-analytics-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](log-analytics-alerts.md) quando si verificano eventi specifici relativi ad Azure SQL.

