---
title: 'Esercitazione: Ripristinare elementi in Windows Server'
description: Questa esercitazione illustra come usare Agente servizi di ripristino di Microsoft Azure per recuperare elementi da Azure a un'istanza di Windows Server.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263113"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperare i file da Azure in un'istanza di Windows Server

Backup di Azure consente il ripristino dei singoli elementi dai backup di Windows Server. Il recupero dei singoli file è utile se è necessario ripristinare rapidamente i file eliminati per errore. Questa esercitazione illustra come usare l'agente di Servizi di ripristino di Microsoft Azure (MARS) per recuperare gli elementi dai backup già eseguiti in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Avviare il ripristino di singoli elementi
> * selezione di un punto di ripristino
> * Ripristinare gli elementi da un punto di ripristino

Questa esercitazione presuppone che sia già stata eseguita la procedura per [eseguire il backup di un'istanza di Windows Server in Azure](backup-windows-with-mars-agent.md) e sia abbia a disposizione almeno un backup dei file di Windows Server in Azure.

## <a name="initiate-recovery-of-individual-items"></a>Avviare il ripristino di singoli elementi

Insieme all'agente di Servizi di ripristino di Microsoft Azure (MARS) viene installata un'utile procedura guidata dell'interfaccia utente denominata Backup di Microsoft Azure. La procedura guidata di Backup di Microsoft Azure, insieme all'agente di Servizi di ripristino di Microsoft Azure (MARS), recupera i dati di backup dai punti di ripristino archiviati in Azure. Usare la procedura guidata di Backup di Microsoft Azure per identificare i file o le cartelle da ripristinare in Windows Server.

1. Aprire lo snap-in di **Backup di Microsoft Azure** . È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Snap-in Backup di Microsoft Azure](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. Nella procedura guidata selezionare **Ripristina dati** nel **riquadro Azioni** della console dell'agente per avviare il **Ripristino guidato dei dati**.

    ![Selezionare Ripristina dati](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Nella pagina **Attività iniziali** selezionare **Questo server (nome server)** e fare clic su **Avanti**.

4. Nella pagina **Seleziona modalità di ripristino** selezionare **Singoli file e cartelle** e fare clic su **Avanti** per iniziare il processo di selezione del punto di ripristino.

5. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file o le cartelle da ripristinare e fare clic su **Monta**. Selezionare una data e un'ora dal menu a discesa che corrisponde a un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino nel giorno specificato.

    ![Seleziona volume e data](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Quando si seleziona **Monta**, Backup di Azure rende disponibile il punto di ripristino come disco. Selezionare e recuperare i file dal disco.

## <a name="restore-items-from-a-recovery-point"></a>Ripristinare gli elementi da un punto di ripristino

1. Dopo aver montato il volume di ripristino, fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle da recuperare.

    ![Selezionare Sfoglia](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    È possibile aprire i file direttamente dal volume di ripristino e verificare i file.

2. In Esplora risorse copiare i file e le cartelle da ripristinare e incollarli nel percorso desiderato nel server.

    ![Copiare file e cartelle](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Dopo aver terminato il ripristino di file e cartelle, nella pagina **Cerca e ripristina file** di **Ripristino guidato dei dati** selezionare **Smonta**.

    ![Selezionare Smonta](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Fare clic su **Sì** per confermare che si intende smontare il volume.

    Dopo aver smontato lo snapshot, viene visualizzato il messaggio **Processo completato** nel riquadro **Processi** nella console dell'agente.

## <a name="next-steps"></a>Passaggi successivi

L'argomento seguente completa le esercitazioni sul backup e il ripristino dei dati di Windows Server in Azure. Per altre informazioni su Backup di Azure, vedere l'esempio PowerShell per il backup di macchine virtuali crittografate.

> [!div class="nextstepaction"]
> [Back up encrypted VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md) (Eseguire il backup di VM crittografate)
