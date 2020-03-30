---
title: Configurare l'esportazione di streaming delle metriche e dei log delle risorseConfigure streaming export of metrics and resource logs
description: Informazioni su come configurare l'esportazione di streaming di metriche e log delle risorse, inclusa l'analisi diagnostica intelligente dal database SQL di Azure alla destinazione scelta per archiviare le informazioni sull'utilizzo delle risorse e le statistiche di esecuzione delle query.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 3784b94a8571ab57d191d0bdb1e38aaa16d3cabb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255977"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Configurare l'esportazione di streaming dei dati di telemetria di diagnostica del database SQL di AzureConfigure streaming export of Azure SQL Database diagnostic telemetry

In questo articolo verranno fornite informazioni sulle metriche delle prestazioni e sui log delle risorse per il database SQL di Azure che è possibile esportare in una delle diverse destinazioni per l'analisi. Si apprenderà come configurare l'esportazione di streaming di questi dati di telemetria di diagnostica tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e i modelli di Azure Resource Manager.You will learn how to configure the streaming export of this diagnostic telemetry through the Azure portal, PowerShell, Azure CLI, the REST API, and Azure Resource Manager templates.

Si apprenderà anche le destinazioni a cui è possibile trasmettere questi dati di telemetria di diagnostica e come scegliere tra queste scelte. Le opzioni di destinazione includono:

