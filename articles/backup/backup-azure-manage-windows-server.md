---
title: Gestire server e insiemi di credenziali di Servizi di ripristino di Azure
description: Gestire processi e avvisi in un insieme di credenziali di Servizi di ripristino di Azure.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: 5876ce4a511617d8465cdf008ea0ce14b3ad15d4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466365"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorare e gestire insiemi di credenziali di Servizi di ripristino

Questo articolo illustra come usare il dashboard **Panoramica** dell'insieme di credenziali di Servizi di ripristino per monitorare e gestire gli insiemi di credenziali di Servizi di ripristino. Quando si apre un insieme di credenziali di Servizi di ripristino dall'elenco, viene aperto il dashboard **Panoramica** dell'insieme di credenziali selezionato. Il dashboard fornisce diversi dettagli sull'insieme di credenziali. Sono presenti *riquadri* che mostrano: lo stato degli avvisi critici e delle avvertenze, i processi di backup in corso e non riusciti e la quantità di archiviazione con ridondanza locale e di archiviazione con ridondanza geografica usata. Se si esegue un backup delle VM di Azure nell'insieme di credenziali, il riquadro [**Stato del controllo preliminare di backup** visualizza eventuali avvisi critici o avvertenze](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). L'immagine seguente illustra il dashboard **Panoramica** per **Contoso-vault**. Il riquadro **Elementi di backup** mostra che sono presenti nove elementi registrati nell'insieme di credenziali.

