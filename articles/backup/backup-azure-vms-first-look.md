---
title: 'Primo approccio: Proteggere le VM di Azure con un insieme di credenziali per il backup | Documentazione Microsoft'
description: Proteggere le VM di Azure con un insieme di credenziali per il backup. Esercitazione che illustra come creare un insieme di credenziali, registrare macchine virtuali, creare criteri e proteggere macchine virtuali in Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/15/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: 9eddde0df61ac7dde9b24f973e7eec786cd48f24
ms.openlocfilehash: b8f62d1fab4b9132f35e2fab632968cf001c832d


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Primo approccio: Backup di macchine virtuali di Azure
> [!div class="op_single_selector"]
> * [Proteggere le VM con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md)
> * [Proteggere le VM di Azure con un insieme di credenziali per il backup](backup-azure-vms-first-look.md)
>
>

Questa esercitazione illustra i passaggi per eseguire il backup di una macchina virtuale di Azure in un insieme di credenziali di backup in Azure. Questo articolo descrive il modello classico o il modello di distribuzione di Service Manager per il backup delle macchine virtuali. Per informazioni sul backup di una VM in un insieme di credenziali dei servizi di ripristino appartenente a un gruppo di risorse, vedere [Primo approccio: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md). Per completare questa esercitazione, è necessario rispettare i prerequisiti seguenti:

* È stata creata una VM nella sottoscrizione di Azure.
* La VM può connettersi agli indirizzi IP pubblici di Azure. Per altre informazioni, vedere [Connettività di rete](backup-azure-vms-prepare.md#network-connectivity).

Ecco i cinque passaggi principali per eseguire il backup di una VM:  

![step-one](./media/backup-azure-vms-first-look/step-one.png) Creare un insieme di credenziali per il backup o identificarne uno esistente. <br/>
![step-two](./media/backup-azure-vms-first-look/step-two.png) Usare il portale di Azure classico per trovare e registrare le macchine virtuali. <br/>
![step-three](./media/backup-azure-vms-first-look/step-three.png) Installare l'agente di macchine virtuali. <br/>
![step-four](./media/backup-azure-vms-first-look/step-four.png) Creare i criteri per proteggere le macchine virtuali. <br/>
![step-five](./media/backup-azure-vms-first-look/step-five.png) Eseguire il backup.

![Panoramica generale del processo di backup VM](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../resource-manager-deployment-model.md). Questa esercitazione si applica alle VM che possono essere create nel portale di Azure classico. Il servizio Backup di Azure supporta le VM basate su Resource Manager. Per informazioni dettagliate sul backup di VM in un insieme di credenziali dei servizi di ripristino, vedere [Primo approccio: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md).
>
>

## <a name="step-1-create-a-backup-vault-for-a-vm"></a>Passaggio 1: Creare un insieme di credenziali di backup per una macchina virtuale
Un insieme di credenziali di backup è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali di backup contiene anche i criteri di backup applicati alle macchine virtuali di cui viene eseguito il backup.

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com/).
2. Nell'angolo in basso a sinistra del portale di Azure fare clic su **Nuovo**

    ![Fare clic sul menu Nuovo](./media/backup-azure-vms-first-look/new-button.png)
3. Nella procedura guidata Creazione rapida fare clic su **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**.

    ![Creare un insieme di credenziali per il backup](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    La procedura guidata richiede di specificare **Nome** e **Area**. Se si amministrano più sottoscrizioni, viene visualizzata una finestra di dialogo per la scelta della sottoscrizione.
4. Nel campo **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure.
5. In **Region**selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali **deve** trovarsi nella stessa area delle macchine virtuali da proteggere.

    Se si non è certi dell'area in cui si trova la VM, chiudere la procedura guidata e fare clic su **Macchine virtuali** nell'elenco di servizi di Azure. La colonna Località indica il nome dell'area. Se si hanno macchine virtuali in più aree, creare un insieme di credenziali per il backup in ogni area.
6. Se la procedura guidata non include una finestra di dialogo **Sottoscrizione** , andare al passaggio successivo. Se si usano più sottoscrizioni, selezionarne una da associare al nuovo insieme di credenziali per il backup.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-first-look/backup-vaultcreate.png)
7. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Un messaggio conferma che l'insieme di credenziali è stato creato. È elencato nella pagina **Servizi di ripristino** come **Attivo**.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-first-look/create-vault-demo-success.png)
8. Nell'elenco di insiemi di credenziali nella pagina **Servizi di ripristino** selezionare quello appena creato per visualizzare la pagina **Avvio rapido**.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look/active-vault-demo.png)
9. Nella pagina **Avvio rapido** fare clic su **Configura** per visualizzare l'opzione di replica di archiviazione.
    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look/configure-storage.png)
