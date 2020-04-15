---
title: Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure
description: Informazioni su come monitorare la sincronizzazione dei dati SQL di Azure usando i log di Monitoraggio di AzureLearn how to monitor Azure SQL Data Sync by using Azure Monitor logs
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383696"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure 

Per controllare il registro attività di sincronizzazione dati SQL e rilevare gli errori e gli avvisi, in precedenza era necessario verificare manualmente la sincronizzazione dati SQL nel Portale di Azure o usare PowerShell o l'API REST. Per configurare una soluzione personalizzata che migliori l'esperienza di monitoraggio della sincronizzazione dei dati, seguire i passaggi descritti in questo articolo. È possibile personalizzare questa soluzione per adattarla allo scenario specifico.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> La sincronizzazione dati SQL di Azure **non** supporta l'istanza gestita del database SQL di Azure in questo momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Dashboard di monitoraggio per tutti i gruppi di sincronizzazione 

Per individuare i problemi, non è più necessario esaminare singolarmente i log di ogni gruppo di sincronizzazione. È possibile monitorare tutti i gruppi di sincronizzazione da qualsiasi sottoscrizione in un'unica posizione usando una visualizzazione personalizzata di Monitoraggio di Azure.You can monitor all your Sync Groups from any of your subscriptions in one place by using a custom Azure Monitor view. Questa vista evidenzia le informazioni importanti per i clienti di sincronizzazione dati SQL.

