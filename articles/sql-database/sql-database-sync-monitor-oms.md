---
title: Monitorare sincronizzazione dati SQL con i log di monitoraggio di Azure
description: Informazioni su come monitorare sincronizzazione dati SQL di Azure usando i log di monitoraggio di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114612"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorare sincronizzazione dati SQL con i log di monitoraggio di Azure 

Per controllare il registro attività di sincronizzazione dati SQL e rilevare gli errori e gli avvisi, in precedenza era necessario verificare manualmente la sincronizzazione dati SQL nel Portale di Azure o usare PowerShell o l'API REST. Per configurare una soluzione personalizzata che migliori l'esperienza di monitoraggio della sincronizzazione dei dati, seguire i passaggi descritti in questo articolo. È possibile personalizzare questa soluzione per adattarla allo scenario specifico.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Al momento la sincronizzazione dati SQL di Azure **non** supporta Istanza gestita di database SQL di Azure.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Dashboard di monitoraggio per tutti i gruppi di sincronizzazione 

Per individuare i problemi, non è più necessario esaminare singolarmente i log di ogni gruppo di sincronizzazione. È possibile monitorare tutti i gruppi di sincronizzazione da una qualsiasi delle sottoscrizioni in un'unica posizione usando una visualizzazione personalizzata di monitoraggio di Azure. Questa vista evidenzia le informazioni importanti per i clienti di sincronizzazione dati SQL.

