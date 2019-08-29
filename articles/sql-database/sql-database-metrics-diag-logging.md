---
title: Metriche del database SQL di Azure e registrazione diagnostica | Microsoft Docs
description: Informazioni su come abilitare la diagnostica nel database SQL di Azure per archiviare le informazioni sull'utilizzo delle risorse e le statistiche di esecuzione delle query.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/21/2019
ms.openlocfilehash: d9f1afdff53ada2df7722fcfdd7014fb6c417e39
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135184"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metriche del database SQL di Azure e registrazione diagnostica

In questo argomento si apprenderà come configurare la registrazione dei dati di telemetria di diagnostica per il database SQL di Azure tramite le portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di monitoraggio di Azure e Azure Resource Manager modello. Questi dati di diagnostica possono essere usati per misurare l'utilizzo delle risorse e le statistiche di esecuzione delle query.

I database singoli, i database in pool di pool elastici e i database dell'istanza in un'istanza gestita possono trasmettere log di diagnostica e metriche per facilitare il monitoraggio delle prestazioni. È possibile configurare un database per trasmettere l'utilizzo delle risorse, i ruoli di lavoro e le sessioni e connettività ad una delle seguenti risorse di Azure:

- **Analisi SQL di Azure**: per ottenere un monitoraggio intelligente dei database SQL di Azure che includa i report delle prestazioni, gli avvisi e i suggerimenti di mitigazione dei rischi.
- **Hub eventi di Azure**: per integrare i dati di telemetria del database SQL con soluzioni di monitoraggio personalizzate o pipeline attive.
- **Archiviazione di Azure**: per archiviare enormi quantità di dati di telemetria a un costo nettamente inferiore.

    ![Architettura](./media/sql-database-metrics-diag-logging/architecture.png)

Per altre informazioni sulle categorie di metriche e di log supportate dai vari servizi di Azure, vedere:

