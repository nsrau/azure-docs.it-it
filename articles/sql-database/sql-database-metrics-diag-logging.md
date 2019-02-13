---
title: Metriche del database SQL di Azure e registrazione diagnostica | Microsoft Docs
description: Informazioni sulla configurazione del database SQL di Azure per archiviare le statistiche sull'uso delle risorse e sull'esecuzione delle query.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 24feef28edac73f625de1c1b7dfd9a4aaf9883af
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734624"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metriche del database SQL di Azure e registrazione diagnostica

I database singoli, i database in pool elastici e i database di istanza in un'istanza gestita possono trasmettere log di diagnostica e metriche per facilitare il monitoraggio delle prestazioni. È possibile configurare un database per trasmettere l'utilizzo delle risorse, i ruoli di lavoro e le sessioni e connettività ad una delle seguenti risorse di Azure:

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

Quando si abilitano le metriche e la registrazione diagnostica, è necessario specificare la risorsa di Azure di destinazione per la raccolta dei dati di telemetria della diagnostica. Le opzioni disponibili includono:

- Azure SQL Analytics
- Hub eventi di Azure
- Archiviazione di Azure

È possibile eseguire il provisioning di una nuova risorsa di Azure o selezionare una risorsa esistente. Dopo aver selezionato una risorsa, usando l'opzione delle **impostazioni di diagnostica**, specificare i dati da raccogliere.

> [!NOTE]
> Se si usano anche i pool elastici o un'istanza gestita, è consigliabile abilitare i dati di telemetria della diagnostica anche per queste risorse. I contenitori di database in pool elastici e un'istanza gestita hanno dati di telemetria della diagnostica separati.

## <a name="enable-logging-for-azure-sql-databases"></a>Abilitare la registrazione per i database SQL di Azure

Abilitare le metriche e la registrazione diagnostica nei database SQL, visto che non sono abilitati per impostazione predefinita.

È possibile impostare i database SQL di Azure per raccogliere i dati di telemetria della diagnostica seguenti:

