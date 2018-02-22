---
title: Monitorare sincronizzazione dati SQL di Azure con Log Analytics di OMS | Microsoft Docs
description: Informazioni su come monitorare la sincronizzazione dati SQL di Azure usando Log Analytics di OMS
services: sql-database
ms.date: 11/07/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 8683b3aec569f210529c1188cbbf514f7956b340
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-sql-data-sync-preview-with-oms-log-analytics"></a>Monitorare sincronizzazione dati SQL (anteprima) con OMS Log Analytics 

Per controllare il registro attività di sincronizzazione dati SQL e rilevare gli errori e gli avvisi, in precedenza era necessario verificare manualmente la sincronizzazione dati SQL nel Portale di Azure o usare PowerShell o l'API REST. Per configurare una soluzione personalizzata che migliori l'esperienza di monitoraggio della sincronizzazione dei dati, seguire i passaggi descritti in questo articolo. È possibile personalizzare questa soluzione per adattarla allo scenario specifico.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Dashboard di monitoraggio per tutti i gruppi di sincronizzazione 

Per individuare i problemi, non è più necessario esaminare singolarmente i log di ogni gruppo di sincronizzazione. È possibile monitorare tutti i gruppi di sincronizzazione di una qualsiasi delle sottoscrizioni in un'unica posizione tramite una vista personalizzata di OMS (Operations Management Suite). Questa vista evidenzia le informazioni importanti per i clienti di sincronizzazione dati SQL.

