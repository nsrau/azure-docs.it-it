---

title: Uso di una macchina virtuale Linux per la risoluzione dei problemi nel portale di Azure | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle macchine virtuali Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: c96ff625c3e83f6fc9057f1163c877e8e0aed5e3
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---

# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Risolvere i problemi relativi a una macchina virtuale Linux collegando il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
Se nella VM Linux viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire dei passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è una voce non valida in `/etc/fstab` che impedisce il corretto avvio della macchina virtuale. Questo articolo illustra come usare il portale di Azure per connettere il disco rigido virtuale a un'altra VM Linux per risolvere eventuali errori e quindi ricreare la VM originale.

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Eliminare la macchina virtuale su cui si riscontrano i problemi, mantenendo i dischi rigidi virtuali.
2. Collegare e montare il disco rigido virtuale in un'altra VM Linux per risolvere i problemi riscontrati.
3. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
4. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
5. Creare una VM usando il disco rigido virtuale originale.


## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
Esaminare la diagnostica di avvio e la schermata della VM per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Un esempio comune è una voce non valida in `/etc/fstab`, oppure l'eliminazione o lo spostamento di un disco rigido virtuale sottostante.

Nel portale, selezionare la macchina virtuale e quindi scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi**. Fare clic su **Diagnostica di avvio** per i visualizzare i messaggi della console originati dalla VM. Esaminare i registri della console per cercare di determinare la causa del problema riscontrato nella macchina virtuale. L'esempio seguente mostra una macchina virtuale bloccata in modalità di manutenzione che richiede l'intervento manuale:

