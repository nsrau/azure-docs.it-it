---
title: Gestire server e insiemi di credenziali di Backup di Azure con il modello di distribuzione classica | Microsoft Docs
description: Utilizzare questa esercitazione per imparare a gestire server e insieme di credenziali di Backup di Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: jwhit
editor: tysonn

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jimpark;markgal

---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Gestire server e insiemi di credenziali di Backup di Azure con il modello di distribuzione classica
> [!div class="op_single_selector"]
> * [Gestione risorse](backup-azure-manage-windows-server.md)
> * [Classico](backup-azure-manage-windows-server-classic.md)
> 
> 

In questo articolo è disponibile una panoramica delle attività di gestione dei backup disponibili tramite il portale di Azure classico e l'agente di Backup di Microsoft Azure.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Modello di distribuzione di Gestione risorse

## <a name="management-portal-tasks"></a>Attività del portale di gestione
1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Servizi di ripristino**, quindi fare clic sul nome dell'insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.
   
    ![Servizi di ripristino](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Selezionando le opzioni nella parte superiore della pagina avvio rapido, è possibile visualizzare le attività di gestione disponibili.

![Gestire le schede](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Dashboard
Fare clic su **Dashboard** per visualizzare la panoramica sull'utilizzo del server. La **panoramica sull'utilizzo** include:

* Il numero di istanze di Windows Server registrate nel cloud
* Il numero di macchine virtuali di Azure protette nel cloud
* Lo spazio di archiviazione totale usato in Azure
* Lo stato dei processi recenti

Nella parte inferiore del dashboard è possibile eseguire le operazioni seguenti:

* **Gestisci certificato** : se è stato usato un certificato per registrare il server, usare questa azione per aggiornare il certificato. Se si usano le credenziali di insieme, non usare la funzionalità **Gestisci certificato**.
* **Elimina** : elimina l'insieme di credenziali di backup corrente. Se un insieme di credenziali per il backup non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. **Elimina** eliminazione viene abilitata solo dopo l'eliminazione di tutti i server registrati dall'insieme di credenziali.

![Eseguire un backup delle attività del dashboard](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Elementi registrati
Fare clic su **Elementi registrati** per visualizzare i nomi dei server registrati nell'insieme di credenziali.

![Elementi registrati](./media/backup-azure-manage-windows-server-classic/registered-items.png)

Il valore predefinito del filtro **Tipo** è Macchina virtuale di Azure. Per visualizzare i nomi dei server registrati nell'insieme di credenziali, scegliere **Windows Server** dal menu a discesa.

Da questo punto è possibile eseguire le attività seguenti:

* **Consenti ripetizione della registrazione**: quando questa opzione è selezionata per un server, è possibile usare la **Registrazione in linea** nell'agente di Backup di Microsoft Azure per registrare nuovamente il server con l'insieme di credenziali di backup. La ri-registrazione di un server può essere necessaria per un errore nel certificato, oppure se è stato necessario ricreare il server.
* **Eliminare** - Consente di eliminare un server dall'insieme di credenziali per il backup. Tutti i dati archiviati associati al server verranno eliminati immediatamente.
  
    ![Attività di Elementi registrati](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Elementi protetti
Fare clic su **Elementi protetti** per visualizzare gli elementi di cui è stato eseguito il backup dai server.

![Elementi protetti](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configurare
Nella scheda **Configura** è possibile selezionare l'opzione di ridondanza di archiviazione appropriata. È consigliabile selezionare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione di un insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali.

> [!WARNING]
> Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.
> 
> 

![Configurare](./media/backup-azure-manage-windows-server-classic/configure.png)

Per altre informazioni sulla [ridondanza di archiviazione](../storage/storage-redundancy.md), vedere questo articolo.

## <a name="microsoft-azure-backup-agent-tasks"></a>Attività dell'agente Backup di Microsoft Azure
### <a name="console"></a>Console
Aprire l'**agente di Backup di Microsoft Azure**. Per trovarlo, cercare nel computer *Backup di Microsoft Azure*.

![Agente di Backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Dalle **Azioni** disponibili a destra della console dell'agente di backup è possibile eseguire le attività di gestione seguenti:

* Registra server
* Pianificazione di un backup
* Esegui backup
* Modifica proprietà

![Azioni della console dell'agente](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Per **ripristinare i dati**, vedere [Ripristinare file da un computer che esegue Windows Server o un client Windows](backup-azure-restore-windows-server.md).
> 
> 

### <a name="modify-an-existing-backup"></a>Modificare un backup esistente
1. Nell'agente di Backup di Microsoft Azure fare clic su **Pianifica backup**.
   
    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. Nella **Pianificazione guidata backup** lasciare selezionata l'opzione **Modifica elementi o tempistica del backup** e fare clic su **Avanti**.
   
    ![Modificare un backup pianificato](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Per aggiungere o modificare elementi, nella schermata **Seleziona elementi per backup** fare clic su **Aggiungi elementi**.
   
    In questa pagina della procedura guidata è anche possibile specificare le **Impostazioni di esclusione** . Per escludere file o tipi di file, leggere la procedura per l'aggiunta di [impostazioni di esclusione](#exclusion-settings).
4. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.
   
    ![Aggiungi elementi](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Specificare la **pianificazione del backup** e fare clic su **Avanti**.
   
    È possibile pianificare backup giornalieri (non più di 3 al giorno) o settimanali.
   
    ![Specificare la pianificazione dei backup](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)
   
   > [!NOTE]
   > Per informazioni dettagliate su come specificare la pianificazione del backup, vedere questo [articolo](backup-azure-backup-cloud-as-tape.md).
   > 
   > 
6. Selezionare i **criteri di conservazione** per la copia di backup e fare clic su **Avanti**.
   
    ![Selezionare i criteri di conservazione](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. Nella schermata **Conferma** riesaminare le informazioni e fare clic su **Fine**.
8. Al termine della creazione della **pianificazione del backup** da parte della procedura guidata, fare clic su **Chiudi**.
   
    Dopo aver modificato la protezione, è possibile verificare che i backup vengano attivati correttamente passando alla scheda **Processi** e assicurandosi che le modifiche siano presenti nei processi di backup.

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda della rete
L'agente Backup di Azure offre la scheda Limitazione larghezza di banda rete che consente di controllare la modalità d'uso della larghezza di banda della rete durante il trasferimento dei dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione del trasferimento dati si applica alle attività di backup e ripristino.  

Per abilitare la limitazione della larghezza di banda della rete:

1. Nell'**agente di Backup** fare clic su **Modifica proprietà**.
2. Selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup** .
   
    ![Limitazione della larghezza di banda della rete](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Dopo aver abilitato la limitazione, specificare la larghezza di banda consentita per il trasferimento dei dati di backup durante le **Ore lavorative** e le **Ore non lavorative**.
   
    I valori della larghezza di banda partono da 512 kilobyte al secondo (Kbps) e possono arrivare fino a 1023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine per **Ore lavorative**e i giorni della settimana da considerare come giorni lavorativi. Le ore al di fuori delle ore lavorative specificate sono considerate non lavorative.
4. Fare clic su **OK**.

## <a name="exclusion-settings"></a>Impostazioni di esclusione
1. Aprire l'**agente di Backup di Microsoft Azure**. Per trovarlo, cercare nel computer *Backup di Microsoft Azure*.
   
    ![Aprire l'agente di backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. Nell'agente di Backup di Microsoft Azure fare clic su **Pianifica backup**.
   
    ![Pianificare un backup di Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. Nella Pianificazione guidata backup lasciare selezionata l'opzione **Modifica elementi o tempistica del backup** e fare clic su **Avanti**.
   
    ![Modificare una pianificazione](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Fare clic su **Impostazioni di esclusione**.
   
    ![Selezionare elementi da escludere](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Fare clic su **Aggiungi esclusione**.
   
    ![Aggiungere esclusioni](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Selezionare il percorso e quindi fare clic su **OK**.
   
    ![Selezionare una posizione per l'esclusione](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Aggiungere l'estensione di file nel campo **Tipo file** .
   
    ![Escludere per tipo di file](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)
   
    Aggiunta di un'estensione mp3
   
    ![Esempio di tipo di file](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)
   
    Per aggiungere un'altra estensione, fare clic su **Aggiungi esclusione** e immettere l'estensione di un altro tipo di file, aggiungendo un'estensione jpeg.
   
    ![Altro esempio di tipo di file](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Dopo aver aggiunto tutte le estensioni, fare clic su **OK**.
9. Continuare con la Pianificazione guidata backup facendo clic su **Avanti** fino alla **pagina Conferma**, quindi fare clic su **Fine**.
   
    ![Conferma dell'esclusione](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Passaggi successivi
* [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
* Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](backup-introduction-to-azure-backup.md)
* Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!--HONumber=Oct16_HO2-->