![Dashboard di monitoraggio della sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notifiche automatiche tramite posta elettronica

Non è più necessario controllare manualmente il log nel portale di Azure oppure tramite PowerShell o l'API REST. Con [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), è possibile creare avvisi inviati direttamente agli indirizzi e-mail delle persone che hanno bisogno di vederli in caso di errore.

![Notifiche tramite e-mail sulla sincronizzazione dei dati](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Come configurare queste funzioni di monitoraggio 

Implementare una soluzione di monitoraggio OMS personalizzata per la sincronizzazione dati SQL in meno di un'ora eseguendo le operazioni seguenti:

È necessario configurare tre componenti:

-   Un runbook PowerShell per inserire i dati dei log di sincronizzazione dati SQL in OMS.

-   Un avviso di Log Analytics di OMS per le notifiche e-mail.

-   Una vista di OMS per il monitoraggio.

### <a name="samples-to-download"></a>Campioni da scaricare

Scaricare i due esempi seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista in OMS dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>prerequisiti

Assicurarsi di avere configurato quanto segue:

-   Un account di Automazione di Azure

-   Log Analytics collegato con area di lavoro OMS

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook PowerShell per ottenere il log di sincronizzazione dati SQL 

Usare un runbook PowerShell in hosting in Automazione di Azure per eseguire il pull dei dati dei log di sincronizzazione dati SQL e inviarli a OMS. È incluso uno script di esempio. Come prerequisito è necessario disporre di un account di Automazione di Azure. È quindi necessario creare un runbook e pianificarne l'esecuzione. 

### <a name="create-a-runbook"></a>Creare un runbook

Per altre informazioni sulla creazione di un runbook, vedere [Il primo runbook PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Nell'account di Automazione di Azure selezionare la scheda**Runbook** in Automazione processi.

2.  Selezionare **Aggiungi runbook** nell'angolo superiore sinistro della pagina Runbook.

3.  Selezionare **Importare un runbook esistente**.

4.  In **	File di runbook** usare il file `DataSyncLogPowerShellRunbook` specifico. Impostare il **tipo di runbook** come `PowerShell`. Assegnare un nome al runbook.

5.  Selezionare **Create**. È ora disponibile un runbook.

6.  In Account di Automazione di Azure selezionare la scheda**Variabili** in Risorse condivise.

7.  Selezionare **Aggiungi variabile** nella pagina Variabili. Creare una variabile per archiviare l'ora dell'ultima esecuzione del runbook. Se sono disponibili più runbook, è necessaria una variabile per ogni runbook.

8.  Impostare il nome della variabile come `DataSyncLogLastUpdatedTime` e impostarne il tipo come valore DateTime.

9.  Selezionare il runbook e fare clic sul pulsante di modifica nella parte superiore della pagina.

10. Apportare le modifiche necessarie per l'account e la configurazione della sincronizzazione dati SQL. Per informazioni più dettagliate, vedere lo script di esempio.

    1.  Informazioni su Azure.

    2.  Informazioni sul gruppo di sincronizzazione.

    3.  Informazioni su OMS. Individuare queste informazioni nel portale di OMS | Impostazioni | Origini connesse. Per altre informazioni sull'invio di dati a Log Analytics, vedere [Inviare dati a Log Analytics con l'API dell'agente di raccolta dati HTTP (anteprima pubblica)](../log-analytics/log-analytics-data-collector-api.md).

11. Eseguire il runbook nel riquadro di test. Assicurarsi che sia stato eseguito correttamente.

    Se sono presenti errori, verificare di avere installato il modulo PowerShell più recente. È possibile installare il modulo di PowerShell più recente in **Raccolta di moduli** nell'account di automazione di Azure.

12. Fare clic su **Pubblica**.

### <a name="schedule-the-runbook"></a>Pianificare il runbook

Per pianificare il runbook:

1.  In Runbook selezionare la scheda **Pianificazioni** in Risorse.

2.  Selezionare **Aggiungi pianificazione** nella pagina Pianificazioni.

3.  Selezionare **Collegare una pianificazione al runbook**.

4.  Selezionare quindi **Crea una nuova pianificazione**.

5.  Impostare **Ricorrenza** su Ricorrente e impostare l'intervallo desiderato. Usare lo stesso intervallo qui, nello script e in OMS.

6.  Selezionare **Create**.

### <a name="check-the-automation"></a>Controllare l'automazione

Per monitorare se l'automazione è in esecuzione come previsto, in **Anteprima** per l'account di automazione individuare la vista **Statistiche processi** in **Monitoraggio**. Aggiungere la vista al dashboard per maggiore comodità di visualizzazione. Le esecuzioni riuscite del runbook vengono indicate come completate, mentre quelle con errore vengono indicate come non riuscite.

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Creare un avviso del lettore di log di OMS per le notifiche e-mail

Per creare un avviso che usa Log Analytics di OMS, eseguire le operazioni seguenti. Come prerequisito è necessario che Log Analytics sia collegato a un'area di lavoro OMS.

1.  Nel portale di OMS selezionare **Ricerca log**.

2.  Creare una query per selezionare gli errori e gli avvisi per gruppo di sincronizzazione entro l'intervallo selezionato. Ad esempio: 

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Dopo avere eseguito la query, selezionare l'icona a forma di campanello indicante **Avviso**.

4.  In **Genera l'avviso in base a** selezionare **Unità di misura della metrica**.

    1.  Impostare il valore di aggregazione **maggiore di**.

    2.  Dopo **maggiore di**  immettere la soglia che deve trascorrere prima di ricevere le notifiche. Nella sincronizzazione dei dati sono previsti errori temporanei. Per ridurre il rumore, impostare la soglia su 5.

5.  In **Azioni** impostare **Notifica di posta elettronica** su "Sì". Immettere i destinatari di posta elettronica desiderati.

6.  Fare clic su **Save**. I destinatari specificati ora ricevono notifiche tramite e-mail in caso di errori.

## <a name="create-an-oms-view-for-monitoring"></a>Creare una vista di OMS per il monitoraggio

Questo passaggio consente di creare una vista in OMS per monitorare visivamente tutti i gruppi di sincronizzazione specificati. La vista include diversi componenti:

-   Un riquadro di panoramica che indica il numero di errori, le operazioni riuscite e gli avvisi di tutti i gruppi di sincronizzazione.

-   Un riquadro per tutti i gruppi di sincronizzazione indicante il conteggio di errori e avvisi per ogni gruppo di sincronizzazione. I gruppi senza problemi non vengono elencati in questo riquadro.

-   Un riquadro per ogni gruppo di sincronizzazione, indicante il numero di errori, le operazioni riuscite e gli avvisi, oltre ai messaggi di errore recenti.

Per configurare la vista di OMS, eseguire le operazioni seguenti:

1.  Nella home page di OMS selezionare il segno più a sinistra per aprire la **finestra di progettazione viste**.

2.  Selezionare **Importa** nella barra superiore della finestra di progettazione viste. Selezionare quindi il file di esempio "DataSyncLogOMSView".

3.  La vista di esempio è relativa alla gestione di due gruppi di sincronizzazione. Modificare questa vista per adattarla allo scenario. Fare clic su **modifica** e apportare le modifiche seguenti:

    1.  Creare nuovi oggetti "	Grafico ad anello ed elenco" dalla raccolta in base alle esigenze.

    2.  In ogni riquadro aggiornare le query con le informazioni necessarie.

        1.  In ogni riquadro modificare l'intervallo di TimeStamp_t nel modo desiderato.

        2.  Nei riquadri per ogni gruppo di sincronizzazione aggiornare i nomi dei gruppi di sincronizzazione.

    3.  In ogni riquadro aggiornare il titolo in base alle esigenze.

4.  Fare clic su **Salva** e la vista è pronta.

## <a name="cost-of-this-solution"></a>Costo di questa soluzione

Nella maggior parte dei casi questa soluzione è gratuita.

**Automazione di Azure:** è possibile che venga addebitato un costo con l'account di Automazione di Azure, in base all'utilizzo. I primi 500 minuti al mese del tempo di esecuzione processo sono gratuiti. Nella maggior parte dei casi l'utilizzo previsto per questa soluzione è inferiore a 500 minuti al mese. Per evitare costi, pianificare l'esecuzione del runbook a un intervallo di due o più ore. Per altre informazioni, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

**Log Analytics di OMS:** è possibile che venga addebitato un costo associato a OMS in base all'utilizzo. Il livello gratuito include 500 MB di dati inseriti al giorno. Nella maggior parte dei casi l'inserimento previsto per questa soluzione è inferiore a 500 MB al mese. Per ridurre l'utilizzo, usare i filtri solo in base all'errore inclusi nel runbook. Se si usano più di 500 MB al giorno, eseguire l'aggiornamento al piano a pagamento per evitare il rischio di interruzione dell'analisi al raggiungimento del limite. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Esempi di codice

Scaricare gli esempi di codice descritti in questo articolo dalle posizioni seguenti:

-   [Runbook PowerShell dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista in OMS dei log di sincronizzazione dati](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   [Configurare la sincronizzazione dati SQL](sql-database-get-started-sql-data-sync.md)
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