- [Log Analytics and SQL Analytics](#stream-into-sql-analytics)
- [Hub eventi](#stream-into-event-hubs)
- [Archiviazione di AzureAzure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Telemetria di diagnostica per l'esportazione per il database SQL di AzureDiagnostic telemetry for export for Azure SQL Database

La cosa più importante tra i dati di telemetria di diagnostica che è possibile esportare è il log di Intelligent Insights (SQLInsights). [Intelligent Insights](sql-database-intelligent-insights.md) utilizza l'intelligenza integrata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare eventi di disturbo che causano una riduzione delle prestazioni. Una volta rilevato, viene eseguita un'analisi dettagliata che genera un log Intelligent Insights con una valutazione intelligente del problema. Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni. È necessario configurare l'esportazione di streaming di questo log per visualizzarne il contenuto.

Oltre a trasmettere l'esportazione del log di Intelligent Insights, è anche possibile esportare un'ampia gamma di metriche delle prestazioni e log del database SQL aggiuntivi. Nella tabella seguente vengono descritte le metriche delle prestazioni e i log delle risorse che è possibile configurare per l'esportazione in streaming in una delle diverse destinazioni. Questi dati di telemetria di diagnostica possono essere configurati per database singoli, pool elastici e database in pool, istanze gestite e database di istanza.

| Telemetria diagnostica per i databaseDiagnostic telemetry for databases | Supporto dei database singoli e in pool | Supporto del database dell'istanza gestita |
| :------------------- | ----- | ----- |
| [Metriche di base](#basic-metrics): Contiene la percentuale DTU/CPU, il limite DTU/CPU, la percentuale di lettura dei dati fisici, la percentuale di scrittura del registro, il successo/non riuscito/bloccato dalle connessioni firewall, la percentuale di sessioni, la percentuale di archiviazione, la percentuale di archiviazione e la percentuale di archiviazione XTP. | Sì | No |
| [Dati avanzati di](#advanced-metrics)istanza e app: contiene i dati del database di sistema tempdb e la dimensione del file di log e il file di log della percentuale tempdb utilizzato. | Sì | No |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contiene informazioni sulle statistiche di runtime delle query, ad esempio l'utilizzo della CPU e le statistiche sulla durata delle query. | Sì | Sì |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contiene informazioni sulle statistiche di attesa delle query (quali sono le query in attesa), ad esempio CPU, LOG e LOCKING. | Sì | Sì |
| [Errori](#errors-dataset): Contiene informazioni sugli errori SQL in un database. | Sì | Sì |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contiene informazioni sul tempo di attesa trascorso da un database per tipi di attesa diversi. | Sì | No |
| [Timeout:](#time-outs-dataset)contiene informazioni sui timeout in un database. | Sì | No |
| [Blocchi](#blockings-dataset): Contiene informazioni sul blocco degli eventi in un database. | Sì | No |
| [Deadlocks](#deadlocks-dataset): Contiene informazioni sugli eventi di deadlock in un database. | Sì | No |
| [AutomaticTuning](#automatic-tuning-dataset): Contiene informazioni sui consigli di ottimizzazione automatica per un database. | Sì | No |
| [SQLInsights](#intelligent-insights-dataset): contiene Intelligent Insights sulle prestazioni per un database. Per altre informazioni, vedere [Intelligent Insights](sql-database-intelligent-insights.md). | Sì | Sì |

> [!NOTE]
> Le impostazioni di diagnostica non possono essere configurate per i database di **sistema,** ad esempio i database master, msdb, model, resource e tempdb.

## <a name="streaming-export-destinations"></a>Streaming delle destinazioni di esportazione

Questa telemetria di diagnostica può essere trasmessa a una delle risorse di Azure seguenti per l'analisi.

- **[Area di lavoro di Log Analytics](#stream-into-sql-analytics)**:

  I dati trasmessi in [un'area](../azure-monitor/platform/resource-logs-collect-workspace.md) di lavoro di Log Analytics possono essere utilizzati da SQL [Analytics.](../azure-monitor/insights/azure-sql.md) Analisi SQL è una soluzione di monitoraggio solo cloud che fornisce un monitoraggio intelligente dei database che include report sulle prestazioni, avvisi e consigli di attenuazione. I dati trasmessi in un'area di lavoro di Log Analytics possono essere analizzati con altri dati di monitoraggio raccolti e consentono inoltre di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioniData streamed to a Log Analytics workspace can be analyzed with other monitoring data collected and also enables you to leverage other Azure Monitor features such as alerts and visualizations
- **[Hub eventi di Azure:](#stream-into-event-hubs)**

  I dati trasmessi in un hub eventi di Azure offrono le funzionalità seguenti:Data streamed to an [Azure Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md)provides the following functionality:

  - **Eseguire il flusso dei log**nei sistemi di registrazione e telemetria di terze parti: eseguire il flusso di tutte le metriche e i log delle risorse a un singolo hub eventi per reindirizzare i dati del log a uno strumento SIEM o di analisi dei log di terze parti.
  - Creare una piattaforma di **telemetria e registrazione personalizzata:** la natura altamente scalabile di pubblicazione e sottoscrizione degli hub eventi consente di inserire in modo flessibile metriche e log delle risorse in una piattaforma di telemetria personalizzata. Per informazioni dettagliate, vedere Progettazione e ridimensionamento di una piattaforma di [telemetria con scalabilità globale negli hub eventi di Azure.See Designing](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) and Sizing a Global Scale Telemetry Platform on Azure Event Hubs for details.
  - **Visualizzare l'integrità del servizio trasmettendo i dati in Power BI:** usare Hub eventi, Analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni dettagliate quasi in tempo reale nei servizi di Azure.View service health by streaming data to Power BI: Use Event Hubs, Stream Analytics, and Power BI to transform your diagnostics data into near real-time insights on your Azure services. Per informazioni dettagliate su questa soluzione, vedere [Analisi di flusso e Power BI: dashboard](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) di analisi in tempo reale per lo streaming dei dati.
- **[Archiviazione di Azure:](#stream-into-azure-storage)**

  I dati trasmessi in [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) consentono di archiviare grandi quantità di telemetria di diagnostica per una frazione del costo delle due opzioni di streaming precedenti.

Questo flusso di telemetria di diagnostica trasmesso a una di queste destinazioni può essere usato per misurare l'utilizzo delle risorse e le statistiche di esecuzione delle query per semplificare il monitoraggio delle prestazioni.

![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Abilitare e configurare l'esportazione di flusso dei dati di telemetria di diagnosticaEnable and configure the streaming export of diagnostic telemetry

È possibile abilitare e gestire le metriche e la registrazione dei dati di telemetria di diagnostica usando uno dei metodi seguenti:You can enable and manage metrics and diagnostic telemetry logging by using one of the following methods:

- Portale di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- API REST di Monitoraggio di Azure
- Modello di Azure Resource Manager

> [!NOTE]
> Per abilitare lo streaming dei log di controllo dei dati di telemetria di sicurezza, vedere [Configurare il controllo per il database](sql-database-auditing.md#subheading-2) e i log di controllo nei log di Monitoraggio di Azure e negli hub eventi di Azure.To enable audit log streaming of security telemetry, see Set up auditing for your database and [auditing logs in Azure Monitor logs and Azure Event Hubs.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Configurare l'esportazione di flusso dei dati di telemetria di diagnosticaConfigure the streaming export of diagnostic telemetry

È possibile usare il menu Impostazioni di **diagnostica** nel portale di Azure per abilitare e configurare lo streaming dei dati di telemetria di diagnostica. Inoltre, è possibile usare PowerShell, l'interfaccia della riga di comando di Azure, [l'API REST](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)e i modelli di [Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md) per configurare il flusso di telemetria di diagnostica. È possibile impostare le destinazioni seguenti per trasmettere i dati di telemetria di diagnostica: Archiviazione di Azure, Hub eventi di Azure e log di Monitoraggio di Azure.You can set the following destinations to stream the diagnostic telemetry: Azure Storage, Azure Event Hubs, and Azure Monitor logs.

> [!IMPORTANT]
> L'esportazione di streaming dei dati di telemetria di diagnostica non è abilitata per impostazione predefinita.

Selezionare una delle schede seguenti per istruzioni dettagliate per la configurazione dell'esportazione di flusso dei dati di telemetria di diagnostica nel portale di Azure e per gli script per eseguire lo stesso risultato con PowerShell e l'interfaccia della riga di comando di Azure.Select one of the following tabs for step-by-step guidance for configuring the streaming export of diagnostic telemetry in the Azure portal and for scripts for accomplishing the same with PowerShell and the Azure CLI.

# <a name="azure-portal"></a>[Portale di Azure](#tab/azure-portal)

### <a name="elastic-pools"></a>Pool elastici

È possibile configurare una risorsa del pool elastico per raccogliere i dati di telemetria di diagnostica seguenti:You can set up an elastic pool resource to collect the following diagnostic telemetry:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Piscina elastica** | [Le metriche di base](sql-database-metrics-diag-logging.md#basic-metrics) contengono la percentuale di eDTU/CPU, il limite eDTU/CPU, la percentuale di lettura dei dati fisici, la percentuale di scrittura del log, la percentuale di sessioni, la percentuale di lavoro, l'archiviazione, la percentuale di archiviazione, il limite di archiviazione e la percentuale di archiviazione XTP. |

Per configurare lo streaming dei dati di telemetria di diagnostica per i pool elastici e i database in pool, è necessario configurare separatamente ognuno di essi separatamente:To configure streaming of diagnostic telemetry for elastic pools and pooled databases, you need to separately configure each separately:

- Abilitare lo streaming dei dati di telemetria di diagnostica per un pool elasticoEnable streaming of diagnostic telemetry for an elastic pool
- Abilitare il flusso di telemetria di diagnostica per ogni database nel pool elasticoEnable streaming of diagnostic telemetry for each database in elastic pool

Il contenitore del pool elastico ha i propri dati di telemetria separati dai dati di telemetria di ogni singolo database in pool.

Per abilitare il flusso di telemetria di diagnostica per una risorsa del pool elastico, attenersi alla seguente procedura:

1. Passare alla risorsa **del pool elastico** nel portale di Azure.Go to the elastic pool resource in Azure portal.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per i dati di diagnostica del flusso: **Archivia nell'account di archiviazione,** **Flusso in un hub eventi**o Invia a Log **Analytics**.
6. Per l'analisi dei log, **selezionare Configura** e creare una nuova area di lavoro selezionando **Crea nuova area di lavoro**oppure selezionare un'area di lavoro esistente.
7. Selezionare la casella di controllo per la telemetria diagnostica del pool elastico: Metriche **di base.**
   ![Configurare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Selezionare **Salva**.
9. Configurare inoltre lo streaming dei dati di telemetria di diagnostica per ogni database all'interno del pool elastico che si vuole monitorare seguendo i passaggi descritti nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare i dati di telemetria di diagnostica per un pool elastico, è anche necessario configurare la telemetria di diagnostica per ogni database nel pool elastico.

### <a name="single-or-pooled-database"></a>Database singolo o in pool

È possibile configurare una risorsa di database singola o in pool per raccogliere la telemetria di diagnostica seguente:You can set up a single or pooled database resource to collect the following diagnostic telemetry:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Database singolo o in pool** | [Le metriche di base](sql-database-metrics-diag-logging.md#basic-metrics) contengono percentuale DTU, DTU utilizzato, limite DTU, percentuale di CPU, percentuale di lettura dei dati fisici, percentuale di scrittura del log, Riuscito/Non riuscito/Bloccato dalle connessioni firewall, percentuale di sessioni, percentuale di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP e deadlock. |

Per abilitare il flusso di dati di telemetria di diagnostica per un database singolo o in pool, attenersi alla seguente procedura:

1. Passare alla risorsa **di database SQL** di Azure.Go to Azure SQL database resource.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente. È possibile creare fino a tre connessioni parallele per trasmettere i dati di telemetria di diagnostica.
4. Selezionare **Aggiungi impostazione** di diagnostica per configurare il flusso parallelo dei dati di diagnostica su più risorse.

   ![Abilitare la diagnostica per database singoli e in poolEnable diagnostics for single and pooled databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Immettere un nome di impostazione per il proprio riferimento.
6. Selezionare una risorsa di destinazione per i dati di diagnostica del flusso: **Archivia nell'account di archiviazione,** **Flusso in un hub eventi**o Invia a Log **Analytics**.
7. Per l'esperienza di monitoraggio standard basata su eventi, selezionare le caselle di controllo seguenti per i dati di telemetria del log di diagnostica del database: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeout**, **Blocchi**e **Deadlock**.
8. Per un'esperienza di monitoraggio avanzata basata su un minuto, selezionare la casella di controllo Metriche **di base.**

   ![Configurare la diagnostica per i database singoli, in pool o dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Selezionare **Salva**.
10. Ripetere questi passaggi per ogni database che si desidera monitorare.

> [!TIP]
> Ripetere questi passaggi per ogni singolo database in pool che si desidera monitorare.

### <a name="managed-instance"></a>Istanza gestita

È possibile configurare una risorsa istanza gestita per raccogliere i dati di telemetria di diagnostica seguenti:You can set up a managed instance resource to collect the following diagnostic telemetry:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Istanza gestita** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per configurare lo streaming dei dati di telemetria di diagnostica per i database dell'istanza gestita e dell'istanza, è necessario configurare separatamente ognuno di essi:To configure streaming of diagnostic telemetry for managed instance databases, you will need to separately configure each:

- Abilitare lo streaming dei dati di telemetria di diagnostica per l'istanza gestitaEnable streaming of diagnostic telemetry for managed instance
- Abilitare il flusso di telemetria di diagnostica per ogni database dell'istanzaEnable streaming of diagnostic telemetry for each instance database

Il contenitore dell'istanza gestita ha i propri dati di telemetria separati dai dati di telemetria di ogni database dell'istanza.

Per abilitare il flusso di dati di telemetria di diagnostica per una risorsa dell'istanza gestita, eseguire la procedura seguente:To enable streaming of diagnostic telemetry for a managed instance resource, follow these steps:

1. Passare alla risorsa **dell'istanza gestita** nel portale di Azure.Go to the managed instance resource in Azure portal.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per i dati di diagnostica del flusso: **Archivia nell'account di archiviazione,** **Flusso in un hub eventi**o Invia a Log **Analytics**.
6. Per l'analisi dei log, **selezionare Configura** e creare una nuova area di lavoro selezionando **Crea nuova area di lavoro**oppure utilizzare un'area di lavoro esistente.
7. Selezionare la casella di controllo per la telemetria diagnostica dell'istanza: **ResourceUsageStats**.

   ![Configurare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Selezionare **Salva**.
9. Configurare inoltre il flusso di dati di telemetria di diagnostica per ogni database dell'istanza all'interno dell'istanza gestita che si vuole monitorare seguendo i passaggi descritti nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare i dati di telemetria di diagnostica per un'istanza gestita, è anche necessario configurare i dati di telemetria di diagnostica per ogni database dell'istanza.

### <a name="instance-database"></a>Database dell'istanza

È possibile impostare una risorsa di database dell'istanza per raccogliere i dati di telemetria di diagnostica seguenti:You can set up an instance database resource to collect the following diagnostic telemetry:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Database dell'istanza** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per abilitare il flusso di dati di telemetria di diagnostica per un database dell'istanza, attenersi alla seguente procedura:

1. Passare alla risorsa **del database dell'istanza** all'interno dell'istanza gestita.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre (3) connessioni parallele per trasmettere i dati di telemetria di diagnostica.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per i dati di diagnostica del flusso: **Archivia nell'account di archiviazione,** **Flusso in un hub eventi**o Invia a Log **Analytics**.
6. Selezionare le caselle di controllo per i dati di telemetria di diagnostica del database: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**e **Errori**.
   ![Configurare la diagnostica per i database dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Selezionare **Salva**.
8. Ripetere questi passaggi per ogni database dell'istanza che si desidera monitorare.

> [!TIP]
> Ripetere questi passaggi per ogni database dell'istanza che si desidera monitorare.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

È possibile abilitare le metriche e la registrazione diagnostica con PowerShell.

- Per abilitare l'archiviazione delle metriche e dei log delle risorse in un account di archiviazione, usare questo comando:To enable storage of metrics and resource logs in a storage account, use this command:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming di metriche e log delle risorse in un hub eventi, usare questo comando:To enable streaming of metrics and resource logs to an event hub, use this command:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Per abilitare l'invio di metriche e log delle risorse a un'area di lavoro di Log Analytics, usare questo comando:To enable sending metrics and resource logs to a Log Analytics workspace, use this command:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- È possibile ottenere l'ID risorsa dell'area di lavoro Log Analytics usando il comando seguente:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

È possibile combinare questi parametri per abilitare più opzioni di output.

**Per configurare più risorse di Azure**

Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell).

Specificare l'ID risorsa dell'area di lavoro \<$WSID\> come parametro quando si esegue lo script `Enable-AzureRMDiagnostics.ps1` per inviare i dati di diagnostica da più risorse all'area di lavoro.

- Per ottenere l'ID dell'area di lavoro \<$WSID\> della destinazione per i dati di diagnostica, usare lo script seguente:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Sostituire \<subID\> con l'ID abbonamento, \<RG_NAME\> con il nome del gruppo di risorse e \<WS_NAME\> con il nome dell'area di lavoro.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

È possibile abilitare le metriche e la registrazione diagnostica con l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Gli script per abilitare la registrazione diagnostica sono supportati per l'interfaccia della riga di comando di Azure v1.0.Scripts to enable diagnostics logging are supported for Azure CLI v1.0. L'interfaccia della riga di comando di Azure v2.0 non è attualmente supportata.

- Per abilitare l'archiviazione delle metriche e dei log delle risorse in un account di archiviazione, usare questo comando:To enable the storage of metrics and resource logs in a storage account, use this command:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare il flusso di metriche e log delle risorse in un hub eventi, usare questo comando:To enable the streaming of metrics and resource logs to an event hub, use this command:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  L'ID regola del bus di servizio è una stringa nel formato seguente:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Per abilitare l'invio di metriche e log delle risorse a un'area di lavoro di Log Analytics, usare questo comando:To enable the sending of metrics and resource logs to a Log Analytics workspace, use this command:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

È possibile combinare questi parametri per abilitare più opzioni di output.

---

## <a name="stream-into-sql-analytics"></a>Eseguire il flusso in Analisi SQL

SQL Database metrics and resource logs that are streamed into a Log Analytics workspace can be consumed by Azure SQL Analytics. Analisi SQL di Azure è una soluzione cloud che consente di monitorare le prestazioni di singoli database, pool elastici e database in pool, none ndola alle istanze gestite e ai database delle istanze su larga scala e tra più sottoscrizioni. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.

![Panoramica di Analisi SQL di Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Panoramica dell'installazione

È possibile monitorare una raccolta di database SQL di Azure con Analisi SQL di Azure eseguendo la procedura seguente:You can monitor a collection of Azure SQL databases with Azure SQL Analytics by performing the following steps:

1. Creare una soluzione Analisi SQL di Azure da Azure Marketplace.
2. Creare un'area di lavoro di Log Analytics nella soluzione.
3. Configurare i database per trasmettere i dati di telemetria di diagnostica nell'area di lavoro.

È possibile configurare l'esportazione di streaming di questi dati di telemetria di diagnostica usando l'opzione **predefinita Invia a Log Analytics** nella scheda Delle impostazioni di diagnostica nel portale di Azure.You can configure the streaming export of this diagnostic telemetry by using the built-in Send to Log Analytics option in the diagnostics settings tab in the Azure portal. È anche possibile abilitare lo streaming in un'area di lavoro di Log Analytics usando le impostazioni di diagnostica tramite i cmdlet di [PowerShell,](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry)l'interfaccia della riga di comando di [Azure,](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)i modelli REST API di [Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)o Resource [Manager.](../azure-monitor/platform/diagnostic-settings-template.md)

### <a name="create-an-azure-sql-analytics-resource"></a>Creare una risorsa di Azure SQL AnalyticsCreate an Azure SQL Analytics resource

1. Cercare Analisi SQL di Azure in Azure Marketplace e selezionarla.

   ![Cercare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selezionare **Crea** nella schermata di panoramica della soluzione.

3. Compilare il modulo di Analisi SQL di Azure con le informazioni aggiuntive necessarie: nome dell'area di lavoro, sottoscrizione, gruppo di risorse, posizione e livello di prezzo.

   ![Configurare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selezionare **OK** per confermare, quindi **Crea**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Configurare la risorsa per registrare metriche e log delle risorseConfigure the resource to record metrics and resource logs

È necessario configurare separatamente il flusso di telemetria di diagnostica per database singoli e in pool, pool elastici, istanze gestite e database di istanza. Il modo più semplice per configurare la posizione in cui una metrica dei record di risorse consiste nell'usare il portale di Azure.The easiest way to configure where a resource records metrics is by using the Azure portal. Per la procedura dettagliata, vedere [Configurare l'esportazione di flusso della telemetria di diagnostica.](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Usare Analisi SQL di Azure per il monitoraggio e gli avvisiUse Azure SQL Analytics for monitoring and alerting

È possibile usare SQL Analytics come dashboard gerarchico per visualizzare le risorse del database SQL.

- Per informazioni su come usare Analisi SQL di Azure, vedere [Monitorare il database SQL tramite SQL Analytics.](../log-analytics/log-analytics-azure-sql.md)
- Per informazioni su come impostare gli avvisi in SQL Analytics, vedere [Creazione di avvisi per database, pool elastici e istanze gestite.](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

È possibile trasmettere le metriche del database SQL e i log delle risorse negli hub eventi usando l'opzione Stream in un hub eventi incorporato nel portale di Azure.You can stream SQL Database metrics and resource logs into Event Hubs by using the built-in **Stream to an event hub** option in the Azure portal. È anche possibile abilitare l'ID regola del bus di servizio usando le impostazioni di diagnostica tramite i cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Azure Monitor.You also can enable the Service Bus rule ID by using diagnostics settings via PowerShell cmdlets, the Azure CLI, or the Azure Monitor REST API.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Cosa fare con le metriche e i log delle risorse negli hub eventi

Dopo aver eseguito lo streaming dei dati selezionati in Hub eventi, sarà possibile iniziare a valutare scenari di monitoraggio avanzati. Hub eventi funge da ingresso per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, potranno essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatore di archiviazione. Hub eventi separa la produzione di un flusso di eventi dal consumo di tali eventi. In questo modo, i consumer eventi possono accedere agli eventi in una pianificazione personalizzata. Per altre informazioni sugli hub eventi, vedere:

- [Che cosa sono gli hub eventi di Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

È possibile usare le metriche tramesse in hub eventi per:

- **Visualizzare l'integrità del servizio tramite lo streaming di dati hot path a Power BIView service health by streaming hot-path data to Power BI**

  Con Hub eventi, Analisi di flusso e Power BI è possibile trasformare facilmente i dati di metriche e diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per una panoramica della configurazione di un hub eventi, dell'elaborazione dei dati con Analisi di flusso e dell'uso di Power BI come output, vedere [Analisi di flusso e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Log di flusso nei flussi di telemetria e di registrazione di terze parti**

  Utilizzando lo streaming di Hub eventi, è possibile ottenere le metriche e i log delle risorse in varie soluzioni di monitoraggio e analisi dei log di terze parti.

- **Creare una piattaforma di telemetria e registrazione personalizzataBuild a custom telemetry and logging platform**

  Si dispone già di una piattaforma di telemetria personalizzata o si intende crearne una? La natura altamente scalabile di pubblicazione-sottoscrizione degli hub eventi consente di ingerire in modo flessibile le metriche e i log delle risorse. [Vedere la guida all'uso dell'hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Eseguire lo streaming in Archiviazione di Azure

È possibile archiviare metriche e log delle risorse in Archiviazione di Azure usando l'opzione **predefinita Archivio in un account di archiviazione** nel portale di Azure.You can store metrics and resource logs in Azure Storage by using the built-in Archive to a storage account option in the Azure portal. È anche possibile abilitare l'archiviazione usando le impostazioni di diagnostica tramite cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.You can also enable Storage by using diagnostics settings via PowerShell cmdlets, the Azure CLI, or the Azure Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema delle metriche e dei log delle risorse nell'account di archiviazioneSchema of metrics and resource logs in the storage account

Dopo aver configurato le metriche e la raccolta dei log delle risorse, viene creato un contenitore di archiviazione nell'account di archiviazione selezionato quando sono disponibili le prime righe di dati. La struttura dei BLOB è:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

O, più semplicemente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, un nome BLOB per le metriche di base potrebbe essere:For example, a blob name for Basic metrics might be:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Un nome di BLOB per archiviare i dati da un pool elastico è simile a:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Criteri di conservazione dei dati e prezzi

Se si seleziona Hub eventi o un account di archiviazione, è possibile specificare criteri di conservazione. Questi criteri eliminano i dati antecedenti a un periodo selezionato. Se si specifica Log Analytics, i criteri di conservazione dipendono dal piano tariffario selezionato. In questo caso, le unità gratuite fornite di inserimento dati possono consentire di monitorare gratuitamente più database ogni mese. Qualsiasi consumo di dati di telemetria di diagnostica superiore alle unità libere potrebbe comportare costi.

> [!IMPORTANT]
> I database attivi con carichi di lavoro più pesanti ingerino più dati rispetto ai database inattivi. Per ulteriori informazioni, vedere [Prezzi di analisi dei log](https://azure.microsoft.com/pricing/details/monitor/).

Se si usa Analisi SQL di Azure, è possibile monitorare l'utilizzo dell'inserimento dei dati selezionando Area di lavoro **OMS** nel menu di spostamento di Analisi SQL di Azure e quindi selezionando **Utilizzo** e **costi stimati**.

## <a name="metrics-and-logs-available"></a>Le metriche e i log disponibili

Il monitoraggio della telemetria disponibile per database singoli, database in pool, pool elastici, database dell'istanza gestita e dell'istanza è documentato in questa sezione dell'articolo. I dati di telemetria di monitoraggio raccolti all'interno di SQL Analytics possono essere usati per l'analisi personalizzata e lo sviluppo di applicazioni usando il linguaggio delle query di log di Monitoraggio di [Azure.Collected](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) monitoring telemetry inside SQL Analytics can be used for your own custom analysis and application development using Azure Monitor log queries language.

### <a name="basic-metrics"></a>Metriche di base

Fare riferimento alle tabelle seguenti per informazioni dettagliate sulle metriche di base in base alla risorsa.

> [!NOTE]
> L'opzione Metriche di base era precedentemente nota come Tutte le metriche. La modifica apportata è stata solo per la denominazione e non è stata apportata alcuna modifica alle metriche monitorate. Questa modifica è stata avviata per consentire l'introduzione di ulteriori categorie di metriche in futuro.

#### <a name="basic-metrics-for-elastic-pools"></a>Metriche di base per pool elastici

|**Risorsa**|**Metriche**|
|---|---|
|Pool elastico|Percentuale eDTU, eDTU usata, limite eDTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Metriche di base per database singoli e in pool

|**Risorsa**|**Metriche**|
|---|---|
|Database singolo e in pool|Percentuale DTU, DTU usata, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, deadlock |

### <a name="advanced-metrics"></a>Metriche avanzate

Fare riferimento alla tabella seguente per informazioni dettagliate sulle metriche avanzate.

|**Metrica**|**Nome visualizzato per la metrica**|**Descrizione**|
|---|---|---|
|tempdb_data_size| Kilobyte delle dimensioni del file di dati Tempdb |Dimensioni file di dati Tempdb Kilobyte. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 vCore e versioni successive o 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database di Hyperscale.This metric is not currently available for Hyperscale databases.|
|tempdb_log_size| Kilobyte delle dimensioni del file di registro di Tempdb |Kilobyte della dimensione del file di registro di Tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 vCore e versioni successive o 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database di Hyperscale.This metric is not currently available for Hyperscale databases.|
|tempdb_log_used_percent| Registro percentuale Tempdb utilizzato |Registro percentuale Tempdb utilizzato. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 vCore e versioni successive o 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database di Hyperscale.This metric is not currently available for Hyperscale databases.|

### <a name="basic-logs"></a>Registri di base

I dettagli dei dati di telemetria disponibili per tutti i log sono documentati nelle tabelle seguenti. Vedere telemetria di [diagnostica supportata](#diagnostic-telemetry-for-export-for-azure-sql-database) per comprendere quali log sono supportati per una determinata funzionalità di database: database singolo, in pool o dell'istanza di Azure.See supported diagnostic telemetry to understand which logs are supported for a particular database flavor - Azure SQL single, pooled, or instance database.

#### <a name="resource-usage-stats-for-managed-instances"></a>Statistiche sull'utilizzo delle risorse per le istanze gestiteResource usage stats for managed instances

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Sempre: ResourceUsageStats |
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

#### <a name="query-store-runtime-statistics"></a>Statistiche di runtime di Query Store

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: QueryStoreRuntimeStatistics |
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

Ulteriori informazioni sui dati delle statistiche di [runtime dell'archivio query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Statistiche relative alle attese di Query Store

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: QueryStoreWaitStatistics |
|OperationName|Nome dell'operazione. Always: QueryStoreWaitStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

#### <a name="errors-dataset"></a>Set di dati di errori

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: Errors |
|OperationName|Nome dell'operazione. Always: ErrorEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

Altre informazioni sui [messaggi di errore di SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Set di dati delle statistiche di attesa del database

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: DatabaseWaitStatistics |
|OperationName|Nome dell'operazione. Always: DatabaseWaitStatisticsEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

#### <a name="time-outs-dataset"></a>Set di dati dei timeout

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: Timeouts |
|OperationName|Nome dell'operazione. Always: TimeoutEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|error_state_d|Codice di stato dell'errore |
|query_hash_s|Hash di query, se disponibile |
|query_plan_hash_s|Hash del piano di query, se disponibile |

#### <a name="blockings-dataset"></a>Set di dati dei blocchi

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: Blocks |
|OperationName|Nome dell'operazione. Always: BlockEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

#### <a name="deadlocks-dataset"></a>Set di dati di deadlock

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC] |Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: Deadlocks |
|OperationName|Nome dell'operazione. Always: DeadlockEvent |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
|SubscriptionId|GUID dell'abbonamento per il database |
|ResourceGroup|Nome del gruppo di risorse per il database |
|LogicalServerName_s|Nome del server per il database |
|ElasticPoolName_s|Nome del pool elastico per il database, se presente |
|DatabaseName_s|Nome del database |
|ResourceId|URI della risorsa |
|deadlock_xml_s|XML del report di deadlock |

#### <a name="automatic-tuning-dataset"></a>Set di dati di ottimizzazione automatica

|Proprietà|Descrizione|
|---|---|
|TenantId|ID del tenant. |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log |
|Type|Always: AzureDiagnostics |
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL |
|Category|Nome della categoria. Always: AutomaticTuning |
|Risorsa|Nome della risorsa |
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES |
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

#### <a name="intelligent-insights-dataset"></a>Set di dati di Intelligent Insights

Altre informazioni sul [formato di log di Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare la registrazione e comprendere le categorie di metriche e di log supportate dai vari servizi di Azure, vedere:

- [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Panoramica dei log della piattaforma di Azure](../azure-monitor/platform/platform-logs-overview.md)

Per informazioni su Hub eventi, leggere:

- [Che cos'è l'hub di eventi di Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Per informazioni su come configurare gli avvisi in base ai dati di telemetria dall'analisi dei log, vedere:To learn how to set up alerts based on telemetry from log analytics see:

- [Creazione di avvisi per il database SQL e l'istanza gestitaCreating alerts for SQL Database and managed instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
