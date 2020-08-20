---
title: Gestire server e insiemi di credenziali di Servizi di ripristino di Azure
description: Questo articolo illustra come usare il dashboard di panoramica dell'insieme di credenziali di servizi di ripristino per monitorare e gestire gli insiemi di credenziali dei servizi di ripristino.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: ee96acf624f1c313c85b21840c142e1e2d6f40d8
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654209"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorare e gestire insiemi di credenziali di Servizi di ripristino

Questo articolo illustra come usare il dashboard **Panoramica** dell'insieme di credenziali di Servizi di ripristino per monitorare e gestire gli insiemi di credenziali di Servizi di ripristino. Quando si apre un insieme di credenziali di Servizi di ripristino dall'elenco, viene aperto il dashboard **Panoramica** dell'insieme di credenziali selezionato. Il dashboard fornisce diversi dettagli sull'insieme di credenziali. Sono disponibili *riquadri* che mostrano lo stato di avvisi critici e di avviso, i processi di backup in corso e non riusciti e la quantità di archiviazione con ridondanza locale (con ridondanza locale) e l'archiviazione con ridondanza geografica (GRS) usata. Se si esegue un backup delle VM di Azure nell'insieme di credenziali, il riquadro [**Stato del controllo preliminare di backup** visualizza eventuali avvisi critici o avvertenze](#backup-pre-check-status). L'immagine seguente illustra il dashboard **Panoramica** per **Contoso-vault**. Il riquadro **Elementi di backup** mostra che sono presenti nove elementi registrati nell'insieme di credenziali.

![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

I prerequisiti per questo articolo sono: una sottoscrizione di Azure, un insieme di credenziali di Servizi di ripristino e almeno un elemento di backup configurato per l'insieme di credenziali.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Aprire un insieme di credenziali di Servizi di ripristino

Per monitorare gli avvisi o visualizzare i dati di gestione su un insieme di credenziali di Servizi di ripristino, aprire l'insieme di credenziali.

1. Accedere al [portale di Azure](https://portal.azure.com/) usando la sottoscrizione di Azure.

2. Nel portale selezionare **Tutti i servizi**.

   ![Aprire l'elenco degli insiemi di credenziali di Servizi di ripristino](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Nella finestra di dialogo **Tutti i servizi** digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione insiemi di credenziali dei **servizi di ripristino** , selezionarla per aprire l'elenco degli insiemi di credenziali dei servizi di ripristino nella sottoscrizione.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Nell'elenco degli insiemi di credenziali selezionare un insieme di credenziali per aprire il relativo dashboard di **Panoramica** .

    ![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Il dashboard Panoramica usa i riquadri per fornire avvisi e dati sui processi di backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorare i processi di backup e gli avvisi

Il dashboard **Panoramica** dell'insieme di credenziali di Servizi di ripristino include i riquadri delle informazioni relative a monitoraggio e utilizzo. I riquadri nella sezione Monitoraggio visualizzano gli avvisi critici, le avvertenze e i processi in corso e non riusciti. Selezionare un particolare avviso o processo per aprire il menu avvisi di backup o processi di backup, filtrato per tale processo o avviso.

![Eseguire un backup delle attività del dashboard](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

La sezione Monitoraggio mostra i risultati delle query **Avvisi di backup** e **Processi di backup** predefinite. I riquadri di Monitoraggio forniscono informazioni aggiornate su:

* Avvisi critici e avvertenze per i processi di backup (nelle ultime 24 ore)
* Stato di verifica preliminare per le macchine virtuali di Azure. Per informazioni complete sullo stato di verifica preliminare, vedere [backup pre-check status](#backup-pre-check-status).
* I processi di backup in corso e i processi non riusciti (nelle ultime 24 ore).

I riquadri di Utilizzo forniscono:

* Il numero di elementi di backup configurati per l'insieme di credenziali.
* Lo spazio di archiviazione di Azure (distinto dall'archiviazione con ridondanza locale e dall'archiviazione con ridondanza geografica) utilizzato dall'insieme di credenziali.

Selezionare i riquadri (ad eccezione di archiviazione di backup) per aprire il menu associato. Nell'immagine precedente il riquadro Avvisi di backup mostra tre avvisi critici. Selezionando la riga avvisi critici nel riquadro avvisi di backup, vengono aperti gli avvisi di backup filtrati per gli avvisi critici.

![Menu Avvisi di backup filtrato per gli avvisi critici](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Il menu Avvisi di backup nell'immagine precedente viene filtrato per: stato attivo, gravità critica e intervallo di tempo corrispondente alle 24 ore precedenti.

### <a name="backup-pre-check-status"></a>Stato del controllo preliminare di backup

I controlli preliminari di backup controllano la configurazione delle VM per individuare eventuali problemi che possono influire negativamente sui backup. Queste informazioni vengono aggregate in modo che sia possibile visualizzarle direttamente dal dashboard dell'insieme di credenziali dei servizi di ripristino e fornire consigli per le misure correttive per garantire la corretta esecuzione di backup coerenti con i file o coerenti con l'applicazione. Non richiedono alcuna infrastruttura e non hanno costi aggiuntivi.  

I controlli preliminari di backup vengono eseguiti come parte delle operazioni di backup pianificate per le macchine virtuali di Azure. Concludono con uno degli Stati seguenti:

* **Passato**: questo stato indica che la configurazione della macchina virtuale deve condurre a backup completati e non è necessario eseguire alcuna azione correttiva.
* **Avviso**: questo stato indica uno o più problemi nella configurazione della macchina virtuale che *potrebbero* causare errori di backup. Sono disponibili procedure *consigliate* per garantire la riuscita dei backup. Se, ad esempio, non è installato l'agente VM più recente, i backup non vengono eseguiti in modo intermittente. Questa situazione fornirà uno stato di avviso.
* **Critico**: questo stato indica uno o più problemi critici nella configurazione della macchina *virtuale che comporteranno* errori di backup e fornirà i passaggi *necessari* per garantire il corretto backup. Un problema di rete, ad esempio, causato da un aggiornamento alle regole NSG di una macchina virtuale, provocherà l'esito negativo dei backup, in quanto impedisce alla macchina virtuale di comunicare con il servizio backup di Azure. Questa situazione fornirà uno stato critico.

Attenersi alla procedura seguente per iniziare a risolvere gli eventuali problemi segnalati dai controlli preliminari di backup per i backup delle VM nell'insieme di credenziali di servizi di ripristino.

* Selezionare il riquadro **stato del controllo preliminare di backup (VM di Azure)** nel dashboard dell'insieme di credenziali di servizi di ripristino.
* Selezionare una macchina virtuale con uno stato di verifica preliminare del backup **critico** o **avviso**. Questa azione apre il riquadro dei **Dettagli della macchina virtuale** .
* Selezionare la notifica del riquadro nella parte superiore del riquadro per visualizzare la descrizione del problema di configurazione e i passaggi correttivi.

## <a name="manage-backup-alerts"></a>Gestire gli avvisi di backup

Per accedere al menu avvisi di backup, nel menu dell'insieme di credenziali di servizi di ripristino selezionare **avvisi di backup**.

![Avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Il report Avvisi di backup elenca gli avvisi per l'insieme di credenziali.

![Report avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Avvisi

L'elenco Avvisi di backup visualizza le informazioni selezionate per gli avvisi filtrati. Nel menu Avvisi di backup è possibile filtrare gli avvisi critici o le avvertenze.

| Livello avviso | Eventi che generano avvisi |
| ----------- | ----------- |
| Critico | Si ricevono avvisi critici quando: i processi di backup hanno esito negativo, i processi di ripristino hanno esito negativo e quando si arresta la protezione su un server, ma si conservano i dati.|
| Avviso | Si ricevono avvisi quando: i processi di backup sono stati completati con avvisi. Ad esempio, quando non viene eseguito il backup di meno di 100 file a causa di problemi di danneggiamento o quando viene eseguito il backup di più di 1 milione file. |
| Informativo | Non sono attualmente in uso avvisi informativi. |

### <a name="viewing-alert-details"></a>Visualizzazione dei dettagli dell'avviso

Il report Avvisi di backup tiene traccia di otto dettagli su ogni avviso. Usare il pulsante **Scegli colonne** per modificare i dettagli nel report.

![Pulsante Seleziona colonne per gli avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

Per impostazione predefinita, tutti i dettagli, tranne **Ora ultima occorrenza**, vengono visualizzati nel report.

* Avviso
* Elementi di backup
* Server protetti
* Gravità
* Duration
* Data creazione
* Stato
* Ora ultima occorrenza

### <a name="change-the-details-in-alerts-report"></a>Modificare i dettagli nei report degli avvisi

1. Per modificare le informazioni del report, scegliere **Scegli colonne**dal menu **avvisi di backup** .

   ![Selezionare Scegli colonne](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Si apre il menu **Scegli colonne**.

2. Nel menu **Scegli colonne** scegliere i dettagli che si vuole visualizzare nel report.

    ![Menu Scegli colonne](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Selezionare **fine** per salvare le modifiche e chiudere il menu Scegli colonne.

   Se si apportano modifiche ma non si desidera conservare le modifiche, selezionare **Reimposta** per restituire l'oggetto selezionato all'ultima configurazione salvata.

### <a name="change-the-filter-in-alerts-report"></a>Modificare il filtro nei report degli avvisi

Usare il menu **Filtra** per modificare la gravità, lo stato, l'ora di inizio e l'ora di fine per gli avvisi.

> [!NOTE]
> Modificando il filtro di Avvisi di backup, gli avvisi critici o le avvertenze nel dashboard Panoramica dell'insieme di credenziali non cambiano.
>  

1. Per modificare il filtro avvisi di backup, scegliere **filtro**dal menu avvisi di backup.

   ![Scegliere il menu Filtra](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Viene visualizzato il menu Filtra.

   ![Menu avviso filtro](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Modificare la gravità, lo stato, l'ora di inizio o l'ora di fine **e selezionare fine** per salvare le modifiche.

## <a name="configuring-notifications-for-alerts"></a>Configurazione delle notifiche per gli avvisi

Configurare le notifiche per generare messaggi di posta elettronica quando si verifica un'avvertenza o un avviso critico. È possibile inviare avvisi di posta elettronica ogni ora o quando viene generato un avviso particolare.

   ![Filtrare gli avvisi](./media/backup-azure-manage-windows-server/configure-notification.png)

Per impostazione predefinita, le notifiche tramite posta elettronica sono **attive**. Selezionare **disattivato** per arrestare le notifiche tramite posta elettronica.

Nel controllo **Notify** scegliere **Per ogni avviso** se non vuole evitare il raggruppamento o non si hanno molti elementi che potrebbero generare avvisi. Ogni avviso genera una notifica (impostazione predefinita) e viene inviato immediatamente un messaggio di risoluzione.

Se si seleziona **Digest orario**, viene inviato ai destinatari un messaggio di posta elettronica che descrive gli avvisi non risolti generati nell'ultima ora. Allo scadere dell'ora viene inviato un messaggio di posta elettronica relativo alla risoluzione.

Scegliere la gravità degli avvisi (critici o avvertenze) usata per generare il messaggio di posta elettronica. Attualmente non sono disponibili avvisi informativi.

## <a name="manage-backup-items"></a>Gestire gli elementi di backup

Un insieme di credenziali di Servizi di ripristino contiene molti tipi di dati di backup. [Altre](backup-overview.md#what-can-i-back-up) informazioni sugli elementi di cui è possibile eseguire il backup. Per gestire i vari server, computer, database e carichi di lavoro, selezionare il riquadro **elementi di backup** per visualizzare il contenuto dell'insieme di credenziali.

![Riquadro Elementi di backup](./media/backup-azure-manage-windows-server/backup-items.png)

Viene aperto l'elenco di elementi di backup, organizzati per tipo di gestione di backup.

![Elenco di elementi di backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Per esplorare un tipo specifico di istanza protetta, selezionare l'elemento nella colonna tipo di gestione backup. Nell'immagine precedente, ad esempio, sono presenti due macchine virtuali di Azure protette in questo insieme di credenziali. Selezionando **macchina virtuale di Azure**, viene aperto l'elenco delle macchine virtuali protette in questo insieme di credenziali.

![Elenco di macchine virtuali protette](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

L'elenco di macchine virtuali include dati utili: il gruppo di risorse associato, il [controllo preliminare di backup](#backup-pre-check-status) precedente, lo stato dell'ultimo backup e la data del punto di ripristino più recente. I puntini di sospensione nell'ultima colonna aprono il menu per attivare le attività comuni. I dati utili forniti nelle colonne sono diversi per ogni tipo di backup.

![Menu Apri puntini di sospensione per le attività comuni](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gestire i processi di backup

Il riquadro **Processi di backup** nel dashboard dell'insieme di credenziali mostra il numero di processi in corso o non riusciti nelle ultime 24 ore. Il riquadro offre una rapida panoramica del menu Processi di backup.

![Riquadro back Jobs](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Per visualizzare ulteriori dettagli sui processi, selezionare **in corso** o **non è** stato possibile aprire il menu processi di backup filtrato per tale stato.

### <a name="backup-jobs-menu"></a>Menu Processi di backup

Il menu **Processi di backup** visualizza informazioni sul tipo di elemento, sull'operazione, sullo stato, sull'ora di inizio e sulla durata.  

Per aprire il menu processi di backup, scegliere **processi di backup**dal menu principale dell'insieme di credenziali.

![Seleziona processi di backup](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Viene aperto l'elenco dei processi di backup.

![Elenco dei processi di backup](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Il menu Processi di backup mostra lo stato di tutte le operazioni, in tutti i tipi di backup, per le ultime 24 ore. Usare **Filtra** per modificare i filtri. I filtri vengono illustrati nelle sezioni seguenti.

Per modificare i filtri:

1. Nel menu processi di backup dell'insieme di credenziali selezionare **filtro**.

   ![Selezionare il filtro per i processi di backup](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Viene aperto il menu Filtra.

   ![Menu filtro aperto per i processi di backup](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Scegliere le impostazioni del filtro e selezionare **fine**. L'elenco filtrato viene aggiornato in base alle nuove impostazioni.

#### <a name="item-type"></a>Tipo elemento

Il tipo di elemento è il tipo di gestione di backup dell'istanza protetta. Ne esistono quattro tipi. Vedere l'elenco seguente. È possibile visualizzare tutti i tipi di elemento o un tipo di elemento. Non è possibile selezionare due o tre tipi di elemento. I tipi di elemento disponibili sono:

* Tutti i tipi di elemento
* Macchina virtuale di Azure
* File e cartelle
* Archiviazione di Azure
* Carico di lavoro di Azure

#### <a name="operation"></a>Operazione

È possibile visualizzare tutte le operazioni o un'operazione. Non è possibile selezionare due o tre operazioni. Le operazioni disponibili sono:

* Tutte le operazioni
* Registrazione
* Configurare il backup
* Backup
* Restore
* Disabilitare il backup
* Elimina dati di backup

#### <a name="status"></a>Stato

È possibile visualizzare tutti gli stati o uno. Non è possibile selezionare due o tre stati. Gli stati disponibili sono:

* Tutti gli stati
* Completi
* In corso
* Non riuscito
* Cancellati
* Completato con avvisi

#### <a name="start-time"></a>Ora di inizio

Giorno e ora di inizio della query. L'impostazione predefinita è un periodo di 24 ore.

#### <a name="end-time"></a>Ora di fine

Giorno e ora di fine della query.

### <a name="export-jobs"></a>Esporta processi

Usare **Esporta processi** per creare un foglio di calcolo contenente tutte le informazioni del menu Processi. Il foglio di calcolo ha un foglio contenente un riepilogo di tutti i processi e singoli fogli per ogni processo.

Per esportare le informazioni sui processi in un foglio di calcolo, selezionare **Esporta processi**. Il servizio crea un foglio di calcolo usando il nome dell'insieme di credenziali e la data, ma è possibile modificare il nome.

## <a name="monitor-backup-usage"></a>Monitorare l'utilizzo del backup

Il riquadro Archivio di backup del dashboard indica lo spazio di archiviazione utilizzato in Azure. L'utilizzo dello spazio di archiviazione viene fornito per:

* Utilizzo dello spazio di archiviazione con ridondanza locale nel cloud associato all'insieme di credenziali
* Utilizzo dello spazio di archiviazione con ridondanza geografica nel cloud associato all'insieme di credenziali

## <a name="troubleshooting-monitoring-issues"></a>Risoluzione dei problemi di monitoraggio

**Problema:** I processi e/o gli avvisi dall'agente di backup di Azure non vengono visualizzati nel portale.

**Procedura per la risoluzione del problema:** il processo, ```OBRecoveryServicesManagementAgent```, viene usato per inviare i dati dei processi e degli avvisi al servizio Backup di Azure. A volte questo processo può risultare danneggiato o arrestato.

1. Per controllare se il processo si è arrestato, aprire **Gestione attività** e controllare se ```OBRecoveryServicesManagementAgent``` è in esecuzione.

2. Se il processo non è in esecuzione, aprire il **Pannello di controllo** e sfogliare l'elenco dei servizi. Avviare o riavviare **Agente di gestione di Servizi di ripristino di Microsoft Azure**.

    Per altre informazioni, sfogliare i log in:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Ad esempio:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passaggi successivi

* [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
* Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](./backup-overview.md)
