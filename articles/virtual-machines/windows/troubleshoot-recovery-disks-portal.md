---
title: Usare una macchina virtuale per la risoluzione dei problemi Windows nel portale di Azure | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle macchine virtuali Windows in Azure connettendo il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 0b6b2220f5b7287bae6bfd6e99390944b7a7578d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
Se nella macchina virtuale Windows in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare il portale di Azure per connettere il disco rigido virtuale a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale.

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Eliminare la macchina virtuale su cui si riscontrano i problemi, mantenendo i dischi rigidi virtuali.
2. Collegare e montare il disco rigido virtuale in un'altra VM Windows per risolvere i problemi riscontrati.
3. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
4. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
5. Creare una VM usando il disco rigido virtuale originale.


## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
Per determinare perché la VM non riesce ad avviarsi correttamente, esaminare lo screenshot della VM relativo alla diagnostica di avvio. Un esempio comune è un aggiornamento di un'applicazione non riuscito oppure l'eliminazione o lo spostamento di un disco rigido virtuale sottostante.

Nel portale, selezionare la macchina virtuale e quindi scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi**. Fare clic su **Diagnostica di avvio** per visualizzare lo screenshot. Esaminare i messaggi di errore o i codici errore specifici per determinare perché si sta verificando un problema con la VM. L'esempio seguente illustra una VM in attesa dell'arresto dei servizi:

![Visualizzazione dei registri della console nella diagnostica di avvio della macchina virtuale](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

È anche possibile fare clic **Screenshot** per scaricare una cattura dello screenshot della VM.


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

1. Aprire una connessione Desktop remoto alla VM. Selezionare la VM nel portale e fare clic su **Connetti**. Scaricare e aprire il file di connessione RDP. Immettere le credenziali per accedere alla VM, come segue:

    ![Accedere alla VM usando Desktop remoto](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Aprire **Server Manager**, quindi selezionare **Servizi file e archiviazione**. 

    ![Selezionare Servizi file e archiviazione in Server Manager](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. Il disco dati viene automaticamente rilevato e collegato. Per visualizzare un elenco dei dischi connessi, selezionare **Dischi**. È possibile selezionare il disco dati per visualizzare informazioni sul volume, inclusa la lettera di unità. L'esempio seguente illustra il disco dati collegato e l'uso di **F:**

    ![Disco collegato e informazioni sul volume in Server Manager](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto gli errori, scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione RDP stabilita con la VM aprire **Server Manager**, quindi selezionare **Servizi file e archiviazione**:

    ![Selezionare Servizi file e archiviazione in Server Manager](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Selezionare **Dischi** e quindi selezionare il disco dati. Fare clic con il pulsante destro del mouse sul disco dati e selezionare **Porta offline**:

    ![Impostare il disco dati come offline in Server Manager](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Scollegare il disco rigido virtuale dalla macchina virtuale. Selezionare la macchina virtuale nel portale di Azure e fare clic su **Dischi**. Selezionare il disco rigido virtuale esistente quindi fare clic su **Scollega**:

    ![Scollegare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Attendere che il disco dati sia completamente scollegato dalla macchina virtuale prima di continuare.

## <a name="create-vm-from-original-hard-disk"></a>Creare una macchina virtuale dal disco rigido originale
Per creare una macchina virtuale dal disco rigido virtuale originale, usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Il modello distribuisce una macchina virtuale in una rete virtuale esistente, usando l'URL del disco rigido virtuale del comando precedente. Fare clic sul pulsante **Distribuisci in Azure** come indicato di seguito:

![Distribuire una macchina virtuale da una modello di Github](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Il modello viene caricato nel portale di Azure per la distribuzione. Immettere i nomi della nuova macchina virtuale e delle risorse di Azure esistenti e incollare l'URL del disco rigido virtuale esistente. Per avviare la distribuzione, fare clic su **Acquista**:

![Distribuire una macchina virtuale dal modello](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Riabilitare la diagnostica di avvio
Quando si crea la macchina virtuale dal disco rigido virtuale esistente, la diagnostica di avvio potrebbe non essere abilitata automaticamente. Per controllare lo stato della diagnostica di avvio e attivarla se necessario, selezionare la macchina virtuale nel portale. In **Monitoraggio**, fare clic su **Impostazioni di diagnostica**. Verificare che lo stato sia **Attivo** e che il segno di spunta accanto a **Diagnostica di avvio** sia selezionato. Se si apportano modifiche, fare clic su **Salva**:

![Aggiornare le impostazioni di diagnostica di avvio](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