![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

I prerequisiti per questo articolo sono: una sottoscrizione di Azure, un insieme di credenziali di Servizi di ripristino e almeno un elemento di backup configurato per l'insieme di credenziali.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Aprire un insieme di credenziali di Servizi di ripristino

Per monitorare gli avvisi o visualizzare i dati di gestione su un insieme di credenziali di Servizi di ripristino, aprire l'insieme di credenziali.

1. Accedere al [portale di Azure](https://portal.azure.com/) usando la sottoscrizione di Azure.

2. Nel portale fare clic su **Tutti i servizi**.

   ![Aprire l'elenco degli insiemi di credenziali di Servizi di ripristino](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Nella finestra di dialogo **Tutti i servizi** digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando l'opzione **Insiemi di credenziali dei servizi di ripristino** viene visualizzata, fare clic per aprire l'elenco degli insiemi di credenziali di Servizi di ripristino presenti nella sottoscrizione.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Nell'elenco di insiemi di credenziali fare clic su uno di essi per aprire il dashboard **Panoramica**.

    ![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Il dashboard Panoramica usa i riquadri per fornire avvisi e dati sui processi di backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorare i processi di backup e gli avvisi

Il dashboard **Panoramica** dell'insieme di credenziali di Servizi di ripristino include i riquadri delle informazioni relative a monitoraggio e utilizzo. I riquadri nella sezione Monitoraggio visualizzano gli avvisi critici, le avvertenze e i processi in corso e non riusciti. Fare clic su un determinato processo o avviso per aprire il menu Avvisi di backup o Processi di backup, filtrato per tale processo o avviso.

![Eseguire un backup delle attività del dashboard](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

La sezione Monitoraggio mostra i risultati delle query **Avvisi di backup** e **Processi di backup** predefinite. I riquadri di Monitoraggio forniscono informazioni aggiornate su:

* Avvisi critici e avvertenze per i processi di backup (nelle ultime 24 ore)
* Stato del controllo preliminare per le VM di Azure: per informazioni complete sullo stato del controllo preliminare, vedere il [blog sullo stato del controllo preliminare di backup](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* I processi di backup in corso e i processi non riusciti (nelle ultime 24 ore).

I riquadri di Utilizzo forniscono:

* Il numero di elementi di backup configurati per l'insieme di credenziali.
* Lo spazio di archiviazione di Azure (distinto dall'archiviazione con ridondanza locale e dall'archiviazione con ridondanza geografica) utilizzato dall'insieme di credenziali.

Fare clic sui riquadri (a eccezione di Archivio backup) per aprire il menu associato. Nell'immagine precedente il riquadro Avvisi di backup mostra tre avvisi critici. Facendo clic sulla riga degli avvisi critici nel riquadro Avvisi di backup, si aprono gli avvisi di backup filtrati per gli avvisi critici.

![Menu Avvisi di backup filtrato per gli avvisi critici](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Il menu Avvisi di Backup, nell'immagine precedente, viene filtrato per: Lo stato è attivo, la gravità è critica e l'ora è impostata sulle 24 ore precedenti.

## <a name="manage-backup-alerts"></a>Gestire gli avvisi di backup

Per accedere al menu Avvisi di backup, dal menu dell'insieme di credenziali di Servizi di ripristino scegliere **Avvisi di backup**.

![Avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Il report Avvisi di backup elenca gli avvisi per l'insieme di credenziali.

![Avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Avvisi

L'elenco Avvisi di backup visualizza le informazioni selezionate per gli avvisi filtrati. Nel menu Avvisi di backup è possibile filtrare gli avvisi critici o le avvertenze.

| Livello avviso | Eventi che generano avvisi |
| ----------- | ----------- |
| Critico | Si ricevono avvisi critici quando: i processi di backup hanno esito negativo, i processi di ripristino hanno esito negativo e quando si arresta la protezione su un server, ma si conservano i dati.|
| Avviso | Si ricevono avvertenze quando: i processi di backup vengono completati con avvisi, ad esempio quando non viene eseguito un backup di meno di 100 file a causa di problemi di danneggiamento o quando viene eseguito un backup di più di 1.000.000 di file. |
| Informazioni | Non sono attualmente in uso avvisi informativi. |

### <a name="viewing-alert-details"></a>Visualizzazione dei dettagli dell'avviso

Il report Avvisi di backup tiene traccia di otto dettagli su ogni avviso. Usare il pulsante **Scegli colonne** per modificare i dettagli nel report.

![Avvisi di backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

Per impostazione predefinita, tutti i dettagli, tranne **Ora ultima occorrenza**, vengono visualizzati nel report.

* Avviso
* Elementi di backup
* Server protetti
* Gravità
* Duration
* Data creazione
* Status
* Ora ultima occorrenza

### <a name="change-the-details-in-alerts-report"></a>Modificare i dettagli nei report degli avvisi

1. Per modificare le informazioni dei report, nel menu **Avvisi di backup** fare clic su **Scegli colonne**.

   ![Avvisi di backup](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Si apre il menu **Scegli colonne**.

2. Nel menu **Scegli colonne** scegliere i dettagli che si vuole visualizzare nel report.

    ![Menu Scegli colonne](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Fare clic su **Fine** per salvare le modifiche e chiudere il menu Scegli colonne.

   Se si apportano modifiche, ma non si vuole conservarle, fare clic su **Reimposta** per ripristinare l'ultima configurazione salvata della selezione.

### <a name="change-the-filter-in-alerts-report"></a>Modificare il filtro nei report degli avvisi

Usare il menu **Filtra** per modificare la gravità, lo stato, l'ora di inizio e l'ora di fine per gli avvisi.

> [!NOTE]
> Modificando il filtro di Avvisi di backup, gli avvisi critici o le avvertenze nel dashboard Panoramica dell'insieme di credenziali non cambiano.
>  

1. Per modificare il filtro di Avvisi di backup, dal menu Avvisi di backup scegliere **Filtra**.

   ![Scegliere il menu Filtra](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Viene visualizzato il menu Filtra.

   ![Scegliere il menu Filtra](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Modificare la gravità, lo stato, l'ora di inizio o l'ora di fine e fare clic su **Fine** per salvare le modifiche.

## <a name="configuring-notifications-for-alerts"></a>Configurazione delle notifiche per gli avvisi

Configurare le notifiche per generare messaggi di posta elettronica quando si verifica un'avvertenza o un avviso critico. È possibile inviare avvisi di posta elettronica ogni ora o quando viene generato un avviso particolare.

   ![Filtrare gli avvisi](./media/backup-azure-manage-windows-server/configure-notification.png)

Per impostazione predefinita, le notifiche tramite posta elettronica sono **attive**. Fare clic su **No** per arrestare le notifiche tramite posta elettronica.

Nel controllo **Notify** scegliere **Per ogni avviso** se non vuole evitare il raggruppamento o non si hanno molti elementi che potrebbero generare avvisi. Ogni avviso genera una notifica (impostazione predefinita) e viene inviato immediatamente un messaggio di risoluzione.

Se si seleziona **Digest orario**, viene inviato ai destinatari un messaggio di posta elettronica che descrive gli avvisi non risolti generati nell'ultima ora. Allo scadere dell'ora viene inviato un messaggio di posta elettronica relativo alla risoluzione.

Scegliere la gravità degli avvisi (critici o avvertenze) usata per generare il messaggio di posta elettronica. Attualmente non sono disponibili avvisi informativi.

## <a name="manage-backup-items"></a>Gestire gli elementi di backup

Un insieme di credenziali di Servizi di ripristino contiene molti tipi di dati di backup. [Altre](backup-overview.md#what-can-i-back-up) informazioni sugli elementi di cui è possibile eseguire il backup. Per gestire i diversi server, computer, database e carichi di lavoro fare clic sul riquadro **Elementi di backup** per visualizzare i contenuti dell'insieme di credenziali.

![Riquadro Elementi di backup](./media/backup-azure-manage-windows-server/backup-items.png)

Viene aperto l'elenco di elementi di backup, organizzati per tipo di gestione di backup.

![Elenco di elementi di backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Per esplorare uno specifico tipo di istanza protetta, fare clic sull'elemento nella colonna Tipo di gestione di backup. Nell'immagine precedente, ad esempio, sono presenti due macchine virtuali di Azure protette in questo insieme di credenziali. Fare clic su **Macchina virtuale di Azure** per aprire l'elenco di macchine virtuali protette in questo insieme di credenziali.

![Elenco del tipo di backup](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

L'elenco di macchine virtuali include dati utili: il gruppo di risorse associato, il [controllo preliminare di backup](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/) precedente, lo stato dell'ultimo backup e la data del punto di ripristino più recente. I puntini di sospensione nell'ultima colonna aprono il menu per attivare le attività comuni. I dati utili forniti nelle colonne sono diversi per ogni tipo di backup.

![Elenco del tipo di backup](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gestire i processi di backup

Il riquadro **Processi di backup** nel dashboard dell'insieme di credenziali mostra il numero di processi in corso o non riusciti nelle ultime 24 ore. Il riquadro offre una rapida panoramica del menu Processi di backup.

![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Per visualizzare altri dettagli sui processi, fare clic su **In corso** o su **Non riusciti** per aprire il menu Processi di backup filtrato per tale stato.

### <a name="backup-jobs-menu"></a>Menu Processi di backup

Il menu **Processi di backup** visualizza informazioni sul tipo di elemento, sull'operazione, sullo stato, sull'ora di inizio e sulla durata.  

Per aprire il menu Processi di backup, nel menu principale dell'insieme di credenziali fare clic su **Processi di backup**.

![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Viene aperto l'elenco dei processi di backup.

![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Il menu Processi di backup mostra lo stato di tutte le operazioni, in tutti i tipi di backup, per le ultime 24 ore. Usare **Filtra** per modificare i filtri. I filtri vengono illustrati nelle sezioni seguenti.

Per modificare i filtri:

1. Dal menu Processi di backup dell'insieme di credenziali scegliere **Filtra**.

   ![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Viene aperto il menu Filtra.

   ![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Scegliere le impostazioni del filtro e fare clic su **Fine**. L'elenco filtrato viene aggiornato in base alle nuove impostazioni.

#### <a name="item-type"></a>Tipo di elemento

Il tipo di elemento è il tipo di gestione di backup dell'istanza protetta. Ne esistono quattro tipi. Vedere l'elenco seguente. È possibile visualizzare tutti i tipi di elemento o un tipo di elemento. Non è possibile selezionare due o tre tipi di elemento. I tipi di elemento disponibili sono:

* Tutti i tipi di elemento
* Macchina virtuale di Azure
* File e cartelle
* Archiviazione di Azure
* Carico di lavoro di Azure

#### <a name="operation"></a>Operazione

È possibile visualizzare tutte le operazioni o un'operazione. Non è possibile selezionare due o tre operazioni. Le operazioni disponibili sono:

* Tutte le operazioni
* Register
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
* Operazione non riuscita
* Cancellati
* Completato con avvisi

#### <a name="start-time"></a>Ora di inizio

Giorno e ora di inizio della query. L'impostazione predefinita è un periodo di 24 ore.

#### <a name="end-time"></a>Ora di fine

Giorno e ora di fine della query.

### <a name="export-jobs"></a>Esporta processi

Usare **Esporta processi** per creare un foglio di calcolo contenente tutte le informazioni del menu Processi. Il foglio di calcolo ha un foglio contenente un riepilogo di tutti i processi e singoli fogli per ogni processo.

Per esportare le informazioni sui processi in un foglio di calcolo, fare clic su **Esporta processi**. Il servizio crea un foglio di calcolo usando il nome dell'insieme di credenziali e la data, ma è possibile modificare il nome.

## <a name="monitor-backup-usage"></a>Monitorare l'utilizzo del backup

Il riquadro Archivio di backup del dashboard indica lo spazio di archiviazione utilizzato in Azure. L'utilizzo dello spazio di archiviazione viene fornito per:

* Utilizzo dello spazio di archiviazione con ridondanza locale nel cloud associato all'insieme di credenziali
* Utilizzo dello spazio di archiviazione con ridondanza geografica nel cloud associato all'insieme di credenziali


## <a name="troubleshooting-monitoring-issues"></a>Risoluzione dei problemi di monitoraggio

**Problema:** i processi e/o gli avvisi generati dall'agente di Backup di Azure non vengono visualizzati nel portale.

**Passaggi per la risoluzione dei problemi:** Il prpcesso, ```OBRecoveryServicesManagementAgent```, viene usato per inviare i dati dei processi e degli avvisi al servizio Backup di Azure. A volte questo processo può risultare danneggiato o arrestato.

1. Per controllare se il processo si è arrestato, aprire **Gestione attività** e controllare se ```OBRecoveryServicesManagementAgent``` è in esecuzione.

2. Se il processo non è in esecuzione, aprire il **Pannello di controllo** e sfogliare l'elenco dei servizi. Avviare o riavviare **Agente di gestione di Servizi di ripristino di Microsoft Azure**.

    Per altre informazioni, sfogliare i log in:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Ad esempio:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passaggi successivi
* [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
* Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](backup-introduction-to-azure-backup.md)
* Visitare il [Forum su Backup di Azure](https://go.microsoft.com/fwlink/p/?LinkId=290933)