10. In **Replica archiviazione** scegliere l'opzione di replica per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario. scegliere l'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con ridondanza geografica e con ridondanza locale, vedere la panoramica [Replica di Archiviazione di Azure](../storage/storage-redundancy.md).

Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la macchina virtuale all'insieme di credenziali. Per iniziare l'associazione, trovare e registrare le macchine virtuali di Azure.

## <a name="step-2-discover-and-register-azure-virtual-machines"></a>Passaggio 2: Trovare e registrare le macchine virtuali di Azure
Prima di registrare la VM con un insieme di credenziali, eseguire il processo di individuazione per identificare eventuali VM nuove. Verrà restituito un elenco delle macchine virtuali disponibili nella sottoscrizione, insieme ad altre informazioni come il nome del servizio cloud e l'area.

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com/)
2. Nel portale di Azure classico fare clic su **Servizi di ripristino** per aprire l'elenco degli insiemi di credenziali dei servizi di ripristino.
    ![Selezionare il carico di lavoro](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. Nell'elenco di insiemi di credenziali selezionare quello da usare per il backup di una VM.

    Quando si seleziona l'insieme di credenziali, viene visualizzata la pagina **Avvio rapido** .
4. Nel menu dell'insieme di credenziali fare clic su **Elementi registrati**.

    ![Selezionare il carico di lavoro](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. Scegliere **Macchina virtuale di Azure** dal menu **Tipo**.

    ![Selezionare il carico di lavoro](./media/backup-azure-vms/discovery-select-workload.png)
6. Fare clic su **INDIVIDUA** nella parte inferiore della pagina.
    ![Pulsante Individua](./media/backup-azure-vms/discover-button-only.png)

    Il processo di individuazione può richiedere alcuni minuti mentre le macchine virtuali vengono elencate in formato tabulare. Nella parte inferiore della schermata è presente una notifica che indica che il processo è in esecuzione.

    ![Individuare le VM](./media/backup-azure-vms/discovering-vms.png)

    Al termine del processo, la notifica cambia.

    ![Individuazione completata](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Fare clic su **REGISTRA** nella parte inferiore della pagina.
    ![Pulsante Registra](./media/backup-azure-vms-first-look/register-icon.png)
8. Nel menu di scelta rapida **Registra elementi** selezionare le macchine virtuali da registrare.

   > [!TIP]
   > È possibile registrare più macchine virtuali contemporaneamente.
   >
   >

    Viene creato un processo per ogni macchina virtuale selezionata.
9. Fare clic su **Visualizza processo** nella notifica per passare alla pagina **Processi**.

    ![Registrare il processo](./media/backup-azure-vms/register-create-job.png)

    La macchina virtuale viene visualizzata anche nell'elenco di elementi registrati insieme allo stato dell'operazione di registrazione.

    ![Registering status 1](./media/backup-azure-vms/register-status01.png)

    Al termine dell'operazione, lo stato diventerà *Registrazione completata* per riflettere la modifica.

    ![Registration status 2](./media/backup-azure-vms/register-status02.png)

## <a name="step-3-install-the-vm-agent-on-the-virtual-machine"></a>Passaggio 3: Installare l'agente di macchine virtuali nella macchina virtuale
Per il funzionamento dell'estensione di backup, l'agente di macchine virtuali deve essere installato nella macchina virtuale di Azure. Se la VM è stata creata dalla raccolta di Azure, l'agente di macchine virtuali è già installato. È possibile passare alla [protezione delle VM](backup-azure-vms-first-look.md#step-4---create-the-backup-policy).

Se la migrazione della VM è stata eseguita da un data center locale, l'agente di macchine virtuali non è probabilmente installato nella VM. Prima di procedere alla protezione della VM, è necessario installare l'agente di macchine virtuali. Per informazioni dettagliate sull'installazione dell'agente di macchine virtuali, vedere la [sezione Agente di macchine virtuali dell'articolo sul backup di macchine virtuali](backup-azure-vms-prepare.md#vm-agent).

## <a name="step-4-create-the-backup-policy"></a>Passaggio 4: Creare i criteri di backup
Prima attivare il processo di backup iniziale, impostare la pianificazione per l'acquisizione degli snapshot di backup. La pianificazione per l'acquisizione degli snapshot di backup e la durata di conservazione di questi snapshot costituiscono i criteri di backup. Le informazioni sul periodo di conservazione si basano sullo schema di rotazione dei backup GFS (Grandfather-Father-Son).

1. Passare all'insieme di credenziali per il backup disponibile in **Servizi di ripristino** nel portale di Azure classico e fare clic su **Elementi registrati**.
2. Selezionare **Macchina virtuale di Azure** dal menu a discesa.

    ![Select workload in portal](./media/backup-azure-vms/select-workload.png)
3. Fare clic su **PROTEGGI** in basso nella pagina.
    ![Fare clic su Proteggi](./media/backup-azure-vms-first-look/protect-icon.png)

    Verrà visualizzata la procedura guidata **Proteggi elementi** , che elenca *solo* le macchine virtuali registrate e non protette.

    ![Configurare la protezione su vasta scala](./media/backup-azure-vms/protect-at-scale.png)
4. Selezionare le macchine virtuali da proteggere.

    Se sono presenti due o più macchine virtuali con lo stesso nome, usare il servizio cloud per distinguerle.
5. Nel menu **Configura protezione** selezionare i criteri esistenti o crearne di nuovi per proteggere le macchine virtuali identificate.

    Ai nuovi insiemi di credenziali di backup sono associati criteri predefiniti. Questi criteri acquisiscono uno snapshot giornaliero ogni sera, che viene mantenuto per 30 giorni. Ai singoli criteri di backup possono essere associate più macchine virtuali. Una macchina virtuale può tuttavia essere associata a un solo criterio alla volta.

    ![Proteggere con nuovi criteri](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > I criteri di backup includono uno schema di conservazione per i backup pianificati. Se sono stati selezionati criteri di backup esistenti, non sarà possibile modificare le opzioni di conservazione nel passaggio successivo.
   >
   >
6. In **Intervallo conservazione** definire l'ambito giornaliero, settimanale, mensile e annuale per i punti di backup specifici.

    ![Virtual machine is backed up with recovery point](./media/backup-azure-vms/long-term-retention.png)

    I criteri di conservazione specificano il periodo di tempo per l'archiviazione di una copia di backup. È possibile specificare criteri di conservazione diversi in base alla momento in cui viene eseguito il backup.
7. Fare clic su **Processi** per visualizzare l'elenco dei processi in **Configura protezione**.

    ![Configure protection job](./media/backup-azure-vms/protect-configureprotection.png)

    Dopo aver stabilito i criteri, andare al passaggio successivo ed eseguire il backup iniziale.

## <a name="step-5-initial-backup"></a>Passaggio 5: Backup iniziale
Dopo aver protetto la macchina virtuale con i criteri specificati, è possibile visualizzare la relazione nella scheda **Elementi protetti** . Finché non viene eseguito il backup iniziale, **Stato di protezione** indica **Protetto (backup iniziale in sospeso)**. Per impostazione predefinita, il primo backup pianificato è il *backup iniziale*.

![Backup in sospeso](./media/backup-azure-vms-first-look/protection-pending-border.png)

Per avviare il backup iniziale:

1. In basso nella pagina **Elementi protetti** fare clic su **Esegui backup ora**.
    ![Icona Esegui backup ora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Il servizio Backup di Azure crea un processo di backup per l'operazione di backup iniziale.
2. Fare clic sulla scheda **Processi** per visualizzare l'elenco dei processi.

    ![Backup in progress](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Al termine del backup iniziale, lo stato della macchina virtuale nella scheda **Elementi protetti** sarà *Protetto*.

    ![Virtual machine is backed up with recovery point](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > Il backup di macchine virtuali è un processo locale. Non è possibile eseguire il backup di macchine virtuali di un'area in un insieme di credenziali per il backup in un'altra area. Di conseguenza, per ogni area di Azure in cui sono presenti VM per cui deve essere eseguito il backup, è necessario creare almeno un insieme di credenziali per il backup in quell'area.
   >
   >

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato eseguito il backup di una macchina virtuale, sono disponibili diversi passaggi successivi interessanti. Il passaggio più logico consiste nell'acquisire familiarità con il ripristino dei dati in una VM. Esistono tuttavia attività di gestione che aiutano a comprendere come proteggere i dati e ridurre al minimo i costi.

* [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-restore-vms.md)
* [Guida alla risoluzione dei problemi](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).



<!--HONumber=Nov16_HO2-->