- [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Questo articolo fornisce indicazioni che consentono di abilitare la telemetria della diagnostica per i database SQL di Azure, i pool elastici e le istanze gestite. Può anche aiutare a configurare Analisi SQL di Azure come strumento di monitoraggio per la visualizzazione dei dati di telemetria diagnostica del database.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Abilitare la registrazione dei dati di telemetria diagnostica

È possibile abilitare e gestire le metriche e la registrazione dei dati di telemetria diagnostica usando uno dei metodi seguenti:

- Portale di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- API REST di Monitoraggio di Azure
- Modello di Azure Resource Manager

Quando si abilitano le metriche e la registrazione diagnostica, è necessario specificare la destinazione della risorsa di Azure per raccogliere i dati di telemetria di diagnostica. Le opzioni disponibili includono:

- Azure SQL Analytics
- Hub eventi di Azure
- Archiviazione di Azure

È possibile eseguire il provisioning di una nuova risorsa di Azure o selezionare una risorsa esistente. Dopo aver selezionato una risorsa, usando l'opzione delle **impostazioni di diagnostica**, specificare i dati da raccogliere.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Registrazione diagnostica supportata per i database SQL di Azure e i database dell'istanza

Abilitare le metriche e la registrazione diagnostica nei database SQL, visto che non sono abilitati per impostazione predefinita.

È possibile configurare i database SQL di Azure e i database dell'istanza per raccogliere i dati di telemetria di diagnostica seguenti:

| Dati di telemetria di monitoraggio per database | Supporto dei database singoli e in pool | Supporto del database dell'istanza |
| :------------------- | ----- | ----- |
| [Metriche di base](#basic-metrics): Contiene la percentuale DTU/CPU, il limite DTU/CPU, la percentuale di lettura dati fisici, la percentuale di scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, la percentuale delle sessioni, la percentuale dei ruoli di lavoro, la risorsa di archiviazione, la percentuale di archiviazione, la percentuale di archiviazione XTP. | Sì | No |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contiene le informazioni sulle statistiche di runtime delle query, ad esempio l'uso della CPU e le statistiche sulla durata delle query. | Sì | Sì |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contiene informazioni sulle statistiche di attesa delle query (su cosa sono attese le query), ad esempio CPU, LOG e blocco. | Sì | Sì |
| [Errori](#errors-dataset): Contiene informazioni sugli errori SQL in un database. | Sì | Sì |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Contiene informazioni sul tempo impiegato da un database per l'attesa di diversi tipi di attesa. | Sì | No |
| [Timeout](#time-outs-dataset): Contiene informazioni sui timeout di un database. | Sì | No |
| [Blocchi](#blockings-dataset): Contiene informazioni sul blocco di eventi in un database. | Sì | No |
| [Deadlock](#deadlocks-dataset): Contiene informazioni sugli eventi di deadlock in un database. | Sì | No |
| [AutomaticTuning](#automatic-tuning-dataset): Contiene informazioni sulle raccomandazioni di ottimizzazione automatica per un database. | Sì | No |
| [SQLInsights](#intelligent-insights-dataset): Contiene Intelligent Insights per le prestazioni di un database. Per altre informazioni, vedere [Intelligent Insights](sql-database-intelligent-insights.md). | Sì | Sì |

> [!IMPORTANT]
> I pool elastici e le istanze gestite presentano dati di telemetria di diagnostica distinti da quelli contenuti. È importante tenere presente che i dati di telemetria di diagnostica sono configurati separatamente per ognuna di queste risorse, come descritto di seguito.

> [!NOTE]
> I registri di sicurezza e SQLSecurityAuditEvents non possono essere abilitati dalle impostazioni di diagnostica del database (anche se visualizzate sullo schermo). Per abilitare lo streaming dei log di controllo, vedere [configurare il controllo per il database](sql-database-auditing.md#subheading-2)e [controllare i log nei log di monitoraggio di Azure e hub eventi di Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Portale di Azure

Per configurare la trasmissione dei dati di telemetria di diagnostica, è possibile usare il menu **impostazioni di diagnostica** per ogni database singolo, in pool o istanza in portale di Azure. Inoltre, la telemetria diagnostica può essere configurata separatamente per i contenitori di database: pool elastici e istanze gestite. È possibile impostare le destinazioni seguenti per trasmettere i dati di telemetria di diagnostica: Archiviazione di Azure, Hub eventi di Azure e log di monitoraggio di Azure.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurare il flusso di dati di telemetria di diagnostica per i pool elastici

   ![Icona del pool elastico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

È possibile impostare una risorsa di pool elastico per raccogliere i dati di telemetria di diagnostica seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Pool elastico** | Le [metriche di base](sql-database-metrics-diag-logging.md#basic-metrics) contengono la percentuale di EDTU/CPU, il limite di EDTU/CPU, la percentuale di lettura dei dati fisici, la percentuale di scrittura del log, la percentuale di sessioni, la percentuale di ruoli di lavoro, l'archiviazione, la percentuale di archiviazione, il limite di archiviazione |

Per configurare il flusso di dati di telemetria di diagnostica per i pool elastici e i database in pool elastici, è necessario configurare separatamente **entrambi** gli elementi seguenti:

- Abilitare la trasmissione dei dati di telemetria di diagnostica per un pool elastico **e**
- Abilitare la trasmissione della telemetria di diagnostica per ogni database in un pool elastico

Questo perché il pool elastico è un contenitore di database i cui dati di telemetria sono separati da singoli dati di telemetria.

Per abilitare il flusso di dati di telemetria della diagnostica per una risorsa di pool elastico, procedere come segue:

1. Passare alla risorsa del **pool elastico** in portale di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per log Analytics, selezionare **Configura** e crea una nuova area di lavoro selezionando **+ Crea nuova area**di lavoro oppure selezionare un'area di lavoro esistente.
1. Selezionare la casella di controllo per la telemetria di diagnostica del pool elastico: Metriche di **base** .
   ![Configurare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Selezionare **Salva**.
1. Inoltre, configurare la trasmissione dei dati di telemetria di diagnostica per ogni database all'interno del pool elastico che si vuole monitorare attenendosi alla procedura descritta nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare la telemetria di diagnostica per un pool elastico, è anche necessario configurare la telemetria di diagnostica per ogni database nel pool elastico, come descritto di seguito.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configurare la trasmissione di dati di telemetria di diagnostica per database singolo o database in un pool elastico

   ![Icona del database SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Per abilitare la trasmissione della telemetria di diagnostica per database singoli o in pool, seguire questa procedura:

1. Passare alla risorsa del **database SQL** di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre connessioni parallele per lo streaming dei dati di telemetria diagnostica.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database singoli, in pool o dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per l'esperienza di monitoraggio standard, basato su eventi, selezionare le caselle di controllo seguenti per la telemetria dei log di diagnostica del database: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errori**, **DatabaseWaitStatistics**, **Timeout**, **Blocchi** e **Deadlock**.
1. Per un'esperienza di monitoraggio avanzata basata su un minuto, selezionare la casella di controllo per le metriche di **base** .
   ![Configurare la diagnostica per database singoli, in pool o di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Selezionare **Salva**.
1. Ripetere questi passaggi per ogni database che si desidera monitorare.

> [!NOTE]
> Non è possibile abilitare il controllo di sicurezza e i log SQLSecurityAuditEvents dalle impostazioni di diagnostica del database, anche se visualizzate sullo schermo. Per abilitare lo streaming dei log di controllo, vedere [configurare il controllo per il database](sql-database-auditing.md#subheading-2)e [controllare i log nei log di monitoraggio di Azure e hub eventi di Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Ripetere questi passaggi per ogni database SQL di Azure che si desidera monitorare.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurare la trasmissione del flusso di dati di telemetria della diagnostica per le istanze gestite

   ![Icona dell'istanza gestita](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

È possibile impostare una risorsa di istanza gestita per raccogliere i dati di telemetria della diagnostica seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Istanza gestita** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per configurare il flusso di dati di telemetria di diagnostica per i database di istanze e istanze gestite, sarà necessario configurare separatamente **entrambi** gli elementi seguenti:

- Abilitare la trasmissione dei dati di telemetria di diagnostica per l'istanza gestita **e**
- Abilitare la trasmissione della telemetria di diagnostica per ogni database dell'istanza

Questo perché l'istanza gestita è un contenitore di database con i relativi dati di telemetria distinti da una singola telemetria del database dell'istanza.

Per abilitare la trasmissione del flusso di dati di telemetria della diagnostica per una risorsa di istanza gestita, seguire questa procedura:

1. Passare alla risorsa **istanza gestita** in portale di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per log Analytics, selezionare **Configura** e crea una nuova area di lavoro selezionando **+ Crea nuova area**di lavoro oppure usare un'area di lavoro esistente.
1. Selezionare la casella di controllo per la telemetria di diagnostica dell'istanza: **ResourceUsageStats**.
   ![Configurare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Selezionare **Salva**.
1. Inoltre, configurare la trasmissione dei dati di telemetria di diagnostica per ogni database di istanza all'interno dell'istanza gestita che si desidera monitorare attenendosi alla procedura descritta nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare la telemetria di diagnostica per un'istanza gestita, è necessario anche configurare la telemetria di diagnostica per ogni database dell'istanza, come descritto di seguito.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configurare il flusso di dati di telemetria di diagnostica per i database dell'istanza

   ![Icona del database dell'istanza nell'istanza gestita](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Per abilitare lo streaming dei dati di telemetria di diagnostica per i database di istanza, seguire questa procedura:

1. Passare alla risorsa **database dell'istanza** all'interno di istanza gestita.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre (3) connessioni parallele per lo streaming dei dati di telemetria diagnostica.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Selezionare le caselle di controllo per i dati di telemetria della diagnostica del database: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **Errori**.
   ![Configurare la diagnostica per i database dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Selezionare **Salva**.
1. Ripetere questi passaggi per ogni database di istanza che si desidera monitorare.

> [!TIP]
> Ripetere questi passaggi per ogni database di istanza che si desidera monitorare.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

È possibile abilitare le metriche e la registrazione diagnostica con PowerShell.

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Per consentire l'invio dei log di diagnostica a un'area di lavoro Log Analytics, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="to-configure-multiple-azure-resources"></a>Per configurare più risorse di Azure

Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell).

Specificare l'ID risorsa dell'area di lavoro \<$WSID\> come parametro quando si esegue lo script `Enable-AzureRMDiagnostics.ps1` per inviare i dati di diagnostica da più risorse all'area di lavoro.

- Per ottenere l'ID dell'area di lavoro \<$WSID\> della destinazione per i dati di diagnostica, usare lo script seguente:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Sostituire \<subID\> con l'ID abbonamento, \<RG_NAME\> con il nome del gruppo di risorse e \<WS_NAME\> con il nome dell'area di lavoro.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile abilitare le metriche e la registrazione diagnostica con l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Gli script per abilitare la registrazione diagnostica sono supportati per l'interfaccia della riga di comando di Azure versione 1.0. Si noti che la versione 2.0 dell'interfaccia della riga di comando non è supportata in questo momento.

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L'ID regola del bus di servizio è una stringa nel formato seguente:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Per consentire l'invio dei log di diagnostica a un'area di lavoro Log Analytics, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modello di Resource Manager

Informazioni su come [abilitare le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Streaming in Analisi SQL di Azure

Analisi SQL di Azure è una soluzione cloud che monitora le prestazioni dei database SQL di Azure, dei pool elastici e delle istanze gestite su larga scala e tra più abbonamenti. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.

![Panoramica di Analisi SQL di Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

I log di diagnostica e le metriche del database SQL possono essere trasmessi in Analisi SQL di Azure con l'opzione **Invia a Log Analytics** integrata nella scheda delle impostazioni di diagnostica nel portale. È anche possibile abilitare log Analytics usando un'impostazione di diagnostica tramite i cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di monitoraggio di Azure.

### <a name="installation-overview"></a>Panoramica dell'installazione

È possibile monitorare una flotta del database SQL con Analisi SQL di Azure. Eseguire i passaggi seguenti:

1. Creare una soluzione Analisi SQL di Azure da Azure Marketplace.
2. Creare un'area di lavoro di monitoraggio nella soluzione.
3. Configurare i database per lo streaming dei dati di telemetria diagnostica nell'area di lavoro.

Se si usano pool elastici o istanze gestite, è necessario anche configurare la trasmissione del flusso di dati di telemetria della diagnostica provenienti da queste risorse.

### <a name="create-azure-sql-analytics-resource"></a>Creare una risorsa di Analisi SQL di Azure

1. Cercare Analisi SQL di Azure in Azure Marketplace e selezionarla.

   ![Cercare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selezionare **Crea** nella schermata di panoramica della soluzione.

3. Compilare il modulo di Analisi SQL di Azure con le informazioni aggiuntive necessarie: nome dell'area di lavoro, sottoscrizione, gruppo di risorse, posizione e livello di prezzo.

   ![Configurare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selezionare **OK** per confermare, quindi **Crea**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurare i database per registrare le metriche e i log di diagnostica

Il modo più semplice per configurare la posizione in cui i database registrano le metriche consiste nell'usare il portale di Azure. Come descritto in precedenza, passare alla risorsa del database SQL nel portale di Azure e selezionare **Impostazioni di diagnostica**.

Se si usano pool elastici o istanze gestite, è necessario anche configurare le impostazioni di diagnostica in queste risorse per abilitare il flusso di dati di telemetria della diagnostica da trasmettere nell'area di lavoro.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Usare la soluzione analisi SQL per il monitoraggio e l'invio di avvisi

È possibile utilizzare SQL Analytics come un dashboard gerarchico per visualizzare le risorse del database SQL.

- Per informazioni su come usare la soluzione Analisi SQL, vedere [Monitorare un database SQL usando la soluzione Analisi SQL](../log-analytics/log-analytics-azure-sql.md).
- Per informazioni su come configurare gli avvisi per il database SQL e l'istanza gestita basata su SQL Analytics, vedere [creazione di avvisi per il database SQL e l'istanza gestita](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

È possibile trasmettere i log di diagnostica e le metriche del database SQL in hub eventi tramite l'opzione integrata **Trasmetti a un hub eventi** nel portale di Azure. È anche possibile abilitare l'ID regola del bus di servizio usando un'impostazione di diagnostica tramite i cmdlet PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Cosa fare con le metriche e i log di diagnostica in Hub eventi

Dopo aver eseguito lo streaming dei dati selezionati in Hub eventi, sarà possibile iniziare a valutare scenari di monitoraggio avanzati. Hub eventi funge da ingresso per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, potranno essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatore di archiviazione. Hub eventi separa la produzione di un flusso di eventi dal consumo di tali eventi. In questo modo, i consumer eventi possono accedere agli eventi in una pianificazione personalizzata. Per altre informazioni sugli hub eventi, vedere:

- [Che cos'è Hub eventi?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

È possibile usare le metriche tramesse in hub eventi per:

- **Visualizzare l'integrità del servizio mediante la trasmissione di dati sul "percorso critico" a Power BI**. Con Hub eventi, Analisi di flusso e Power BI è possibile trasformare facilmente i dati di metriche e diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per una panoramica della configurazione di un hub eventi, dell'elaborazione dei dati con Analisi di flusso e dell'uso di Power BI come output, vedere [Analisi di flusso e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Eseguire lo streaming dei log in stream di registrazione e telemetria di terze parti**. Usando lo streaming di Hub eventi è possibile trasmettere le metriche e i log di diagnostica a diverse soluzioni di monitoraggio e analisi dei log di terze parti.

- **Compilare una piattaforma di registrazione e telemetria personalizzata**. Si dispone già di una piattaforma di telemetria personalizzata o si intende crearne una? La natura altamente scalabile di pubblicazione-sottoscrizione dell'hub eventi offre flessibilità nell'inserimento dei log di diagnostica. [Vedere la guida all'uso dell'hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Streaming in Archiviazione

È possibile archiviare i log di diagnostica e le metriche del database SQL in Archiviazione di Azure usando l'opzione **Archivia in un account di archiviazione** integrata nel portale di Azure. È anche possibile abilitare Archiviazione usando un'impostazione di diagnostica tramite i cmdlet PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schema delle metriche e dei log di diagnostica nell'account di archiviazione

Dopo aver configurato la raccolta delle metriche e dei log di diagnostica, verrà creato un contenitore di archiviazione nell'account di archiviazione selezionato quando sono disponibili le prime righe di dati. La struttura dei BLOB è:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

O, più semplicemente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, un nome di BLOB per le metriche di base potrebbe essere:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Un nome di BLOB per archiviare i dati da un pool elastico è simile a:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Criteri di conservazione dei dati e prezzi

Se si seleziona Hub eventi o un account di archiviazione, è possibile specificare criteri di conservazione. Questi criteri eliminano i dati antecedenti a un periodo selezionato. Se si specifica Log Analytics, i criteri di conservazione dipendono dal piano tariffario selezionato. In questo caso, le unità gratuite fornite di inserimento dati possono consentire di monitorare gratuitamente più database ogni mese. Qualsiasi utilizzo dei dati di telemetria di diagnostica che superano le unità gratuite può generare dei costi. Si noti che i database attivi con carichi di lavoro più pesanti inseriscono più dati rispetto ai database inattivi. Per altre informazioni, vedere [prezzi di log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se si usa Analisi SQL di Azure, è possibile monitorare il consumo di inserimento dati nella soluzione selezionando **Area di lavoro di OMS** nel menu di spostamento di Analisi SQL di Azure e quindi scegliendo **Utilizzo** e **Costi stimati**.

## <a name="metrics-and-logs-available"></a>Le metriche e i log disponibili

Il monitoraggio della telemetria disponibile per il database SQL di Azure, i pool elastici e l'istanza gestita sono descritti di seguito. I dati di telemetria di monitoraggio raccolti all'interno di analisi SQL possono essere usati per lo sviluppo di applicazioni e analisi personalizzate usando il linguaggio di [query log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) .

## <a name="basic-metrics"></a>Metriche di base

Per informazioni dettagliate sulle metriche di base per risorsa, vedere le tabelle seguenti.

> [!NOTE]
> L'opzione metrica di base era nota in precedenza come tutte le metriche. La modifica apportata al solo nome e alla metrica monitorata non è stata modificata. Questa modifica è stata avviata per consentire l'introduzione di altre categorie di metriche in futuro.

### <a name="basic-metrics-for-elastic-pools"></a>Metriche di base per i pool elastici

|**Risorsa**|**Metriche**|
|---|---|
|Pool elastico|Percentuale eDTU, eDTU usata, limite eDTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Metriche di base per i database SQL di Azure

|**Risorsa**|**Metriche**|
|---|---|
|Database SQL di Azure|Percentuale DTU, DTU usata, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, deadlock |

## <a name="basic-logs"></a>Log di base

I dettagli dei dati di telemetria disponibili per tutti i log sono descritti nelle tabelle seguenti. Vedere [registrazione diagnostica supportata](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) per comprendere quali log sono supportati per una particolare versione del database: database SQL singolo, in pool o istanza di Azure.

### <a name="resource-usage-stats-for-managed-instance"></a>Statistiche sull'utilizzo delle risorse per l'istanza gestita

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: ResourceUsageStats |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome dell'istanza gestita |
|ResourceId|URI della risorsa |
|SKU_s|Codice di riferimento del prodotto dell'istanza gestita |
|virtual_core_count_s|Numero di vCore disponibili |
|avg_cpu_percent_s|Percentuale CPU Media |
|reserved_storage_mb_s|Capacità di archiviazione riservata nell'istanza gestita |
|storage_space_used_mb_s|Spazio di archiviazione usato nell'istanza gestita |
|io_requests_s|Numero IOPS |
|io_bytes_read_s|Byte IOPS letti |
|io_bytes_written_s|Byte di IOPS scritti |

### <a name="query-store-runtime-statistics"></a>Statistiche di runtime di Query Store

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome dell'operazione. Sempre: QueryStoreRuntimeStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|query_hash_s|Hash di query |
|query_plan_hash_s|Hash del piano di query |
|statement_sql_handle_s|Punto di controllo dell'istruzione SQL |
|interval_start_time_d|Datetimeoffset iniziale dell'intervallo in numero di tick dal 1900-1-1 |
|interval_end_time_d|Datetimeoffset finale dell'intervallo in numero di tick dal 1900-1-1 |
|logical_io_writes_d|Numero totale di scritture di I/O logiche |
|max_logical_io_writes_d|Numero massimo di scritture di I/O logiche per esecuzione |
|physical_io_reads_d|Numero totale di letture di I/O fisiche |
|max_physical_io_reads_d|Numero massimo letture di I/O logiche per esecuzione |
|logical_io_reads_d|Numero totale di letture di I/O logiche |
|max_logical_io_reads_d|Numero massimo letture di I/O logiche per esecuzione |
|execution_type_d|Tipo di esecuzione |
|count_executions_d|Numero di esecuzioni della query |
|cpu_time_d|Tempo totale della CPU usato dalla query in microsecondi |
|max_cpu_time_d|Consumer massimo del tempo di CPU di una singola esecuzione in microsecondi |
|dop_d|Somma dei gradi di parallelismo |
|max_dop_d|Massimo grado parallelismo usato per una singola esecuzione |
|rowcount_d|Numero di righe totali restituite |
|max_rowcount_d|Numero massimo di righe restituite in una singola esecuzione |
|query_max_used_memory_d|Quantità totale di memoria usata in KB |
|max_query_max_used_memory_d|Quantità massima di memoria usata da una singola esecuzione in KB |
|duration_d|Tempo di esecuzione totale in microsecondi |
|max_duration_d|Tempo massimo di esecuzione di una singola esecuzione |
|num_physical_io_reads_d|Numero totale di letture fisiche |
|max_num_physical_io_reads_d|Numero massimo di scritture fisiche per esecuzione |
|log_bytes_used_d|Quantità totale di byte di log usati |
|max_log_bytes_used_d|Quantità massima di byte di log usati per esecuzione |
|query_id_d|ID della query in Query Store |
|plan_id_d|ID del piano in Query Store |

Altre informazioni sui [dati delle statistiche di runtime di Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiche relative alle attese di Query Store

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome dell'operazione. Sempre: QueryStoreWaitStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|wait_category_s|Categoria dell'attesa |
|is_parameterizable_s|La query è parametrizzabile |
|statement_type_s|Tipo di istruzione |
|statement_key_hash_s|Hash della chiave di istruzione |
|exec_type_d|Tipo di esecuzione |
|total_query_wait_time_ms_d|Tempo di attesa totale della query nella categoria di attesa specifica |
|max_query_wait_time_ms_d|Tempo di attesa massimo della query nella singola esecuzione nella categoria di attesa specifica |
|query_param_type_d|0 |
|query_hash_s|Hash di query in Query Store |
|query_plan_hash_s|Hash del piano di query in Query Store |
|statement_sql_handle_s|Handle di istruzione in Query Store |
|interval_start_time_d|Datetimeoffset iniziale dell'intervallo in numero di tick dal 1900-1-1 |
|interval_end_time_d|Datetimeoffset finale dell'intervallo in numero di tick dal 1900-1-1 |
|count_executions_d|Numero di esecuzioni della query |
|query_id_d|ID della query in Query Store |
|plan_id_d|ID del piano in Query Store |

Altre informazioni sui [dati delle statistiche di attesa di Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Set di dati di errori

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: Errors |
|OperationName|Nome dell'operazione. Sempre: ErrorEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|Message|Messaggio di errore in testo normale |
|user_defined_b|È il bit di errore definito dall'utente |
|error_number_d|Codice di errore |
|Gravità|Gravità dell'errore |
|state_d|Stato dell'errore |
|query_hash_s|Hash di query della query non riuscita, se disponibile |
|query_plan_hash_s|Hash del piano di query della query non riuscita, se disponibile |

Altre informazioni sui [messaggi di errore di SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Set di dati delle statistiche di attesa del database

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome dell'operazione. Sempre: DatabaseWaitStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|wait_type_s|Nome del tipo di attesa |
|start_utc_date_t [UTC]|Ora di inizio del periodo misurato |
|end_utc_date_t [UTC]|Ora di fine del periodo misurato |
|delta_max_wait_time_ms_d|Tempo massimo tempo di attesa per esecuzione |
|delta_signal_wait_time_ms_d|Tempo di attesa totale dei segnali |
|delta_wait_time_ms_d|Tempo di attesa totale nel periodo |
|delta_waiting_tasks_count_d|Numero di attività in attesa |

Altre informazioni sulle [statistiche di attesa del database](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Set di dati dei timeout

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: Timeout |
|OperationName|Nome dell'operazione. Sempre: TimeoutEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|error_state_d|Codice di stato dell'errore |
|query_hash_s|Hash di query, se disponibile |
|query_plan_hash_s|Hash del piano di query, se disponibile |

### <a name="blockings-dataset"></a>Set di dati dei blocchi

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: Blocchi |
|OperationName|Nome dell'operazione. Sempre: BlockEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|lock_mode_s|Modalità di blocco usata dalla query |
|resource_owner_type_s|Proprietario del blocco |
|blocked_process_filtered_s|Report XML del processo bloccato |
|duration_d|Durata del blocco in microsecondi |

### <a name="deadlocks-dataset"></a>Set di dati di deadlock

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: Deadlock |
|OperationName|Nome dell'operazione. Sempre: DeadlockEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|deadlock_xml_s|XML del report di deadlock |

### <a name="automatic-tuning-dataset"></a>Set di dati di ottimizzazione automatica

|Proprietà|DESCRIZIONE|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT. SQL |
|Categoria|Nome della categoria. Sempre: AutomaticTuning |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Sempre: SERVER/DATABASE |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|LogicalDatabaseName_s|Nome del database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|RecommendationHash_s|Hash univoco del suggerimento di ottimizzazione automatica |
|OptionName_s|Operazione di ottimizzazione automatica |
|Schema_s|Schema del database |
|Table_s|Tabella interessata |
|IndexName_s|Nome dell'indice |
|IndexColumns_s|Nome colonna |
|IncludedColumns_s|Colonne incluse |
|EstimatedImpact_s|Impatto stimato del file JSON delle raccomandazioni di ottimizzazione automatica |
|Event_s|Tipo di evento di ottimizzazione automatica |
|Timestamp_t|Timestamp dell'ultimo aggiornamento |

### <a name="intelligent-insights-dataset"></a>Set di dati di Intelligent Insights

Altre informazioni sul [formato di log di Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare la registrazione e comprendere le categorie di metriche e di log supportate dai vari servizi di Azure, vedere:

- [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Per informazioni su Hub eventi, leggere:

- [Che cos'è l'hub di eventi di Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Per informazioni su come configurare gli avvisi in base ai dati di telemetria da log Analytics, vedere:

- [Creazione di avvisi per database SQL e istanza gestita](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
