---
title: Gestire server e insiemi di credenziali di Servizi di ripristino di Azure | Microsoft Docs
description: Usare questa esercitazione per imparare a gestire server e insiemi di credenziali dei servizi di ripristino di Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markgal
ms.openlocfilehash: 5922e308f5c205a07bd329c28322ae82cea0e1fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Monitorare e gestire i server e gli insiemi di credenziali dei servizi di ripristino di Azure per i computer Windows
> [!div class="op_single_selector"]
> * [Gestione risorse](backup-azure-manage-windows-server.md)
> * [Classico](backup-azure-manage-windows-server-classic.md)
>
>

In questo articolo è disponibile una panoramica delle attività di gestione e monitoraggio dei backup disponibili tramite il portale di Azure e l'agente di Backup di Microsoft Azure. Questo articolo presuppone che sia già disponibile una sottoscrizione di Azure e che sia stato creato almeno un insieme di credenziali di Servizi di ripristino.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Aprire un insieme di credenziali di Servizi di ripristino

Il dashboard dell'insieme di credenziali di Servizi di ripristino visualizza i dettagli o attributi di un insieme di credenziali di Servizi di ripristino.

1. Accedere al [portale di Azure](https://portal.azure.com/) usando la sottoscrizione di Azure.
2. Nel menu Hub fare clic su **Altri servizi**.

    ![Aprire l'elenco degli insiemi di credenziali di Servizi di ripristino](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

3. Si vuole aprire un insieme di credenziali di Servizi di ripristino. Nella finestra di dialogo, iniziare a digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino** per visualizzare l'elenco degli insiemi di credenziali di Servizi di ripristino presenti nella sottoscrizione.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-windows-server/browse-to-rs-vaults-2.png) <br/>

    Verrà visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare il nome dell'insieme da aprire. Si apre il pannello del dashboard dell'insieme di credenziali dei servizi di ripristino.

    ![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Dopo aver aperto l'insieme di credenziali di Servizi di ripristino, provare una delle attività di monitoraggio o gestione.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorare i processi di backup e gli avvisi

I processi e gli avvisi vengono monitorati dal dashboard dell'insieme di credenziali dei servizi di ripristino, dove vengono visualizzati:

* Dettagli degli avvisi di backup
* File e cartelle, oltre alle macchine virtuali di Azure protette nel cloud
* Spazio di archiviazione totale utilizzato in Azure
* Stato dei processi di backup

![Eseguire un backup delle attività del dashboard](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Fare clic sulle informazioni in ogni riquadro per aprire il pannello associato dove si gestiscono le attività correlate.

Nella parte superiore del dashboard:

* Impostazioni: fornisce l'accesso alle attività di backup disponibili.
* Backup: consente di eseguire il backup di nuovi file e cartelle (o VM di Azure) nell'insieme di credenziali dei servizi di ripristino.
* Elimina: se un insieme di credenziali dei servizi di ripristino non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. L'opzione Elimina viene abilitata solo dopo l'eliminazione di tutti i server protetti dall'insieme di credenziali.

![Eseguire un backup delle attività del dashboard](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Avvisi per i backup con l'agente di Backup di Azure:
| Livello avviso | Avvisi inviati |
| --- | --- |
| Critico |Errore di backup, errore di ripristino |
| Avviso |Backup completato con avvisi (quando non viene eseguito il backup di meno di cento file a causa di problemi di danneggiamento e viene completato il backup di più di un milione di file) |
| Informazioni |None |

## <a name="manage-backup-alerts"></a>Gestire gli avvisi di backup
Fare clic sul riquadro **Avvisi di backup** per aprire il pannello **Avvisi di backup** e gestire gli avvisi.

![Avvisi di backup](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Il riquadro Avvisi di backup mostra il numero di:

* avvisi critici non risolti nelle ultime 24 ore
* avvertenze non risolte nelle ultime 24 ore

Fare clic su ogni collegamento per passare al pannello **Avvisi di backup** con una visualizzazione filtrata di questi avvisi (critici o avvertenze).

Nel pannello Avvisi di backup è possibile:

* Scegliere le informazioni appropriate da includere con gli avvisi.

    ![Scegliere le colonne](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrare gli avvisi per gravità, stato e ora di inizio/fine.

    ![Filtrare gli avvisi](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Configurare le notifiche in relazione a gravità, frequenza e destinatari, oltre ad attivare o disattivare gli avvisi.

    ![Filtrare gli avvisi](./media/backup-azure-manage-windows-server/configure-notifications.png)

Se si seleziona **Per ogni avviso** come frequenza per **Notifica**, nei messaggi di posta elettronica non vengono eseguiti raggruppamenti o riduzioni. Ogni avviso restituisce 1 notifica. Questa è l'impostazione predefinita e viene anche inviato immediatamente il messaggio di posta elettronica relativo alla risoluzione.

Se si seleziona **Riepilogo orario** come frequenza per **Notifica**, viene inviato all'utente un messaggio di posta elettronica che informa che sono presenti nuovi avvisi non risolti generati nell'ultima ora. Allo scadere dell'ora viene inviato un messaggio di posta elettronica relativo alla risoluzione.

Possono essere inviati avvisi per i livelli di gravità seguenti:

* Critico
* Avviso
* Informazioni

Per disattivare l'avviso, usare il pulsante **Disattiva** nel pannello dei dettagli del processo. Quando si fa clic su Disattiva, è possibile inserire note sulla risoluzione.

Per scegliere le colonne da visualizzare nell'avviso, usare il pulsante **Scegli colonne** .

> [!NOTE]
> Nel pannello **Impostazioni** si gestiscono gli avvisi di backup selezionando **Monitoraggio e report > Avvisi ed eventi > Avvisi di backup** e quindi facendo clic su **Filtro** o su **Configura notifiche**.
>
>

## <a name="manage-backup-items"></a>Gestire gli elementi di backup
Nel portale di gestione ora è disponibile la gestione dei backup locali. Nella sezione Backup del dashboard il riquadro **Elementi di backup** indica il numero di elementi di backup protetti per l'insieme di credenziali.

Fare clic su **File-cartelle** nel riquadro Elementi di backup.

![Riquadro Elementi di backup](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Il pannello Elementi di backup si apre con il filtro impostato su File-cartella, dove è possibile vedere elencato ogni elemento di backup specifico.

![Elementi di backup](./media/backup-azure-manage-windows-server/backup-item-list.png)

Se si seleziona un elemento di backup specifico nell'elenco, vengono visualizzati i dettagli più importanti dell'elemento.

> [!NOTE]
> Nel pannello **Impostazioni** si gestiscono file e cartelle selezionando **Elementi protetti > Elementi di backup** e quindi scegliendo **File-Cartelle** dal menu a discesa.
>
>

![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Gestire i processi di backup
I processi di backup per i backup sia locali (quando il server locale esegue il backup in Azure) che di Azure sono visibili nel dashboard.

Nella sezione Backup del dashboard il riquadro Processo di backup indica il numero di processi:

* in corso
* non riusciti nelle ultime 24 ore

Per gestire i processi di backup, fare clic sul riquadro **Processi di backup** per aprire il pannello Processi di backup.

![Elementi di backup da Impostazioni](./media/backup-azure-manage-windows-server/backup-jobs.png)

Per modificare le informazioni disponibili nel pannello Processi di backup, usare il pulsante **Scegli colonne** nella parte superiore della pagina.

Usare il pulsante **Filtro** per scegliere tra File e cartelle e Backup macchina virtuale di Azure.

Se i file e le cartelle di cui è stato eseguito il backup non vengono visualizzati, fare clic sul pulsante **Filtro** nella parte superiore della pagina e scegliere **File e cartelle** dal menu Tipo di elemento.

> [!NOTE]
> Nel pannello **Impostazioni** si gestiscono i processi di Backup selezionando **Monitoraggio e report > Processi > Processi di backup** e quindi scegliendo **File-Cartelle** dal menu a discesa.
>
>

## <a name="monitor-backup-usage"></a>Monitorare l'utilizzo del backup
Nella sezione Backup del dashboard, il riquadro Utilizzo del backup indica lo spazio di archiviazione usato in Azure. L'utilizzo dello spazio di archiviazione viene fornito per:

* Utilizzo dello spazio di archiviazione con ridondanza locale nel cloud associato all'insieme di credenziali
* Utilizzo dello spazio di archiviazione con ridondanza geografica nel cloud associato all'insieme di credenziali

## <a name="manage-your-production-servers"></a>Gestire i server di produzione
Per gestire i server di produzione, fare clic su **Impostazioni**.

In Gestisci fare clic su **Infrastruttura di backup > Server di produzione**.

Il pannello Server di produzione elenca tutti i server di produzione disponibili. Fare clic su un server nell'elenco per aprire i dettagli del server.

![Elementi protetti](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Aprire l'agente di Backup di Azure
Aprire l'**agente di Backup di Microsoft Azure**. Per trovarlo, cercare nel computer *Backup di Microsoft Azure*.

![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Dalle **Azioni** disponibili a destra della console dell'agente di backup è possibile eseguire le attività di gestione seguenti:

* Registra server
* Pianificazione di un backup
* Esegui backup
* Modifica proprietà

![Azioni della console dell'agente Backup di Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Per **ripristinare i dati**, vedere [Ripristinare file da un computer che esegue Windows Server o un client Windows](backup-azure-restore-windows-server.md).
>
>

## <a name="modify-the-backup-schedule"></a>Modificare la pianificazione dei backup
1. Nell'agente di Backup di Microsoft Azure fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. Nella **Pianificazione guidata backup** lasciare selezionata l'opzione **Modifica elementi o tempistica del backup** e fare clic su **Avanti**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Per aggiungere o modificare elementi, nella schermata **Seleziona elementi per backup** fare clic su **Aggiungi elementi**.

    In questa pagina della procedura guidata è anche possibile specificare le **Impostazioni di esclusione** . Per escludere file o tipi di file, leggere la procedura per l'aggiunta di [impostazioni di esclusione](#manage-exclusion-settings).
4. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri (non più di 3 al giorno) o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Per informazioni dettagliate su come specificare la pianificazione del backup, vedere questo [articolo](backup-azure-backup-cloud-as-tape.md).
   >

6. Selezionare i **criteri di conservazione** per la copia di backup e fare clic su **Avanti**.

    ![Elementi per il backup di Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Nella schermata **Conferma** riesaminare le informazioni e fare clic su **Fine**.
8. Al termine della creazione della **pianificazione del backup** da parte della procedura guidata, fare clic su **Chiudi**.

    Dopo aver modificato la protezione, è possibile verificare che i backup vengano attivati correttamente passando alla scheda **Processi** e assicurandosi che le modifiche siano presenti nei processi di backup.

## <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda della rete

L'agente Backup di Azure offre la scheda Limitazione larghezza di banda rete che consente di controllare la modalità d'uso della larghezza di banda della rete durante il trasferimento dei dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione del trasferimento dati si applica alle attività di backup e ripristino.  

Per abilitare la limitazione della larghezza di banda della rete:

1. Nell'**agente di Backup** fare clic su **Modifica proprietà**.
2. Nella scheda **Limitazione larghezza di banda rete selezionare **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Dopo aver abilitato la limitazione, specificare la larghezza di banda consentita per il trasferimento dei dati di backup durante le **Ore lavorative** e le **Ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobyte al secondo (Kbps) e possono arrivare fino a 1023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine per **Ore lavorative**e i giorni della settimana da considerare come giorni lavorativi. Le ore al di fuori delle ore lavorative specificate sono considerate non lavorative.
3. Fare clic su **OK**.

## <a name="manage-exclusion-settings"></a>Gestire le impostazioni di esclusione
1. Aprire l'**agente di Backup di Microsoft Azure**. Per trovarlo, cercare nel computer *Backup di Microsoft Azure*.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Nell'agente di Backup di Microsoft Azure fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. Nella Pianificazione guidata backup lasciare selezionata l'opzione **Modifica elementi o tempistica del backup** e fare clic su **Avanti**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Fare clic su **Impostazioni di esclusione**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Fare clic su **Aggiungi esclusione**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Selezionare il percorso e quindi fare clic su **OK**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Aggiungere l'estensione di file nel campo **Tipo file** .

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Aggiunta di un'estensione mp3

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Per aggiungere un'altra estensione, fare clic su **Aggiungi esclusione** e immettere l'estensione di un altro tipo di file, aggiungendo un'estensione jpeg.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Dopo aver aggiunto tutte le estensioni, fare clic su **OK**.
9. Continuare con la Pianificazione guidata backup facendo clic su **Avanti** fino alla **pagina Conferma**, quindi fare clic su **Fine**.

    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Domande frequenti
**D1. Lo stato del processo di backup risulta completato nell'agente di Backup di Azure. Perché non è visibile immediatamente nel portale?**

R1. C'è un ritardo massimo di 15 minuti tra lo stato del processo di backup visibile nell'agente di Backup di Azure e nel portale di Azure.

**D.2 Quando un processo di backup non riesce, quanto tempo passa prima che venga generato un avviso?**

R.2 Un avviso viene generato entro 20 minuti dall'errore di backup di Azure.

**D3. Esiste un caso in cui non viene inviato un messaggio di posta elettronica se le notifiche sono configurate?**

R3. Di seguito sono indicati i casi in cui la notifica non verrà inviata per ridurre la frequenza degli avvisi:

* Se le notifiche sono configurate su base oraria e un avviso viene generato e risolto entro l'ora
* Il processo viene annullato.
* Secondo processo di backup non riuscito perché è in corso il processo di backup originale.

## <a name="troubleshooting-monitoring-issues"></a>Risoluzione dei problemi di monitoraggio
**Problema:** i processi e/o gli avvisi generati dall'agente di Backup di Azure non vengono visualizzati nel portale.

**Procedura per la risoluzione del problema:** il processo, ```OBRecoveryServicesManagementAgent```, viene usato per inviare i dati dei processi e degli avvisi al servizio Backup di Azure. A volte questo processo può risultare danneggiato o arrestato.

1. Per controllare se il processo si è arrestato, aprire **Gestione attività** e controllare se il processo ```OBRecoveryServicesManagementAgent``` è in esecuzione.
2. Se il processo non è in esecuzione, aprire il **Pannello di controllo** e sfogliare l'elenco dei servizi. Avviare o riavviare **Agente di gestione di Servizi di ripristino di Microsoft Azure**.

    Per altre informazioni, sfogliare i log in:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Ad esempio:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passaggi successivi
* [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
* Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](backup-introduction-to-azure-backup.md)
* Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