| Dati di telemetria di monitoraggio per database | Supporto dei database singoli e in pool | Supporto dell'istanza gestita |
| :------------------- | ------------------- | ------------------- |
| [Tutte le metriche](sql-database-metrics-diag-logging.md#all-metrics): Contiene la percentuale DTU/CPU, il limite DTU/CPU, la percentuale di lettura dati fisici, la percentuale di scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, la percentuale delle sessioni, la percentuale dei ruoli di lavoro, la risorsa di archiviazione, la percentuale di archiviazione, la percentuale di archiviazione XTP. | Sì | No  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): Contiene le informazioni sulle statistiche di runtime delle query, ad esempio l'uso della CPU e le statistiche sulla durata delle query. | Sì | Sì |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): Contiene le informazioni sulle statistiche di attesa delle query (le cause di attesa delle query) ad esempio CPU, LOG o LOCKING. | Sì | Sì |
| [Errori](sql-database-metrics-diag-logging.md#errors-dataset): Contiene informazioni sugli errori di SQL nel database. | Sì | Sì |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): Contiene informazioni sul tempo di attesa trascorso dal database per tipi di attesa diversi. | Sì | No  |
| [Timeout](sql-database-metrics-diag-logging.md#time-outs-dataset): Contiene informazioni sui timeout nel database. | Sì | No  |
| [Blocchi](sql-database-metrics-diag-logging.md#blockings-dataset): Contiene informazioni sugli eventi di blocco nel database. | Sì | No  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): Contiene Intelligent Insights nelle prestazioni. Per altre informazioni, vedere [Intelligent Insights](sql-database-intelligent-insights.md). | Sì | Sì |

### <a name="azure-portal"></a>Portale di Azure

Usare il menu **Impostazioni di diagnostica** per ciascuno dei database singoli, in pool o di istanza nel portale di Azure per configurare il flusso di dati di telemetria della diagnostica per i database SQL di Azure. È possibile impostare le destinazioni seguenti: Archiviazione di Azure, Hub eventi di Azure e Analisi dei log di Azure.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-pooled-or-instance-databases"></a>Configurare la trasmissione del flusso di dati di telemetria della diagnostica per i database singoli, in pool o di istanza

   ![Icona del database SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Per abilitare la trasmissione del flusso di dati di telemetria della diagnostica per i database singoli, in pool o di istanza, seguire questa procedura:

1. Passare alla risorsa di database SQL di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre connessioni parallele per lo streaming dei dati di telemetria diagnostica.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database singoli, in pool o di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per l'esperienza di monitoraggio standard, basato su eventi, selezionare le caselle di controllo seguenti per la telemetria dei log di diagnostica del database: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errori**, **DatabaseWaitStatistics**, **Timeout**, **Blocchi** e **Deadlock**.
1. Per un'esperienza di monitoraggio avanzata, basata su 1 minuto, selezionare la casella di controllo **AllMetrics**.
1. Selezionare **Salva**.

   ![Configurare la diagnostica per i database singoli, in pool o di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Non è possibile abilitare i log dei controlli di sicurezza dalle impostazioni di diagnostica del database. Per abilitare la trasmissione dei log di controllo, vedere [Configurare il controllo per il database](sql-database-auditing.md#subheading-2) e [Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/) (Log di controllo in Azure Log Analytics e Hub eventi di Azure).
> [!TIP]
> Ripetere questi passaggi per ogni database SQL di Azure che si desidera monitorare.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases-in-managed-instance"></a>Configurare la trasmissione del flusso di dati di telemetria della diagnostica per i database di istanza nell'istanza gestita

   ![Icona del database di istanza nell'istanza gestita](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Per abilitare la trasmissione del flusso di dati di telemetria della diagnostica per i database di istanza nell'istanza gestita, seguire questa procedura:

1. Passare al database di istanza nell'istanza gestita.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre (3) connessioni parallele per lo streaming dei dati di telemetria diagnostica.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
6. Selezionare le caselle di controllo per i dati di telemetria della diagnostica del database: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **Errori**.
7. Selezionare **Salva**.

   ![Configurare la diagnostica per i database di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Ripetere questi passaggi per ogni istanza che si vuole monitorare.

## <a name="enable-logging-for-elastic-pools-or-managed-instances"></a>Abilitare la registrazione per i pool elastici o le istanze gestite

Abilitare i dati di telemetria della diagnostica per i pool elastici e le istanze gestite come contenitori di database. Hanno i propri dati di telemetria della diagnostica che non sono abilitati per impostazione predefinita.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurare il flusso di dati di telemetria di diagnostica per i pool elastici

   ![Icona del pool elastico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

È possibile impostare una risorsa di pool elastico per raccogliere i dati di telemetria di diagnostica seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Pool elastico** | [Tutte le metriche](sql-database-metrics-diag-logging.md#all-metrics) contiene percentuale eDTU/CPU, limite eDTU/CPU, limite eDTU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP. |

Per abilitare il flusso di dati di telemetria della diagnostica per una risorsa di pool elastico, procedere come segue:

1. Passare alla risorsa di pool elastico nel portale di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per Log Analytics, selezionare **Configura** e creare una nuova area di lavoro selezionando **+Crea nuova area di lavoro** oppure selezionarne una esistente.
1. Selezionare la casella di controllo per la telemetria di diagnostica del pool elastico: **AllMetrics**.
1. Selezionare **Salva**.

   ![Configurare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Ripetere questi passaggi per ogni pool elastico che si desidera monitorare.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurare la trasmissione del flusso di dati di telemetria della diagnostica per le istanze gestite

   ![Icona dell'istanza gestita](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

È possibile impostare una risorsa di istanza gestita per raccogliere i dati di telemetria della diagnostica seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Istanza gestita** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#logs-for-managed-instance) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per abilitare la trasmissione del flusso di dati di telemetria della diagnostica per una risorsa di istanza gestita, seguire questa procedura:

1. Passare alla risorsa di istanza gestita nel portale di Azure.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Immettere un nome di impostazione per il proprio riferimento.
1. Selezionare una risorsa di destinazione per i dati di diagnostica di streaming: **Archivia in un account di archiviazione**, **Trasmetti ad un hub eventi** oppure **Invia a Log Analytics**.
1. Per Log Analytics, selezionare **Configura** e creare una nuova area di lavoro selezionando **+Crea nuova area di lavoro** oppure usarne una esistente.
1. Selezionare la casella di controllo per la telemetria di diagnostica dell'istanza: **ResourceUsageStats**.
1. Selezionare **Salva**.

   ![Configurare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Ripetere questi passaggi per ogni istanza gestita che si vuole monitorare.

### <a name="powershell"></a>PowerShell

È possibile abilitare le metriche e la registrazione diagnostica con PowerShell.

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="to-configure-multiple-azure-resources"></a>Per configurare più risorse di Azure

Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell).

Specificare l'ID risorsa dell'area di lavoro \<$WSID\> come parametro quando si esegue lo script `Enable-AzureRMDiagnostics.ps1` per inviare i dati di diagnostica da più risorse all'area di lavoro.

- Per ottenere l'ID dell'area di lavoro \<$WSID\> della destinazione per i dati di diagnostica, usare lo script seguente:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Sostituire \<subID\> con l'ID abbonamento, \<RG_NAME\> con il nome del gruppo di risorse e \<WS_NAME\> con il nome dell'area di lavoro.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile abilitare le metriche e la registrazione diagnostica con l'interfaccia della riga di comando di Azure.

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

- Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

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

I log di diagnostica e le metriche del database SQL possono essere trasmessi in Analisi SQL di Azure con l'opzione **Invia a Log Analytics** integrata nella scheda delle impostazioni di diagnostica nel portale. È anche possibile abilitare Log Analytics usando un'impostazione di diagnostica tramite i cmdlet PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

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

Il modo più semplice per configurare la posizione in cui i database registrano le metriche è con il portale di Azure. Come descritto in precedenza, passare alla risorsa del database SQL nel portale di Azure e selezionare **Impostazioni di diagnostica**.

Se si usano pool elastici o istanze gestite, è necessario anche configurare le impostazioni di diagnostica in queste risorse per abilitare il flusso di dati di telemetria della diagnostica da trasmettere nell'area di lavoro.

### <a name="use-the-sql-analytics-solution"></a>Usare la soluzione Analisi SQL

È possibile utilizzare SQL Analytics come un dashboard gerarchico per visualizzare le risorse del database SQL. Per informazioni su come usare la soluzione Analisi SQL, vedere [Monitorare un database SQL usando la soluzione Analisi SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

È possibile trasmettere i log di diagnostica e le metriche del database SQL in hub eventi tramite l'opzione integrata **Trasmetti a un hub eventi** nel portale di Azure. È anche possibile abilitare l'ID regola del bus di servizio usando un'impostazione di diagnostica tramite i cmdlet PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Cosa fare con le metriche e i log di diagnostica in Hub eventi

Dopo aver eseguito lo streaming dei dati selezionati in Hub eventi, sarà possibile iniziare a valutare scenari di monitoraggio avanzati. Hub eventi funge da ingresso per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, potranno essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatore di archiviazione. Hub eventi separa la produzione di un flusso di eventi dal consumo di tali eventi. In questo modo, i consumer eventi possono accedere agli eventi in una pianificazione personalizzata. Per altre informazioni sugli hub eventi, vedere:

- [Che cos'è Hub eventi?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

È possibile usare le metriche tramesse in hub eventi per:

- **Visualizzare l'integrità del servizio mediante la trasmissione di dati sul "percorso critico" a Power BI**. Con Hub eventi, Analisi di flusso e Power BI è possibile trasformare facilmente i dati di metriche e diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per una panoramica della configurazione di un hub eventi, dell'elaborazione dei dati con Analisi di flusso e dell'uso di Power BI come output, vedere [Analisi di flusso e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Eseguire lo streaming dei log in stream di registrazione e telemetria di terze parti**. Usando lo streaming di Hub eventi è possibile trasmettere le metriche e i log di diagnostica a diverse soluzioni di monitoraggio e analisi di log di terze parti.

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

Ad esempio, un nome del BLOB per l'opzione Tutte le metriche potrebbe essere:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Un nome di BLOB per archiviare i dati da un pool elastico è simile a:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Scaricare le metriche e i log da Archiviazione

Informazioni su come [scaricare le metriche e i log di diagnostica da Archiviazione](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Criteri di conservazione dei dati e prezzi

Se si seleziona Hub eventi o un account di archiviazione, è possibile specificare criteri di conservazione. Questi criteri eliminano i dati antecedenti a un periodo selezionato. Se si specifica Log Analytics, i criteri di conservazione dipendono dal piano tariffario selezionato. In questo caso, le unità gratuite fornite di inserimento dati possono consentire di monitorare gratuitamente più database ogni mese. Qualsiasi utilizzo dei dati di telemetria di diagnostica che superano le unità gratuite può generare dei costi. Si noti che i database attivi con carichi di lavoro più pesanti inseriscono più dati rispetto ai database inattivi. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se si usa Analisi SQL di Azure, è possibile monitorare il consumo di inserimento dati nella soluzione selezionando **Area di lavoro di OMS** nel menu di spostamento di Analisi SQL di Azure e quindi scegliendo **Utilizzo** e **Costi stimati**.

## <a name="metrics-and-logs-available"></a>Le metriche e i log disponibili

I dati di telemetria di monitoraggio raccolti possono essere usati per _l'analisi personalizzata_ e _lo sviluppo di applicazioni_ mediante il [linguaggio di Analisi SQL](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="all-metrics"></a>Tutte le metriche

Fare riferimento alle tabelle seguenti per informazioni dettagliate su tutte le metriche dalle risorse.

### <a name="all-metrics-for-elastic-pools"></a>Tutte le metriche per i pool elastici

|**Risorsa**|**Metriche**|
|---|---|
|Pool elastico|Percentuale eDTU, eDTU usata, limite eDTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Tutti le metriche per i database SQL di Azure

|**Risorsa**|**Metriche**|
|---|---|
|Database SQL di Azure|Percentuale DTU, DTU usata, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, deadlock |

## <a name="logs-for-managed-instances"></a>Log per le istanze gestite

Fare riferimento alla tabella seguente per informazioni dettagliate sui log per le istanze gestite.

### <a name="resource-usage-statistics"></a>Statistiche di utilizzo delle risorse

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

## <a name="logs-for-single-pooled-and-instance-databases"></a>Log per i database singoli, in pool e di istanza

Fare riferimento alle tabelle seguenti per informazioni dettagliate sui log per i database SQL di Azure singoli, in pool e di istanza.

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
|ResourceProvider|Nome del provider di risorse. Sempre: MICROSOFT.SQ |
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

Per informazioni su Archiviazione di Azure, leggere come [scaricare le metriche e i log di diagnostica da Archiviazione](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
