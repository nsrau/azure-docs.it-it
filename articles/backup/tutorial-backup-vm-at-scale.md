---
title: 'Esercitazione: Eseguire il backup di più macchine virtuali di Azure'
description: Questa esercitazione illustra come creare un insieme di credenziali di Servizi di ripristino, definire un criterio di backup e contemporaneamente eseguire il backup di più macchine virtuali.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 76c09cc02a3545d975de5d6d49b396b12f8abb44
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757507"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Usare il portale di Azure per eseguire il backup di più macchine virtuali

Quando si esegue il backup di dati in Azure, i dati vengono archiviati in una risorsa di Azure denominata un insieme di credenziali di Servizi di ripristino. La risorsa dell'insieme di credenziali di Servizi di ripristino è disponibile dal menu Impostazioni della maggior parte dei servizi di Azure. La presenza di un insieme di credenziali di Servizi di ripristino integrato nel menu Impostazioni della maggior parte dei servizi di Azure consente di semplificare il backup dei dati. Tuttavia, lavorare con ogni singola macchina virtuale o database dell'azienda è noioso. Cosa accade se si desidera eseguire il backup dei dati per tutte le macchine virtuali di un reparto o di un'unica posizione? Il backup di più macchine virtuali può essere eseguito facilmente creando un criterio di backup e applicando tale criterio alle macchine virtuali desiderate. In questa esercitazione viene illustrato come:

> [!div class="checklist"]
>
> * Creare un insieme di credenziali di Servizi di ripristino
> * Definire un criterio di backup
> * Applicare un criterio di backup per proteggere più macchine virtuali
> * Attivare un processo di backup su richiesta per le macchine virtuali protette

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

L'insieme di credenziali dei Servizi di ripristino contiene i dati di backup e i criteri di backup applicati alle macchine virtuali protette. Il backup di macchine virtuali è un processo locale. Non è possibile eseguire il backup delle macchine virtuali da una località a un insieme di credenziali di Servizi di ripristino in un'altra località. In tal caso, in ogni località di Azure con macchine virtuali di cui eseguire il backup deve esistere almeno un insieme di credenziali dei Servizi di ripristino.

1. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Selezionare tutti i servizi](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. Nella finestra di dialogo **Tutti i servizi** inserire *Servizi di ripristino*. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare**Insieme di credenziali di Servizi di ripristino**.

    ![Inserire e selezionare le insiemi di credenziali di Servizi di ripristino](./media/tutorial-backup-vm-at-scale/all-services.png)

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

1. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Aggiungi**.

    ![Aggiungere un insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. Nel menu dell'insieme di credenziali dei Servizi di ripristino,

    * digitare *myRecoveryServicesVault* in **Nome**.
    * L'ID di sottoscrizione corrente viene visualizzato in **Sottoscrizione**. Se si hanno sottoscrizioni aggiuntive, è possibile scegliere un'altra sottoscrizione per il nuovo insieme di credenziali.
    * Per **Gruppo di risorse**, selezionare **Usa esistente** e scegliere *myResourceGroup*. Se *myResourceGroup* non esiste, selezionare **Crea nuovo** e digitare *myResourceGroup*.
    * Dal menu a discesa **Percorso**, scegliere *Europa occidentale*.

    ![Valori dell'insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Un insieme di credenziali di Servizi di ripristino deve trovarsi nello stesso percorso delle macchine virtuali da proteggere. Se si hanno macchine virtuali in più aree, creare un insieme di credenziali di Servizi di ripristino in ogni area. Questa esercitazione consente di creare un insieme di credenziali di Servizi di ripristino in *Europa occidentale*, dove *myVM* (macchina virtuale creata con la Guida introduttiva) è stata creata.

1. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, selezionare **Crea**.

    ![Creare l'insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. La creazione dell'insieme di credenziali di Servizi di ripristino può richiedere del tempo. Monitorare le notifiche di stato nell'area **Notifiche** nell'angolo in alto a destra del portale. Dopo essere stato creato, l'insieme di credenziali, sarà visualizzabile nell'insieme di credenziali di Servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

     ![Aggiornare l'elenco dell'insieme di credenziali di backup](./media/tutorial-backup-vm-at-scale/refresh-button.png)

Quando si crea un insieme di credenziali di Servizi di ripristino, per impostazione predefinita l'insieme di credenziali ha un'archiviazione con ridondanza geografica. Per garantire la resilienza dei dati, l'archiviazione con ridondanza geografica replica i dati più volte in due aree di Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Impostare dei criteri di backup per proteggere le macchine virtuali

Dopo aver creato l'insieme di credenziali di Servizi di ripristino, il passaggio successivo consiste nel configurare l'insieme di credenziali per il tipo di dati e impostare i criteri di backup. I criteri di backup determinano la pianificazione relativa alla frequenza e al momento in cui acquisiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino. Per questa esercitazione si supponga che l'azienda sia un complesso sportivo con un hotel, stadio, ristoranti e pertinenze e che si intenda proteggere i dati nelle macchine virtuali. La seguente procedura crea un criterio di backup per i dati finanziari.

1. Dall'elenco degli insiemi di credenziali dei servizi di ripristino selezionare **myRecoveryServicesVault** per aprire il relativo dashboard.

   ![Menu Scenario aperto](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. Selezionare **Backup** nel menu del dashboard dell'insieme di credenziali per aprire il menu Backup.

1. Nel menu Obiettivo di backup, dal menu a discesa **Posizione di esecuzione del carico di lavoro** selezionare *Azure*. Nel menu a discesa **Elementi di cui eseguire il backup** scegliere *Macchina virtuale* e quindi fare selezionare **Backup**.

    Queste azioni preparano l'insieme di credenziali di Servizi di ripristino per l'interazione con una macchina virtuale. Gli insiemi di credenziali di Servizi di ripristino dispongono di un criterio predefinito che crea un punto di recupero ogni giorno e mantiene i punti di ripristino per 30 giorni.

    ![Obiettivo del backup](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Per creare un nuovo criterio, nel menu dei criteri di Backup selezionare *Crea nuovi criteri* nel menu a discesa **Scegliere i criteri di backup**.

    ![Creare nuovi criteri](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Verrà aperto il riquadro **Criteri di backup**. Inserire i dettagli seguenti:
   * In **Nome criterio** digitare *Finance*. Immettere le seguenti modifiche per il criterio di Backup:
   * Per **Frequenza di backup**, impostare il fuso orario su *Fuso cen.* Poiché il complesso sportivo si trova in Texas, il proprietario desidera che il fuso orario sia quello locale. Lasciare la frequenza di backup impostata su Ogni giorno alle 03:30.
   * Per **Conservazione del punto di backup giornaliero**, impostare un periodo di 90 giorni.
   * Per **Conservazione del punto di backup settimanale**, usare il punto di recupero *Lunedì* e mantenerlo per 52 settimane.
   * Per **Conservazione del punto di backup mensile**, usare il punto di recupero dalla prima domenica del mese e mantenerlo per 36 mesi.
   * Deselezionare l'opzione **Conservazione del punto di backup annuale**. Il responsabile del criterio Finance non desidera conservare i dati oltre 36 mesi.
   * Selezionare **OK** per creare il criterio di backup.

     ![Impostazioni dei criteri di backup](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Dopo avere creato il criterio di backup, associarlo alle macchine virtuali.

1. In **Macchine virtuali** selezionare **Aggiungi**.

     ![Aggiungi macchine virtuali](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Verrà aperto il riquadro **Seleziona macchine virtuali**. Selezionare *myVM* e quindi **OK** per distribuire il criterio di backup nelle macchine virtuali.

    Verranno visualizzate tutte le macchine virtuali che si trovano sullo stesso percorso e non sono già associate a un criterio di backup. *myVMH1* e *myVMR1* vengono selezionati per essere associati al criterio *Finance*.

    ![Scegliere le macchine virtuali da proteggere](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Dopo aver scelto le macchine virtuali, selezionare **Abilita backup**.

    Al termine della distribuzione, si riceverà la notifica di completamento dell'operazione.

## <a name="initial-backup"></a>Backup iniziale

È stata abilitata la copia di backup per gli insiemi di credenziali di Servizi di ripristino, ma non è stato creato un backup iniziale. Ai fini di un ripristino di emergenza è consigliabile attivare il primo backup, in modo da proteggere i dati.

Per eseguire un processo di backup su richiesta:

1. Nel dashboard dell'insieme di credenziali selezionare **3** sotto **Elementi di backup** per aprire il menu Elementi di backup.

    ![Elementi di backup](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Si aprirà l'elenco **Elementi di backup**.

1. Nel menu **Elementi di backup** selezionare **Macchina virtuale di Azure** per aprire l'elenco di macchine virtuali associate all'insieme di credenziali.

    ![Elenco di macchine virtuali](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. Si apre l'elenco **Elementi di backup**.

    ![Processo di backup attivato](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. Nell'elenco **Elementi di backup** selezionare i puntini di sospensione ( **...** ) per aprire il menu di scelta rapida.

1. Nel menu di scelta rapida, selezionare **Esegui backup ora**.

    ![Menu di scelta rapida - Selezionare Esegui backup ora](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Si aprirà il menu Esegui backup ora.

1. Nel menu Esegui backup ora immettere l'ultimo giorno di conservazione del punto di ripristino e selezionare **OK**.

    ![Impostare l'ultimo giorno di conservazione del punto di ripristino di Esegui backup ora](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Le notifiche sulla distribuzione consentono di sapere che il processo di backup è stato attivato e che è possibile monitorare lo stato di avanzamento del processo nella pagina Processi di backup. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

    Al termine del processo di backup iniziale è possibile visualizzare lo stato nel menu Processo di backup. Il processo di backup su richiesta ha creato il punto di recupero iniziale per *myVM*. Se si desidera eseguire il backup di altre macchine virtuali, ripetere questi passaggi per ogni macchina virtuale.

    ![Riquadro dei processi di backup](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa esercitazione. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa esercitazione nel portale di Azure.

1. Nel dashboard **myRecoveryServicesVault** selezionare **3** sotto **Elementi di backup** per aprire il menu Elementi di backup.

    ![Aprire il menu Elementi di backup](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. Nel menu **Elementi di backup** selezionare **Macchina virtuale di Azure** per aprire l'elenco di macchine virtuali associate all'insieme di credenziali.

    ![Elenco di macchine virtuali](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Si apre l'elenco **Elementi di backup**.

1. Nel menu **Elementi di backup** selezionare i puntini di sospensione per aprire il menu di scelta rapida.

    ![Nel menu Elementi di backup aprire il menu di scelta rapida](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. Nel menu di scelta rapida selezionare **Interrompi backup** per aprire il menu corrispondente.

    ![Menu Interrompi backup](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. Nel menu **Interrompi Backup** selezionare il menu a discesa superiore e scegliere **Elimina dati di Backup**.

1. Nella finestra di dialogo **Type the name of the Backup item** (Digita nome dell'elemento di backup), digitare *myVM*.

1. Dopo la verifica dell'elemento di backup (viene visualizzato un segno di spunta), il pulsante **Interrompi backup** è abilitato. Selezionare **Interrompi Backup** per arrestare il criterio ed eliminare i punti di ripristino.

    ![Selezionare Interrompi backup per eliminare l'insieme di credenziali](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Gli elementi eliminati vengono conservati nello stato di eliminazione temporanea per 14 giorni. Solo dopo tale periodo è possibile eliminare l'insieme di credenziali. Per altre informazioni, vedere [Eliminare un insieme di credenziali di Servizi di ripristino di Backup di Azure](backup-azure-delete-vault.md).

1. Quando nell'insieme di credenziali non ci sono più elementi, selezionare **Elimina**.

    ![Selezionare Elimina](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Dopo l'eliminazione dell'insieme di credenziali, verrà nuovamente visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il portale di Azure per eseguire le operazioni seguenti:

> [!div class="checklist"]
>
> * Creare un insieme di credenziali di Servizi di ripristino
> * Impostare l'insieme di credenziali per proteggere le macchine virtuali
> * Creare un criterio di backup e conservazione personalizzato
> * Assegnare il criterio di backup per proteggere più macchine virtuali
> * Attivare un backup su richiesta per una macchina virtuale

Proseguire con l'esercitazione successiva sul ripristino di una macchina virtuale di Azure da disco.

> [!div class="nextstepaction"]
> [Ripristino di macchine virtuali con l'interfaccia della riga di comando](./tutorial-restore-disk.md)