![Visualizzazione dei registri della console nella diagnostica di avvio della macchina virtuale](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

È anche possibile fare clic su **Schermata** nella parte superiore del log della diagnostica di avvio per scaricare una schermata della macchina virtuale.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualizzare i dettagli del disco rigido virtuale esistente
Prima di collegare il disco rigido virtuale a un'altra macchina virtuale, è necessario identificare il nome del disco rigido virtuale. 

Selezionare il gruppo di risorse dal portale, quindi selezionare il proprio account di archiviazione. Fare clic su **BLOB**, come nell'esempio seguente:

![Selezionare il BLOB di archiviazione](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

In genere è presente un contenitore denominato **vhds** che contiene i dischi rigidi virtuali. Selezionare il contenitore per visualizzare un elenco dei dischi rigidi virtuali. Annotare il nome del disco rigido virtuale desiderato. Il prefisso è in genere il nome della propria macchina virtuale:

![Identificare il disco rigido virtuale nel contenitore di archiviazione](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Selezionare il disco rigido virtuale esistente dall'elenco e copiare l'URL, che verrà usato nei passaggi seguenti:

![Copiare l'URL del disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Eliminare la VM esistente
In Azure, i dischi rigidi virtuali e le macchine virtuali sono due risorse distinte. In un disco rigido virtuale sono archiviati il sistema operativo, le applicazioni e le configurazioni. La macchina virtuale è invece costituita da metadati che definiscono le dimensioni o il percorso, e da risorse di riferimento, ad esempio un disco rigido virtuale o una scheda di interfaccia di rete virtuale. A ogni disco rigido virtuale associato a una macchina virtuale viene assegnato un lease. È possibile collegare e scollegare i dischi dati anche quando la macchina virtuale è in esecuzione, mentre non è possibile scollegare il disco del sistema operativo, a meno che la risorsa di macchina non sia stata eliminata. Il lease continua ad associare il disco del sistema operativo e la macchina virtuale anche quando questa viene arrestata e deallocata.

Il primo passaggio per ripristinare la macchina virtuale consiste nell'eliminare la risorsa della macchina virtuale stessa. Anche se si elimina la macchina virtuale, i dischi rigidi virtuali restano nell'account di archiviazione. Dopo aver eliminato la macchina virtuale, il disco rigido virtuale viene collegato a un'altra macchina virtuale per diagnosticare e risolvere gli errori.

Selezionare la macchina virtuale nel portale, quindi fare clic su **Elimina**:

![Schermata di diagnostica di avvio della macchina virtuale che mostra un errore di avvio](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Attendere il completamento dell'eliminazione della macchina virtuale prima di collegare il disco rigido virtuale a un'altra macchina virtuale. Il lease del disco rigido virtuale che lo associa alla macchina virtuale deve essere rilasciato prima di poter collegare il disco a un'altra macchina.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Collegare il disco rigido virtuale esistente a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Il disco rigido virtuale esistente viene collegato alla macchina virtuale usata per la risoluzione dei problemi, grazie alla quale è possibile individuare e modificare il contenuto del disco. Questo processo consente, ad esempio, di correggere eventuali errori di configurazione, di esaminare applicazioni aggiuntive o file del registro di sistema. Scegliere o creare un'altra macchina virtuale da usare per la risoluzione dei problemi.

1. Nel portale, selezionare il gruppo di risorse e quindi la macchina virtuale da usare per la risoluzione dei problemi. Selezionare **Dischi** e quindi fare clic su **Collega esistente**:

    ![Collegare un disco esistente nel portale](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Per selezionare il disco rigido virtuale esistente, fare clic su **File VHD**:

    ![Cercare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Selezionare l'account e il contenitore di archiviazione, quindi fare clic sul disco rigido virtuale esistente. Fare clic sul pulsante **Seleziona** per confermare la scelta:

    ![Selezionare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Dopo aver selezionato il disco rigido virtuale, fare clic su **OK** per collegare il disco rigido virtuale esistente:

    ![Confermare il collegamento del disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Dopo alcuni secondi, il riquadro **Dischi** della macchina virtuale esistente mostra il disco rigido virtuale esistente collegato come disco dati:

    ![Disco rigido virtuale esistente collegato come disco dati](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montare il disco dati collegato

> [!NOTE]
> Gli esempi seguenti mostrano in dettaglio i passaggi necessari in una VM Ubuntu. Se si usa una diversa distribuzione Linux, ad esempio Red Hat Enterprise Linux o SUSE, i percorsi dei file di log e i comandi `mount` potrebbero differire. Consultare la documentazione relativa alla distribuzione specifica per le opportune modifiche ai comandi.

1. Eseguire SSH nella macchina virtuale di cui risolvere i problemi usando le credenziali appropriate. Se questo disco è il primo disco dati collegato alla macchina virtuale di cui risolvere i problemi, è probabilmente connesso a `/dev/sdc`. Usare `dmseg` per elencare i dischi collegati:

    ```bash
    dmesg | grep SCSI
    ```
    L'output è simile all'esempio seguente:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    Nell'esempio precedente, il disco del sistema operativo è in `/dev/sda` e il disco temporaneo fornito per ogni macchina virtuale è in `/dev/sdb`. Se sono presenti più dischi dati, devono trovarsi in `/dev/sdd`, `/dev/sde`, e così via.

2. Creare una directory per montare il disco rigido virtuale esistente. L'esempio seguente crea una directory denominata `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se sul disco rigido virtuale esistente sono presenti più partizioni, montare la partizione richiesta. L'esempio seguente monta la prima partizione primaria in `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Si consiglia di montare i dischi dati nelle macchine virtuali in Azure usando l'identificatore univoco universale (UUID) del disco rigido virtuale. Per questo scenario, non è necessario montare il disco rigido virtuale usando il relativo l'UUID. Durante il normale utilizzo, invece, modificare `/etc/fstab` per montare i dischi rigidi virtuali usando il nome di dispositivo anziché l'UUID può impedire il corretto avvio della macchina virtuale.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto gli errori, scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione SSH nella macchina virtuale usata per la risoluzione dei problemi, smontare il disco rigido virtuale esistente. Modificare innanzitutto la directory padre del punto di montaggio:

    ```bash
    cd /
    ```

    A questo punto smontare il disco rigido virtuale esistente. Nell'esempio viene smontato il dispositivo in `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Scollegare il disco rigido virtuale dalla macchina virtuale. Selezionare la macchina virtuale nel portale, quindi fare clic su **Dischi**. Selezionare il disco rigido virtuale esistente quindi fare clic su **Scollega**:

    ![Scollegare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Attendere che il disco dati sia completamente scollegato dalla macchina virtuale prima di continuare.

## <a name="create-vm-from-original-hard-disk"></a>Creare una macchina virtuale dal disco rigido originale
Per creare una macchina virtuale dal disco rigido virtuale originale, usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Il modello distribuisce una macchina virtuale in una rete virtuale esistente, usando l'URL del disco rigido virtuale del comando precedente. Fare clic sul pulsante **Distribuisci in Azure** come indicato di seguito:

![Distribuire una macchina virtuale da un modello di GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Il modello viene caricato nel portale di Azure per la distribuzione. Immettere i nomi della nuova macchina virtuale e delle risorse di Azure esistenti e incollare l'URL del disco rigido virtuale esistente. Per avviare la distribuzione, fare clic su **Acquista**:

![Distribuire una macchina virtuale dal modello](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Riabilitare la diagnostica di avvio
Quando si crea la macchina virtuale dal disco rigido virtuale esistente, la diagnostica di avvio potrebbe non essere abilitata automaticamente. Per controllare lo stato della diagnostica di avvio e attivarla se necessario, selezionare la macchina virtuale nel portale. In **Monitoraggio**, fare clic su **Impostazioni di diagnostica**. Verificare che lo stato sia **Attivo** e che il segno di spunta accanto a **Diagnostica di avvio** sia selezionato. Se si apportano modifiche, fare clic su **Salva**:

![Aggiornare le impostazioni di diagnostica di avvio](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla macchina virtuale, vedere l'articolo sulla [risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni in una macchina virtuale di Azure per Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