![Dashboard di monitoraggio della sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notifiche automatiche tramite posta elettronica

Non è più necessario controllare manualmente il log nel portale di Azure oppure tramite PowerShell o l'API REST. Con i [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)è possibile creare avvisi che passano direttamente agli indirizzi di posta elettronica delle persone che devono visualizzarli quando si verifica un errore.

![Notifiche tramite e-mail sulla sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Come configurare queste funzioni di monitoraggio 

Implementare una soluzione di monitoraggio dei log di monitoraggio di Azure personalizzata per sincronizzazione dati SQL in meno di un'ora eseguendo le operazioni seguenti:

È necessario configurare tre componenti:

-   Un Runbook di PowerShell per il feed sincronizzazione dati SQL i dati di log nei log di monitoraggio di Azure.

-   Avviso di monitoraggio di Azure per le notifiche di posta elettronica.

-   Una vista di monitoraggio di Azure per il monitoraggio.

### <a name="samples-to-download"></a>Campioni da scaricare

Scaricare i due esempi seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualizzazione monitoraggio di Azure per la sincronizzazione dei dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>prerequisiti

Assicurarsi di avere configurato quanto segue:

-   Un account di Automazione di Azure

-   Area di lavoro Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook PowerShell per ottenere il log di sincronizzazione dati SQL 

Usare un Runbook di PowerShell ospitato in automazione di Azure per eseguire il pull dei dati di log sincronizzazione dati SQL e inviarli ai log di monitoraggio di Azure. È incluso uno script di esempio. Come prerequisito è necessario disporre di un account di Automazione di Azure. È quindi necessario creare un runbook e pianificarne l'esecuzione. 

### <a name="create-a-runbook"></a>Creare un runbook

Per altre informazioni sulla creazione di un runbook, vedere [Il primo runbook PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Nell'account di Automazione di Azure selezionare la scheda**Runbook** in Automazione processi.

2.  Selezionare **Aggiungi runbook** nell'angolo superiore sinistro della pagina Runbook.

3.  Selezionare **Importare un runbook esistente**.

4.  In **File di runbook** usare il file `DataSyncLogPowerShellRunbook` specifico. Impostare il **tipo di runbook** come `PowerShell`. Assegnare un nome al runbook.

5.  Selezionare **Create**. È ora disponibile un runbook.

6.  In Account di Automazione di Azure selezionare la scheda**Variabili** in Risorse condivise.

7.  Selezionare **Aggiungi variabile** nella pagina Variabili. Creare una variabile per archiviare l'ora dell'ultima esecuzione del runbook. Se sono disponibili più runbook, è necessaria una variabile per ogni runbook.

8.  Impostare il nome della variabile come `DataSyncLogLastUpdatedTime` e impostarne il tipo come valore DateTime.

9.  Selezionare il runbook e fare clic sul pulsante di modifica nella parte superiore della pagina.

10. Apportare le modifiche necessarie per l'account e la configurazione della sincronizzazione dati SQL. Per informazioni più dettagliate, vedere lo script di esempio.

    1.  Informazioni su Azure.

    2.  Informazioni sul gruppo di sincronizzazione.

    3.  Il monitoraggio di Azure registra le informazioni. Trovare queste informazioni nel portale di Azure | Impostazioni | Origini connesse. Per altre informazioni sull'invio di dati ai log di monitoraggio di Azure, vedere [inviare dati ai log di monitoraggio di Azure con l'API dell'agente di raccolta dati http (anteprima)](../azure-monitor/platform/data-collector-api.md).

11. Eseguire il runbook nel riquadro di test. Assicurarsi che sia stato eseguito correttamente.

    Se sono presenti errori, verificare di avere installato il modulo PowerShell più recente. È possibile installare il modulo di PowerShell più recente in **Raccolta di moduli** nell'account di automazione di Azure.

12. Fare clic su **Pubblica**.

### <a name="schedule-the-runbook"></a>Pianificare il runbook

Per pianificare il runbook:

1.  In Runbook selezionare la scheda **Pianificazioni** in Risorse.

2.  Selezionare **Aggiungi pianificazione** nella pagina Pianificazioni.

3.  Selezionare **Collegare una pianificazione al runbook**.

4.  Selezionare quindi **Crea una nuova pianificazione**.

5.  Impostare **Ricorrenza** su Ricorrente e impostare l'intervallo desiderato. Usare lo stesso intervallo qui, nello script e nei log di monitoraggio di Azure.

6.  Selezionare **Create**.

### <a name="check-the-automation"></a>Controllare l'automazione

Per monitorare se l'automazione è in esecuzione come previsto, in **Anteprima** per l'account di automazione individuare la vista **Statistiche processi** in **Monitoraggio**. Aggiungere la vista al dashboard per maggiore comodità di visualizzazione. Le esecuzioni riuscite del runbook vengono indicate come completate, mentre quelle con errore vengono indicate come non riuscite.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Creare un avviso del lettore di monitoraggio di Azure per le notifiche di posta elettronica

Per creare un avviso che usa i log di monitoraggio di Azure, eseguire le operazioni seguenti. Come prerequisito, è necessario che i log di monitoraggio di Azure siano collegati con un'area di lavoro Log Analytics.

1.  Nel portale di Azure selezionare **Ricerca log**.

2.  Creare una query per selezionare gli errori e gli avvisi per gruppo di sincronizzazione entro l'intervallo selezionato. Ad esempio:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Dopo avere eseguito la query, selezionare l'icona a forma di campanello indicante **Avviso**.

4.  In **Genera l'avviso in base a** selezionare **Unità di misura della metrica**.

    1.  Impostare il valore di aggregazione **maggiore di**.

    2.  Dopo **maggiore di**  immettere la soglia che deve trascorrere prima di ricevere le notifiche. Nella sincronizzazione dei dati sono previsti errori temporanei. Per ridurre il rumore, impostare la soglia su 5.

5.  In **Azioni** impostare **Notifica di posta elettronica** su "Sì". Immettere i destinatari di posta elettronica desiderati.

6.  Fare clic su **Save**. I destinatari specificati ora ricevono notifiche tramite e-mail in caso di errori.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Creare una visualizzazione di monitoraggio di Azure per il monitoraggio

Questo passaggio crea una visualizzazione di monitoraggio di Azure per monitorare visivamente tutti i gruppi di sincronizzazione specificati. La vista include diversi componenti:

-   Un riquadro di panoramica che indica il numero di errori, le operazioni riuscite e gli avvisi di tutti i gruppi di sincronizzazione.

-   Un riquadro per tutti i gruppi di sincronizzazione indicante il conteggio di errori e avvisi per ogni gruppo di sincronizzazione. I gruppi senza problemi non vengono elencati in questo riquadro.

-   Un riquadro per ogni gruppo di sincronizzazione, indicante il numero di errori, le operazioni riuscite e gli avvisi, oltre ai messaggi di errore recenti.

Per configurare la visualizzazione di monitoraggio di Azure, eseguire le operazioni seguenti:

1.  Nell'area di lavoro Log Analytics home page selezionare il segno più a sinistra per aprire **Progettazione viste**.

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

**Log di monitoraggio di Azure:** I log di monitoraggio di Azure possono avere un costo associato a seconda dell'utilizzo. Il livello gratuito include 500 MB di dati inseriti al giorno. Nella maggior parte dei casi l'inserimento previsto per questa soluzione è inferiore a 500 MB al mese. Per ridurre l'utilizzo, usare i filtri solo in base all'errore inclusi nel runbook. Se si usano più di 500 MB al giorno, eseguire l'aggiornamento al piano a pagamento per evitare il rischio di interruzione dell'analisi al raggiungimento del limite. Per altre informazioni, vedere [prezzi dei log di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Esempi di codice

Scaricare gli esempi di codice descritti in questo articolo dalle posizioni seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualizzazione monitoraggio di Azure per la sincronizzazione dei dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

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
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md).
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
