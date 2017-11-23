---
title: Gestire e monitorare i backup della macchina virtuale di Azure | Documentazione Microsoft
description: Informazioni su come gestire e monitorare i backup della macchina virtuale di Azure
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3d3de79c7f2465791ec68f850df2fc6317880f9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Gestire i processi di Backup di Azure comuni e attivare gli avvisi nel portale classico
> [!div class="op_single_selector"]
> * [Gestire i backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
> * [Gestire i backup delle macchine virtuali classiche](backup-azure-manage-vms-classic.md)
>
>

In questo articolo vengono fornite informazioni sulle comuni attività di gestione e monitoraggio per le macchine virtuali modello classico protette in Azure.  

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Per informazioni dettagliate sull'utilizzo delle macchine virtuali con il modello di distribuzione classica, vedere [Preparare l'ambiente per il backup di macchine virtuali di Azure](backup-azure-vms-prepare.md) .
>
> [!IMPORTANT]
>A partire da marzo 2017, non è più possibile usare il portale classico per creare insiemi di credenziali di backup.
>
> È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino.<br/> Dopo il 30 novembre 2017 non sarà possibile usare PowerShell per creare insiemi di credenziali di backup. **Entro il 30 novembre 2017**:
>- Tutti gli insiemi di credenziali di backup rimanenti verranno aggiornati automaticamente a insiemi di credenziali dei servizi di ripristino.
>- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.

## <a name="manage-protected-virtual-machines"></a>Gestire le macchine virtuali protette
Per gestire le macchine virtuali protette:

