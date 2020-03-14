---
title: Configurare l'esportazione di flussi di metriche e log delle risorse
description: Informazioni su come configurare l'esportazione di flussi di metriche e log delle risorse, inclusa l'analisi diagnostica intelligente dal database SQL di Azure alla destinazione scelta per archiviare le informazioni sull'utilizzo delle risorse e le statistiche di esecuzione delle query.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255977"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Configurare l'esportazione di flussi di dati di telemetria diagnostica del database SQL di Azure

In questo articolo vengono illustrate le metriche delle prestazioni e i log delle risorse per il database SQL di Azure che è possibile esportare in una delle diverse destinazioni per l'analisi. Si apprenderà come configurare l'esportazione di flussi di dati di telemetria di diagnostica tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e i modelli Azure Resource Manager.

Si apprenderanno anche le destinazioni in cui è possibile trasmettere i dati di telemetria di diagnostica e come scegliere tra queste opzioni. Le opzioni di destinazione includono:

- [Log Analytics e analisi SQL](#stream-into-sql-analytics)
- [Hub eventi](#stream-into-event-hubs)
- [Archiviazione di Azure](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Telemetria diagnostica per l'esportazione per il database SQL di Azure

La più importante tra i dati di telemetria di diagnostica che è possibile esportare è il registro Intelligent Insights (sqlinsights). [Intelligent Insights](sql-database-intelligent-insights.md) utilizza l'Intelligence incorporata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare gli eventi che provocano una riduzione delle prestazioni. Una volta rilevata, viene eseguita un'analisi dettagliata che genera un log di Intelligent Insights con una valutazione intelligente del problema. Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni. Per visualizzare il contenuto, è necessario configurare l'esportazione del flusso di questo log.

Oltre a trasmettere l'esportazione del log di Intelligent Insights, è anche possibile esportare una serie di metriche delle prestazioni e log aggiuntivi del database SQL. La tabella seguente descrive le metriche delle prestazioni e i log delle risorse che è possibile configurare per l'esportazione in streaming in una delle diverse destinazioni. Questa telemetria diagnostica può essere configurata per database singoli, pool elastici e database in pool, nonché istanze gestite e database di istanza.

| Telemetria diagnostica per i database | Supporto dei database singoli e in pool | Supporto database istanza gestita |
| :------------------- | ----- | ----- |
| [Metriche di base](#basic-metrics): contiene DTU/percentuale CPU, limite DTU/CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, spazio di archiviazione, percentuale di archiviazione e percentuale di archiviazione XTP. | Sì | No |
| [Istanza e app avanzate](#advanced-metrics): contiene i dati del database di sistema tempdb e le dimensioni del file di log e il file di log percentuale del tempdb utilizzati. | Sì | No |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): contiene informazioni sulle statistiche di runtime delle query, ad esempio le statistiche di utilizzo della CPU e della durata delle query. | Sì | Sì |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): contiene informazioni sulle statistiche di attesa delle query, quali le query in attesa, come CPU, log e blocco. | Sì | Sì |
| [Errors](#errors-dataset): contiene informazioni sugli errori SQL in un database. | Sì | Sì |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contiene informazioni sul tempo di attesa trascorso da un database per tipi di attesa diversi. | Sì | No |
| [Timeout](#time-outs-dataset): contiene informazioni sui timeout in un database. | Sì | No |
| [Blocks](#blockings-dataset): contiene informazioni sul blocco di eventi in un database. | Sì | No |
| [Deadlocks](#deadlocks-dataset): contiene informazioni sugli eventi di deadlock in un database. | Sì | No |
| [AutomaticTuning](#automatic-tuning-dataset): contiene informazioni sulle raccomandazioni di ottimizzazione automatica per un database. | Sì | No |
| [Sqlinsights](#intelligent-insights-dataset): contiene Intelligent Insights le prestazioni di un database. Per altre informazioni, vedere [Intelligent Insights](sql-database-intelligent-insights.md). | Sì | Sì |

> [!NOTE]
> Non è possibile configurare le impostazioni di diagnostica per i **database di sistema**, ad esempio database master, msdb, Model, Resource e tempdb.

## <a name="streaming-export-destinations"></a>Destinazioni esportazione flussi

Questi dati di telemetria diagnostici possono essere trasmessi a una delle seguenti risorse di Azure per l'analisi.

- **[Log Analytics area di lavoro](#stream-into-sql-analytics)** :

  I dati trasmessi a un' [area di lavoro di log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) possono essere utilizzati da [analisi SQL](../azure-monitor/insights/azure-sql.md). Analisi SQL è una soluzione di monitoraggio solo cloud che consente di monitorare in modo intelligente i database che includono report sulle prestazioni, avvisi e consigli di mitigazione. I dati trasmessi a un'area di lavoro di Log Analytics possono essere analizzati con altri dati di monitoraggio raccolti e consentono anche di sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni
- **[Hub eventi di Azure](#stream-into-event-hubs)** :

  I dati trasmessi a un [Hub eventi di Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md)offrono le funzionalità seguenti:

  - **Trasmettere log a sistemi di registrazione e telemetria**di terze parti: trasmettere tutte le metriche e i log delle risorse a un singolo hub eventi per inviare tramite pipe i dati di log a uno strumento Siem o log Analytics di terze parti.
  - **Creare una piattaforma di registrazione e telemetria personalizzata**: la natura di pubblicazione-sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile le metriche e i log delle risorse in una piattaforma di telemetria personalizzata. Per informazioni dettagliate, vedere [progettazione e dimensionamento di una piattaforma di telemetria su scala globale in hub eventi di Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - Consente **di visualizzare l'integrità del servizio tramite lo streaming dei dati in Power bi**: usare hub eventi, analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per informazioni dettagliate su questa soluzione, vedere [analisi di flusso e Power bi: un dashboard di analisi in tempo reale per il flusso di dati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .
- **[Archiviazione di Azure](#stream-into-azure-storage)** :

  Il flusso di dati in [archiviazione di Azure](../azure-monitor/platform/resource-logs-collect-storage.md) consente di archiviare grandi quantità di dati di telemetria di diagnostica per una frazione del costo delle due opzioni di streaming precedenti.

Questa telemetria diagnostica trasmessa a una di queste destinazioni può essere usata per misurare l'utilizzo delle risorse e le statistiche di esecuzione delle query per semplificare il monitoraggio delle prestazioni.

![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Abilitare e configurare l'esportazione di flussi di dati di telemetria diagnostica

È possibile abilitare e gestire le metriche e la registrazione di dati di telemetria diagnostica usando uno dei metodi seguenti:

- Portale di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- API REST di Monitoraggio di Azure
- Modello di Azure Resource Manager

> [!NOTE]
> Per abilitare lo streaming dei log di controllo della telemetria di sicurezza, vedere [configurare il controllo per il database e i](sql-database-auditing.md#subheading-2) [log di controllo nei log di monitoraggio di Azure e in hub eventi di Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Configurare l'esportazione di flussi di dati di telemetria diagnostica

È possibile usare il menu **impostazioni di diagnostica** nel portale di Azure per abilitare e configurare la trasmissione dei dati di telemetria di diagnostica. Inoltre, è possibile usare PowerShell, l'interfaccia della riga di comando di Azure, l' [API REST](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)e [modelli di gestione risorse](../azure-monitor/platform/diagnostic-settings-template.md) per configurare il flusso di dati di telemetria diagnostici. È possibile impostare le destinazioni seguenti per trasmettere i dati di telemetria di diagnostica: archiviazione di Azure, Hub eventi di Azure e log di monitoraggio di Azure.

> [!IMPORTANT]
> L'esportazione di flussi di dati di telemetria diagnostici non è abilitata per impostazione predefinita.

Selezionare una delle schede seguenti per istruzioni dettagliate per la configurazione dell'esportazione in streaming dei dati di telemetria diagnostici nell'portale di Azure e per gli script per eseguire le stesse operazioni con PowerShell e l'interfaccia della riga di comando di Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

### <a name="elastic-pools"></a>Pool elastici

È possibile configurare una risorsa del pool elastico per raccogliere i dati di telemetria diagnostici seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Pool elastico** | Le [metriche di base](sql-database-metrics-diag-logging.md#basic-metrics) contengono la percentuale di EDTU/CPU, il limite di EDTU/CPU, la percentuale di lettura dei dati fisici, la percentuale di scrittura del log, la percentuale di sessioni, la percentuale di ruoli di lavoro, l'archiviazione, la percentuale di archiviazione, il limite di archiviazione |

Per configurare il flusso di dati di telemetria diagnostica per i pool elastici e i database in pool, è necessario configurare separatamente ciascuno separatamente:

- Abilitare la trasmissione dei dati di telemetria diagnostica per un pool elastico
- Abilitare la trasmissione della telemetria diagnostica per ogni database nel pool elastico

Il contenitore del pool elastico ha i propri dati di telemetria distinti rispetto ai dati di telemetria di ogni singolo database in pool.

Per abilitare lo streaming dei dati di telemetria di diagnostica per una risorsa del pool elastico, seguire questa procedura:

1. Passare alla risorsa del **pool elastico** in portale di Azure.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per il flusso dei dati di diagnostica: **archivia in un account di archiviazione**, trasmette **a un hub eventi**o **Invia a log Analytics**.
6. Per log Analytics, selezionare **Configura** e crea una nuova area di lavoro selezionando **+ Crea nuova area**di lavoro oppure selezionare un'area di lavoro esistente.
7. Selezionare la casella di controllo per la telemetria diagnostica del pool elastico: metriche di **base** .
   ![configurare la diagnostica per i pool elastici](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Selezionare **Salva**.
9. Inoltre, configurare la trasmissione dei dati di telemetria di diagnostica per ogni database all'interno del pool elastico che si vuole monitorare attenendosi alla procedura descritta nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare la telemetria diagnostica per un pool elastico, è anche necessario configurare la telemetria diagnostica per ogni database nel pool elastico.

### <a name="single-or-pooled-database"></a>Database singolo o in pool

È possibile configurare una risorsa di database singola o in pool per raccogliere i dati di telemetria diagnostici seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Database singolo o in pool** | Le [metriche di base](sql-database-metrics-diag-logging.md#basic-metrics) contengono la percentuale di DTU, il DTU usato, il limite di DTU, la percentuale di CPU, la percentuale di lettura dei dati fisici, la percentuale di scrittura del log, l'esito positivo/negativo/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, |

Per abilitare lo streaming dei dati di telemetria diagnostici per un database singolo o in pool, seguire questa procedura:

1. Passare alla risorsa del **database SQL** di Azure.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente. È possibile creare fino a tre connessioni parallele per eseguire il flusso di dati di telemetria diagnostici.
4. Selezionare **Aggiungi impostazioni di diagnostica** per configurare lo streaming parallelo dei dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per database singoli e in pool](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Immettere un nome di impostazione per il proprio riferimento.
6. Selezionare una risorsa di destinazione per il flusso dei dati di diagnostica: **archivia in un account di archiviazione**, trasmette **a un hub eventi**o **Invia a log Analytics**.
7. Per l'esperienza di monitoraggio standard basata su eventi, selezionare le caselle di controllo seguenti per la telemetria del log di diagnostica del database: **Sqlinsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **timeouts**, **Blocks**e **Deadlocks**.
8. Per un'esperienza di monitoraggio avanzata basata su un minuto, selezionare la casella di controllo per le metriche di **base** .

   ![Configurare la diagnostica per i database singoli, in pool o dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Selezionare **Salva**.
10. Ripetere questi passaggi per ogni database che si desidera monitorare.

> [!TIP]
> Ripetere questi passaggi per ogni database singolo e in pool che si desidera monitorare.

### <a name="managed-instance"></a>Istanza gestita

È possibile configurare una risorsa istanza gestita per raccogliere i dati di telemetria diagnostici seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Istanza gestita** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per configurare il flusso di dati di telemetria diagnostici per i database di istanza e istanza gestita, è necessario configurare separatamente ciascuno di essi:

- Abilitare lo streaming dei dati di telemetria di diagnostica per l'istanza gestita
- Abilitare lo streaming dei dati di telemetria diagnostici per ogni database dell'istanza

Il contenitore di istanze gestite ha i propri dati di telemetria distinti dalla telemetria del database di ogni istanza.

Per abilitare la trasmissione dei dati di telemetria di diagnostica per una risorsa istanza gestita, seguire questa procedura:

1. Passare alla risorsa **istanza gestita** in portale di Azure.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.

   ![Abilitare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per il flusso dei dati di diagnostica: **archivia in un account di archiviazione**, trasmette **a un hub eventi**o **Invia a log Analytics**.
6. Per log Analytics, selezionare **Configura** e crea una nuova area di lavoro selezionando **+ Crea nuova area**di lavoro oppure usare un'area di lavoro esistente.
7. Selezionare la casella di controllo per la telemetria diagnostica dell'istanza: **ResourceUsageStats**.

   ![Configurare la diagnostica per l'istanza gestita](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Selezionare **Salva**.
9. Inoltre, configurare la trasmissione dei dati di telemetria di diagnostica per ogni database di istanza all'interno dell'istanza gestita che si desidera monitorare attenendosi alla procedura descritta nella sezione successiva.

> [!IMPORTANT]
> Oltre a configurare la telemetria diagnostica per un'istanza gestita, è necessario anche configurare la telemetria diagnostica per ogni database dell'istanza.

### <a name="instance-database"></a>Database dell'istanza

È possibile configurare una risorsa di database dell'istanza per raccogliere i dati di telemetria diagnostici seguenti:

| Risorsa | Dati di telemetria di monitoraggio |
| :------------------- | ------------------- |
| **Database dell'istanza** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contiene il numero di vCore, la percentuale CPU media, le richieste IO, i byte letti/scritti, lo spazio di archiviazione riservato e lo spazio di archiviazione usato. |

Per abilitare lo streaming dei dati di telemetria diagnostici per un database di istanza, seguire questa procedura:

1. Passare alla risorsa **database dell'istanza** all'interno di istanza gestita.
2. Selezionare **Impostazioni di diagnostica**.
3. Selezionare **Abilita diagnostica** se non ci sono impostazioni precedenti oppure selezionare **Modifica l'impostazione** per modificare un'impostazione precedente.
   - È possibile creare fino a tre (3) connessioni parallele per eseguire il flusso di dati di telemetria diagnostici.
   - Selezionare **+Add diagnostic setting** (Aggiungi impostazione di diagnostica) per configurare flussi paralleli di dati di diagnostica in più risorse.

   ![Abilitare la diagnostica per i database dell'istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Immettere un nome di impostazione per il proprio riferimento.
5. Selezionare una risorsa di destinazione per il flusso dei dati di diagnostica: **archivia in un account di archiviazione**, trasmette **a un hub eventi**o **Invia a log Analytics**.
6. Selezionare le caselle di controllo per la telemetria diagnostica del database: **Sqlinsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**ed **Errors**.
   ![configurare la diagnostica per i database di istanza](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Selezionare **Salva**.
8. Ripetere questi passaggi per ogni database di istanza che si desidera monitorare.

> [!TIP]
> Ripetere questi passaggi per ogni database di istanza che si desidera monitorare.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

È possibile abilitare le metriche e la registrazione diagnostica con PowerShell.

- Per abilitare l'archiviazione di metriche e log delle risorse in un account di archiviazione, usare questo comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming di metriche e log delle risorse in un hub eventi, usare questo comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Per abilitare l'invio di metriche e log delle risorse a un'area di lavoro Log Analytics, usare questo comando:

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile abilitare le metriche e la registrazione diagnostica con l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Gli script per abilitare la registrazione diagnostica sono supportati per l'interfaccia della riga di comando di Azure versione 1.0. La versione 2.0 dell'interfaccia della riga di comando di Azure non è supportata in questo momento.

- Per abilitare l'archiviazione delle metriche e dei log delle risorse in un account di archiviazione, usare questo comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione di destinazione.

- Per abilitare lo streaming di metriche e log delle risorse in un hub eventi, usare questo comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  L'ID regola del bus di servizio è una stringa nel formato seguente:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Per consentire l'invio di metriche e log delle risorse a un'area di lavoro Log Analytics, usare questo comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

È possibile combinare questi parametri per abilitare più opzioni di output.

---

## <a name="stream-into-sql-analytics"></a>Flusso in analisi SQL

Le metriche e i log delle risorse del database SQL trasmessi in un'area di lavoro di Log Analytics possono essere utilizzati da Analisi SQL di Azure. Analisi SQL di Azure è una soluzione cloud che monitora le prestazioni di database singoli, pool elastici e database in pool, nonché istanze gestite e database di istanza su larga scala e tra più sottoscrizioni. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.

![Panoramica di Analisi SQL di Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Panoramica dell'installazione

È possibile monitorare una raccolta di database SQL di Azure con Analisi SQL di Azure attenendosi alla procedura seguente:

1. Creare una soluzione Analisi SQL di Azure da Azure Marketplace.
2. Creare un'area di lavoro Log Analytics nella soluzione.
3. Configurare i database per trasmettere i dati di telemetria diagnostici nell'area di lavoro.

È possibile configurare l'esportazione del flusso di questi dati di telemetria di diagnostica usando l'opzione **Invia a log Analytics** predefinita nella scheda Impostazioni di diagnostica della portale di Azure. È anche possibile abilitare lo streaming in un'area di lavoro Log Analytics usando le impostazioni di diagnostica tramite i [cmdlet di PowerShell](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), l'interfaccia della riga di comando di [Azure](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), l' [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)o i [modelli gestione risorse](../azure-monitor/platform/diagnostic-settings-template.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Creare una risorsa Analisi SQL di Azure

1. Cercare Analisi SQL di Azure in Azure Marketplace e selezionarla.

   ![Cercare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selezionare **Crea** nella schermata di panoramica della soluzione.

3. Compilare il modulo di Analisi SQL di Azure con le informazioni aggiuntive necessarie: nome dell'area di lavoro, sottoscrizione, gruppo di risorse, posizione e livello di prezzo.

   ![Configurare Analisi SQL di Azure nel portale](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selezionare **OK** per confermare, quindi **Crea**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Configurare la risorsa per registrare le metriche e i log delle risorse

È necessario configurare separatamente la trasmissione di dati di telemetria diagnostica per database singoli e in pool, pool elastici, istanze gestite e database di istanza. Il modo più semplice per configurare la posizione in cui una risorsa registra le metriche consiste nell'usare la portale di Azure. Per i passaggi dettagliati, vedere [configurare l'esportazione di flussi di dati di telemetria diagnostici](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Usare Analisi SQL di Azure per il monitoraggio e l'invio di avvisi

È possibile usare analisi SQL come dashboard gerarchico per visualizzare le risorse del database SQL.

- Per informazioni su come usare Analisi SQL di Azure, vedere [monitorare il database SQL tramite analisi SQL](../log-analytics/log-analytics-azure-sql.md).
- Per informazioni su come configurare gli avvisi per in SQL Analytics, vedere [creazione di avvisi per database, pool elastici e istanze gestite](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

È possibile trasmettere le metriche e i log delle risorse del database SQL in hub eventi usando il flusso predefinito in un'opzione di **Hub eventi** nel portale di Azure. È anche possibile abilitare l'ID regola del bus di servizio usando le impostazioni di diagnostica tramite i cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di monitoraggio di Azure.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Cosa fare con le metriche e i log delle risorse in hub eventi

Dopo aver eseguito lo streaming dei dati selezionati in Hub eventi, sarà possibile iniziare a valutare scenari di monitoraggio avanzati. Hub eventi funge da ingresso per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, potranno essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatore di archiviazione. Hub eventi separa la produzione di un flusso di eventi dal consumo di tali eventi. In questo modo, i consumer eventi possono accedere agli eventi in una pianificazione personalizzata. Per altre informazioni sugli hub eventi, vedere:

- [Che cos'è Hub eventi?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

È possibile usare le metriche tramesse in hub eventi per:

- **Visualizzare l'integrità del servizio tramite il flusso dei dati del percorso critico a Power BI**

  Con Hub eventi, Analisi di flusso e Power BI è possibile trasformare facilmente i dati di metriche e diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per una panoramica della configurazione di un hub eventi, dell'elaborazione dei dati con Analisi di flusso e dell'uso di Power BI come output, vedere [Analisi di flusso e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Trasmettere log a flussi di registrazione e telemetria di terze parti**

  Usando lo streaming di hub eventi, è possibile ottenere le metriche e i log delle risorse in diverse soluzioni di monitoraggio e analisi di log di terze parti.

- **Creare una piattaforma di registrazione e telemetria personalizzata**

  Si dispone già di una piattaforma di telemetria personalizzata o si intende crearne una? La natura di pubblicazione-sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile le metriche e i log delle risorse. [Vedere la guida all'uso dell'hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Eseguire lo streaming in Archiviazione di Azure

È possibile archiviare le metriche e i log delle risorse in archiviazione di Azure usando l'opzione **archivio predefinito in un account di archiviazione** nell'portale di Azure. È anche possibile abilitare l'archiviazione usando le impostazioni di diagnostica tramite i cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o l'API REST di monitoraggio di Azure.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema delle metriche e dei log delle risorse nell'account di archiviazione

Dopo aver configurato la raccolta di metriche e di log delle risorse, viene creato un contenitore di archiviazione nell'account di archiviazione selezionato quando sono disponibili le prime righe di dati. La struttura dei BLOB è:

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

Se si seleziona Hub eventi o un account di archiviazione, è possibile specificare criteri di conservazione. Questi criteri eliminano i dati antecedenti a un periodo selezionato. Se si specifica Log Analytics, i criteri di conservazione dipendono dal piano tariffario selezionato. In questo caso, le unità gratuite fornite di inserimento dati possono consentire di monitorare gratuitamente più database ogni mese. Qualsiasi consumo di dati di telemetria diagnostici in eccesso rispetto alle unità gratuite potrebbe comportare costi.

> [!IMPORTANT]
> I database attivi con carichi di lavoro più pesanti inseriscono più dati rispetto ai database inattivi. Per altre informazioni, vedere [prezzi di log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se si usa Analisi SQL di Azure, è possibile monitorare il consumo di inserimento dei dati selezionando **area di lavoro OMS** nel menu di navigazione di analisi SQL di Azure, quindi selezionando **utilizzo** e **costi stimati**.

## <a name="metrics-and-logs-available"></a>Le metriche e i log disponibili

Il monitoraggio della telemetria disponibile per database singoli, database in pool, pool elastici, istanza gestita e database di istanza è documentato in questa sezione dell'articolo. I dati di telemetria di monitoraggio raccolti all'interno di analisi SQL possono essere usati per lo sviluppo di applicazioni e analisi personalizzate usando il linguaggio di [query log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) .

### <a name="basic-metrics"></a>Metriche di base

Per informazioni dettagliate sulle metriche di base per risorsa, vedere le tabelle seguenti.

> [!NOTE]
> L'opzione metrica di base era nota in precedenza come tutte le metriche. La modifica apportata al solo nome e alla metrica monitorata non è stata modificata. Questa modifica è stata avviata per consentire l'introduzione di altre categorie di metriche in futuro.

#### <a name="basic-metrics-for-elastic-pools"></a>Metriche di base per i pool elastici

|**Risorsa**|**Metriche**|
|---|---|
|Pool elastico|Percentuale eDTU, eDTU usata, limite eDTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Metriche di base per database singoli e in pool

|**Risorsa**|**Metriche**|
|---|---|
|Database singolo e in pool|Percentuale DTU, DTU usata, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, deadlock |

### <a name="advanced-metrics"></a>Metriche avanzate

Per informazioni dettagliate sulle metriche avanzate, vedere la tabella seguente.

|**Metrica**|**Nome visualizzato per la metrica**|**Descrizione**|
|---|---|---|
|tempdb_data_size| Kilobyte dimensioni file di dati tempdb |Dimensioni del file di dati tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 Vcore e versioni successive oppure 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database con iperscalabilità.|
|tempdb_log_size| Kilobyte dimensioni file di log tempdb |Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 Vcore e versioni successive oppure 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database con iperscalabilità.|
|tempdb_log_used_percent| Log percentuale tempdb utilizzato |Log percentuale tempdb utilizzato. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore con 2 Vcore e versioni successive oppure 200 DTU e versioni successive per i modelli di acquisto basati su DTU. Questa metrica non è attualmente disponibile per i database con iperscalabilità.|

### <a name="basic-logs"></a>Log di base

I dettagli dei dati di telemetria disponibili per tutti i log sono descritti nelle tabelle seguenti. Vedere [telemetria diagnostica supportata](#diagnostic-telemetry-for-export-for-azure-sql-database) per comprendere quali log sono supportati per una particolare versione del database: database SQL singolo, in pool o istanza di Azure.

#### <a name="resource-usage-stats-for-managed-instances"></a>Statistiche sull'utilizzo delle risorse per le istanze gestite

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

Altre informazioni sui [dati delle statistiche di runtime di Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

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

Per informazioni su come configurare gli avvisi in base ai dati di telemetria da log Analytics, vedere:

- [Creazione di avvisi per database SQL e istanza gestita](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