![Dashboard di monitoraggio della sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notifiche automatiche tramite posta elettronica

Non è più necessario controllare manualmente il log nel portale di Azure oppure tramite PowerShell o l'API REST. Con [i log](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)di Monitoraggio di Azure è possibile creare avvisi che vanno direttamente agli indirizzi di posta elettronica delle persone che devono visualizzarli quando si verifica un errore.

![Notifiche tramite e-mail sulla sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Come configurare queste funzioni di monitoraggio 

Implementare una soluzione di monitoraggio dei log di Monitoraggio di Azure personalizzata per la sincronizzazione dei dati SQL in meno di un'ora eseguendo le operazioni seguenti:Implement a custom Azure Monitor logs monitoring solution for SQL Data Sync in less than an hour by doing the following things:

È necessario configurare tre componenti:

-   Un runbook di PowerShell per inserire i dati del log di sincronizzazione dei dati SQL nei log di Monitoraggio di Azure.A PowerShell runbook to feed SQL Data Sync log data to Azure Monitor logs.

-   Un avviso di Monitoraggio di Azure per le notifiche tramite posta elettronica.

-   Visualizzazione di Monitoraggio di Azure per il monitoraggio.

### <a name="samples-to-download"></a>Campioni da scaricare

Scaricare i due esempi seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualizzazione monitoraggio di Azure della sincronizzazione datiData Sync Azure Monitor View](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Prerequisiti

Assicurarsi di avere configurato quanto segue:

-   Un account di Automazione di Azure

-   Area di lavoro Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook PowerShell per ottenere il log di sincronizzazione dati SQL 

Usare un runbook di PowerShell ospitato in Automazione di Azure per estrarre i dati del log di sincronizzazione dati SQL e inviarli ai log di Monitoraggio di Azure.Use a PowerShell runbook hosted in Azure Automation to pull the SQL Data Sync log data and send it to Azure Monitor logs. È incluso uno script di esempio. Come prerequisito è necessario disporre di un account di Automazione di Azure. È quindi necessario creare un runbook e pianificarne l'esecuzione. 

### <a name="create-a-runbook"></a>Creare un runbook

Per altre informazioni sulla creazione di un runbook, vedere [Il primo runbook PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Nell'account di Automazione di Azure selezionare la scheda**Runbook** in Automazione processi.

2.  Selezionare **Aggiungi runbook** nell'angolo superiore sinistro della pagina Runbook.

3.  Selezionare **Importare un runbook esistente**.

4.  In **File di runbook** usare il file `DataSyncLogPowerShellRunbook` specifico. Impostare il **tipo di runbook** come `PowerShell`. Assegnare un nome al runbook.

5.  Selezionare **Create** (Crea). È ora disponibile un runbook.

6.  In Account di Automazione di Azure selezionare la scheda**Variabili** in Risorse condivise.

7.  Selezionare **Aggiungi variabile** nella pagina Variabili. Creare una variabile per archiviare l'ora dell'ultima esecuzione del runbook. Se sono disponibili più runbook, è necessaria una variabile per ogni runbook.

8.  Impostare il nome della variabile come `DataSyncLogLastUpdatedTime` e impostarne il tipo come valore DateTime.

9.  Selezionare il runbook e fare clic sul pulsante di modifica nella parte superiore della pagina.

10. Apportare le modifiche necessarie per l'account e la configurazione della sincronizzazione dati SQL. Per informazioni più dettagliate, vedere lo script di esempio.

    1.  Informazioni su Azure.

    2.  Informazioni sul gruppo di sincronizzazione.

    3.  Azure Monitor registra le informazioni. Trovare queste informazioni nel portale di Azure | Impostazioni | Origini connesse. Per altre informazioni sull'invio di dati ai log di Monitoraggio di Azure, vedere [Inviare dati ai log di Monitoraggio di Azure con l'API agente di raccolta dati HTTP (anteprima).](../azure-monitor/platform/data-collector-api.md)

11. Eseguire il runbook nel riquadro di test. Assicurarsi che sia stato eseguito correttamente.

    Se sono presenti errori, verificare di avere installato il modulo PowerShell più recente. È possibile installare il modulo di PowerShell più recente in **Raccolta di moduli** nell'account di automazione di Azure.

12. Fare clic su **Pubblica**

### <a name="schedule-the-runbook"></a>Pianificare il runbook

Per pianificare il runbook:

1.  In Runbook selezionare la scheda **Pianificazioni** in Risorse.

2.  Selezionare **Aggiungi pianificazione** nella pagina Pianificazioni.

3.  Selezionare **Collega una pianificazione al runbook**.

4.  Selezionare **Crea una nuova pianificazione.**

5.  Impostare **Ricorrenza** su Ricorrente e impostare l'intervallo desiderato. Usare lo stesso intervallo qui, nello script e nei log di Monitoraggio di Azure.Use the same interval here, in the script, and in Azure Monitor logs.

6.  Selezionare **Create** (Crea).

### <a name="check-the-automation"></a>Controllare l'automazione

Per monitorare se l'automazione è in esecuzione come previsto, in **Anteprima** per l'account di automazione individuare la vista **Statistiche processi** in **Monitoraggio**. Aggiungere la vista al dashboard per maggiore comodità di visualizzazione. Le esecuzioni riuscite del runbook vengono indicate come completate, mentre quelle con errore vengono indicate come non riuscite.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Creare un avviso di Azure Monitor Reader per le notifiche tramite posta elettronicaCreate an Azure Monitor Reader Alert for Email Notifications

Per creare un avviso che usa i log di Monitoraggio di Azure, eseguire le operazioni seguenti. Come prerequisito, è necessario collegare i log di Monitoraggio di Azure a un'area di lavoro di Log Analytics.As a prerequisite, you need to have Azure Monitor logs linked with a Log Analytics workspace.

1.  Nel portale di Azure selezionare **Ricerca log**.

2.  Creare una query per selezionare gli errori e gli avvisi per gruppo di sincronizzazione entro l'intervallo selezionato. Ad esempio:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Dopo avere eseguito la query, selezionare l'icona a forma di campanello indicante **Avviso**.

4.  In **Genera l'avviso in base a** selezionare **Unità di misura della metrica**.

    1.  Impostare il valore di aggregazione **maggiore di**.

    2.  Dopo **maggiore di ** immettere la soglia che deve trascorrere prima di ricevere le notifiche. Sono previsti errori temporanei nella sincronizzazione dei dati. Per ridurre il rumore, impostare la soglia su 5.

5.  In **Azioni** impostare **Notifica di posta elettronica** su "Sì". Immettere i destinatari di posta elettronica desiderati.

6.  Fare clic su **Salva**. I destinatari specificati ora ricevono notifiche tramite e-mail in caso di errori.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Creare una visualizzazione di Monitoraggio di Azure per il monitoraggioCreate an Azure Monitor View for Monitoring

Questo passaggio consente di creare una visualizzazione di Monitoraggio di Azure per monitorare visivamente tutti i gruppi di sincronizzazione specificati. La vista include diversi componenti:

-   Un riquadro di panoramica che indica il numero di errori, le operazioni riuscite e gli avvisi di tutti i gruppi di sincronizzazione.

-   Un riquadro per tutti i gruppi di sincronizzazione indicante il conteggio di errori e avvisi per ogni gruppo di sincronizzazione. I gruppi senza problemi non vengono elencati in questo riquadro.

-   Un riquadro per ogni gruppo di sincronizzazione, indicante il numero di errori, le operazioni riuscite e gli avvisi, oltre ai messaggi di errore recenti.

Per configurare la visualizzazione Monitoraggio di Azure, eseguire le operazioni seguenti:To configure the Azure Monitor view, do the following things:

1.  Nella home page dell'area di lavoro di Log Analytics selezionare il segno più a sinistra per aprire **Progettazione viste.**

2.  Selezionare **Importa** nella barra superiore della finestra di progettazione viste. Selezionare quindi il file di esempio "DataSyncLogOMSView".

3.  La vista di esempio è relativa alla gestione di due gruppi di sincronizzazione. Modificare questa vista per adattarla allo scenario. Fare clic su **modifica** e apportare le modifiche seguenti:

    1.  Creare nuovi oggetti "Donut & List" dalla raccolta in base alle esigenze.

    2.  In ogni riquadro aggiornare le query con le informazioni necessarie.

        1.  In ogni riquadro modificare l'intervallo di TimeStamp_t nel modo desiderato.

        2.  Nei riquadri per ogni gruppo di sincronizzazione aggiornare i nomi dei gruppi di sincronizzazione.

    3.  In ogni riquadro aggiornare il titolo in base alle esigenze.

4.  Fare clic su **Salva** e la vista è pronta.

## <a name="cost-of-this-solution"></a>Costo di questa soluzione

Nella maggior parte dei casi questa soluzione è gratuita.

**Automazione di Azure:** è possibile che venga addebitato un costo con l'account di Automazione di Azure, in base all'utilizzo. I primi 500 minuti al mese del tempo di esecuzione processo sono gratuiti. Nella maggior parte dei casi l'utilizzo previsto per questa soluzione è inferiore a 500 minuti al mese. Per evitare costi, pianificare l'esecuzione del runbook a un intervallo di due o più ore. Per altre informazioni, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

**Log di Monitoraggio di Azure:Azure Monitor logs:** A seconda dell'utilizzo, potrebbe essere previsto un costo associato ai log di Monitoraggio di Azure.There may be a cost associated with Azure Monitor logs depending on your usage. Il livello gratuito include 500 MB di dati inseriti al giorno. Nella maggior parte dei casi l'inserimento previsto per questa soluzione è inferiore a 500 MB al mese. Per ridurre l'utilizzo, usare i filtri solo in base all'errore inclusi nel runbook. Se si usano più di 500 MB al giorno, eseguire l'aggiornamento al piano a pagamento per evitare il rischio di interruzione dell'analisi al raggiungimento del limite. Per altre informazioni, vedere Prezzi dei log di [Monitoraggio di Azure.For more](https://azure.microsoft.com/pricing/details/log-analytics/)info, see Azure Monitor logs pricing .

## <a name="code-samples"></a>Esempi di codice

Scaricare gli esempi di codice descritti in questo articolo dalle posizioni seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualizzazione monitoraggio di Azure della sincronizzazione datiData Sync Azure Monitor View](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Nel portale: [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale](sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-database-data-sync-agent.md)
-   Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   Risoluzione dei problemi: [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQLSQL Database Overview](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