1. Per visualizzare e gestire le impostazioni di backup per una macchina virtuale, fare clic sulla scheda **Elementi protetti** .
2. Fare clic sul nome di un elemento protetto per visualizzare la scheda **Dettagli backup** contenente le informazioni sull'ultimo backup.

    ![Backup di una macchina virtuale](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Per visualizzare e gestire le impostazioni dei criteri di backup per una macchina virtuale fare clic sulla scheda **Criteri** .

    ![Criteri per una macchina virtuale](./media/backup-azure-manage-vms/manage-policy-settings.png)

    Nella scheda **Criteri di backup** vengono visualizzati i criteri esistenti. È possibile modificare i criteri in base alle esigenze. Se è necessario creare un nuovo criterio, fare clic su **Crea** nella pagina **Criteri**. Si noti che un criterio può essere rimosso solo se non ha alcuna macchina virtuale associata.

    ![Criteri per una macchina virtuale](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. È possibile ottenere altre informazioni sulle azioni o sullo stato di una macchina virtuale nella pagina **Processi** . Fare clic su un processo nell'elenco per ottenere informazioni dettagliate oppure filtrare i processi per una macchina virtuale specifica.

    ![Processi](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Backup su richiesta di una macchina virtuale
È possibile eseguire il backup su richiesta di una macchina virtuale dopo averla configurata per la protezione. Se il backup iniziale è in sospeso per la macchina virtuale, il backup su richiesta creerà una copia completa della macchina virtuale nell'insieme di credenziali di backup di Azure. Se il primo backup è stato completato, il backup su richiesta invierà all'insieme di credenziali di Backup di Azure solo le modifiche effettuate dopo il backup precedente.

> [!NOTE]
> Il periodo di conservazione dei dati di un backup su richiesta è impostato al valore specificato per la conservazione giornaliera nei criteri di conservazione corrispondenti alla macchina virtuale.  
>
>

Per eseguire un backup su richiesta di una macchina virtuale:

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come **Tipo**, se non è già selezionata, quindi fare clic sul pulsante **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)
2. Selezionare la macchina virtuale su cui si vuole eseguire un backup su richiesta e fare clic sul pulsante **Esegui backup ora** nella parte inferiore della pagina.

    ![Esegui backup](./media/backup-azure-manage-vms/backup-now.png)

    Verrà creato un processo di backup nella macchina virtuale selezionata. L'intervallo di conservazione del punto di ripristino creato tramite questo processo sarà uguale a quello specificato nei criteri associati alla macchina virtuale.

    ![Creazione del processo di backup](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > Per visualizzare i criteri associati a una macchina virtuale, eseguire il drill-down nella macchina virtuale nella pagina **Elementi protetti** e passare alla scheda dei criteri di backup.
   >
   >
3. Dopo la creazione del processo, è possibile fare clic sul pulsante **Visualizza processo** sulla barra degli strumenti per visualizzare il processo corrispondente nella pagina dei processi.

    ![Processo di backup creato](./media/backup-azure-manage-vms/created-job.png)
4. Dopo il completamento corretto del processo, verrà creato un punto di ripristino che potrà essere usato per ripristinare la macchina virtuale. Ciò incrementerà anche di 1 il valore della colonna del punto di ripristino nella pagina **Elementi protetti** .

## <a name="stop-protecting-virtual-machines"></a>Arrestare la protezione delle macchine virtuali
È possibile scegliere di arrestare i backup futuri di una macchina virtuale con le opzioni seguenti:

* Mantenere i dati di backup associati alla macchina virtuale nell'insieme di credenziali di Backup di Azure.
* Eliminare i dati di backup associati alla macchina virtuale.

Se si è scelto di conservare i dati di backup associati alla macchina virtuale, è possibile usarli per ripristinare la macchina virtuale. Per i dettagli relativi ai prezzi per queste macchine virtuali, fare clic [qui](https://azure.microsoft.com/pricing/details/backup/).

Per arrestare la protezione per una macchina virtuale:

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come tipo di filtro, se non è già selezionata, quindi fare clic sul pulsante **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)
2. Selezionare la macchina virtuale e fare clic su **Arresta protezione** nella parte inferiore della pagina.

    ![Arresta protezione](./media/backup-azure-manage-vms/stop-protection.png)
3. Per impostazione predefinita, il backup di Azure non elimina i dati di backup associati alla macchina virtuale.

    ![Conferma arresto protezione](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Per eliminare i dati di backup, selezionare la casella di controllo.

    ![Casella di controllo](./media/backup-azure-manage-vms/checkbox.png)

    Selezionare un motivo per l'arresto del backup. Benché ciò sia facoltativo, specificando un motivo si consentirà a Backup di Azure di valutare i commenti e i suggerimenti e di assegnare priorità idonee agli scenari dei clienti.
4. Fare clic su **Invia** per inviare il processo **Arresta protezione**. Fare clic su **Visualizza processo** per visualizzare il processo corrispondente nella pagina **Processi**.

    ![Arresta protezione](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se non è stata selezionata l'opzione **Elimina i dati di backup associati** durante la procedura guidata **Arresta protezione**, dopo il completamento del processo lo stato della protezione verrà cambiato in **Protezione arrestata**. I dati rimangono in Azure Backup finché non vengono eliminati esplicitamente. È comunque possibile eliminare i dati selezionando la macchina virtuale nella pagina **Elementi protetti** e facendo clic su **Elimina**.

    ![Protezione arrestata](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se è stata selezionata l'opzione **Elimina i dati di backup associati**, la macchina virtuale non sarà inclusa nella pagina **Elementi protetti**.

## <a name="re-protect-virtual-machine"></a>Riattivare la protezione della macchina virtuale
Se non è stata selezionata l'opzione **Elimina i dati di backup associati** in **Arresta protezione**, sarà possibile riattivare la protezione della macchina virtuale eseguendo una procedura analoga al backup di macchine virtuali registrate. Dopo la protezione, i dati di backup di questa macchina virtuale verranno conservati prima dell'arresto della protezione e verranno creati punti di ripristino dopo la riattivazione della protezione.

Dopo la riattivazione della protezione, lo stato di protezione della macchina virtuale verrà cambiato in **Protetto** se sono presenti punti di ripristino prima di **Arresta protezione**.

  ![Protezione VM riattivata](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Quando si riattiva la protezione della macchina virtuale, è possibile scegliere un criterio diverso rispetto ai criteri con cui la macchina virtuale è stata protetta inizialmente.
>
>

## <a name="unregister-virtual-machines"></a>Annullare la registrazione di macchine virtuali
Per rimuovere la macchina virtuale dall'insieme di credenziali per il backup:

1. Fare clic sul pulsante **ANNULLA REGISTRAZIONE** nella parte inferiore della pagina.

    ![Disabilita protezione](./media/backup-azure-manage-vms/unregister-button.png)

    Verrà visualizzata una notifica di tipo avviso popup nella parte inferiore della schermata di richiesta di conferma. Fare clic su **Sì** per continuare.

    ![Disabilita protezione](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Eliminare i dati di backup
È possibile eliminare i dati di backup associati a una macchina virtuale in due modi:

* Durante il processo Arresta protezione
* Dopo il completamento del processo Arresta protezione in una macchina virtuale

Per eliminare i dati di backup in una macchina virtuale con stato *Protezione arrestata* dopo il completamento corretto di un processo **Arresta backup** :

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come *Tipo*, quindi fare clic sul pulsante **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)
2. Selezionare la macchina virtuale. Lo stato della macchina virtuale sarà **Protezione arrestata** .

    ![Protezione arrestata](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Fare clic sul pulsante **ELIMINA** nella parte inferiore della pagina.

    ![Elimina backup](./media/backup-azure-manage-vms/delete-backup.png)
4. Nella procedura guidata **Elimina dati di backup** selezionare un motivo per l'eliminazione dei dati di backup (opzione consigliata) e fare clic su **Invia**.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Verrà creato un processo per l'eliminazione dei dati di backup di una macchina virtuale selezionata. Fare clic su **Visualizza processo** per visualizzare il processo corrispondente nella pagina Processi.

    ![Eliminazione dei dati completata](./media/backup-azure-manage-vms/delete-data-success.png)

    Dopo il completamento del processo, la voce corrispondente alla macchina virtuale verrà rimossa dalla pagina **Elementi protetti** .

## <a name="dashboard"></a>Dashboard
Nella pagina **Dashboard** è possibile esaminare le informazioni sulle macchine virtuali di Azure, le relative risorse di archiviazione e i processi associati nelle ultime 24 ore. È possibile visualizzare lo stato del backup ed eventuali errori di backup associati.

![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> I valori nel dashboard vengono aggiornati ogni 24 ore.
>
>

## <a name="auditing-operations"></a>Operazioni di controllo
Backup di Azure consente di esaminare i "log operazioni" delle operazioni di backup attivate dal cliente, per vedere più facilmente e con esattezza quali operazioni di gestione sono state eseguite nell'insieme di credenziali per il backup. I log operazioni offrono un ottimo supporto per i controlli e le relazioni finali sulle operazioni di backup.

Le operazioni seguenti vengono registrate nei log operazioni:

* Registra
* Unregister 
* Configura protezione
* Backup (sia backup pianificato che su richiesta tramite BackupNow)
* Ripristino
* Arresta protezione
* Elimina dati di backup
* Aggiungi criteri
* Elimina criteri
* Aggiorna criteri
* Annulla processo

Per visualizzare i log operazioni corrispondenti all'insieme di credenziali per il backup:

1. Nel portale di Azure passare a **Servizi di gestione** e quindi fare clic sulla scheda **Log operazioni**.

    ![Log delle operazioni](./media/backup-azure-manage-vms/ops-logs.png)
2. Nell'area dei filtri selezionare **Backup** come *Tipo*, specificare il nome dell'insieme di credenziali per il backup in *nome servizio* e fare clic su **Invia**.

    ![Filtro dei log operazioni](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. Nei log operazioni selezionare un'operazione qualsiasi e fare clic su **Dettagli** per visualizzare le informazioni corrispondenti a un'operazione.

    ![Log operazioni: recupero dei dettagli](./media/backup-azure-manage-vms/ops-logs-details.png)

    La procedura guidata **Dettagli** include informazioni su operazione attivate, ID processo, risorsa nella quale è stata attivata l'operazione e la relativa data di inizio.

    ![Dettagli operazione](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Notifiche di avviso
Nel portale è possibile ottenere notifiche di avviso personalizzate per i processi. Questo risultato si ottiene definendo le regole di avviso basate su PowerShell negli eventi dei log operativi. Si raccomanda di utilizzare *PowerShell 1.3.0 o versioni successive*.

Per definire una notifica di avviso personalizzata per gli errori di backup, ecco un comando di esempio:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: è possibile ottenere queste informazioni dalla finestra popup Log operazioni come descritto nella sezione precedente. ResourceUri nella finestra popup dei dettagli di un'operazione è il valore di ResourceId da fornire per questo cmdlet.

**OperationName**: questa proprietà è in questo formato "Microsoft.Backup/backupvault/<EventName>" dove EventName può essere uno dei seguenti eventi: Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy

**Status**: i valori supportati sono Started, Succeeded e Failed.

**ResourceGroup**: gruppo di risorse della risorsa in cui viene attivata l'operazione. È possibile ottenere queste informazioni dal valore di ResourceId. Il valore tra i campi */resourceGroups/* e */providers/* nel valore ResourceId è il valore per ResourceGroup.

**Name**: nome della regola di avviso.

**CustomEmail**: specificare l'indirizzo di posta elettronica personalizzato a cui dovrà essere inviata la notifica di avviso.

**SendToServiceOwners**: questa opzione invia la notifica di avviso a tutti gli amministratori e i coamministratori della sottoscrizione. Può essere usata nel cmdlet **New AzureRmAlertRuleEmail** .

### <a name="limitations-on-alerts"></a>Limitazioni per gli avvisi
Gli avvisi basati su eventi sono soggetti alle limitazioni seguenti:

1. Gli avvisi vengono attivati in tutte le macchine virtuali nell'insieme di credenziali per il backup. Non è possibile personalizzare l'impostazione per ricevere avvisi per un set specifico di macchine virtuali in un insieme di credenziali per il backup.
2. Questa funzionalità è in anteprima. [Altre informazioni](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Si riceveranno avvisi da "alerts-noreply@mail.windowsazure.com". Attualmente non è possibile modificare il mittente del messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi
* [Ripristinare una macchina virtuale](backup-azure-restore-vms.md)
