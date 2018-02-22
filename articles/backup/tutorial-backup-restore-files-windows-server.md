---
title: Recuperare i file da Azure in Windows Server | Microsoft Docs
description: Questa esercitazione illustra nei dettagli come recuperare gli elementi da Azure in Windows Server.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: eseguire il backup di windows server; ripristinare i file in windows server; eseguire il backup e ripristino di emergenza
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: 3bda261bae8155ccb48196a980b14afc56004da8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperare i file da Azure in un'istanza di Windows Server

Backup di Azure consente il ripristino dei singoli elementi dai backup di Windows Server. Il recupero dei singoli file è utile se è necessario ripristinare rapidamente i file eliminati per errore. Questa esercitazione illustra come usare l'agente di Servizi di ripristino di Microsoft Azure (MARS) per recuperare gli elementi dai backup già eseguiti in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Avviare il ripristino di singoli elementi 
> * selezione di un punto di ripristino 
> * Ripristinare gli elementi da un punto di ripristino

Questa esercitazione presuppone che sia già stata eseguita la procedura per [eseguire il backup di un'istanza di Windows Server in Azure](backup-configure-vault.md) e sia abbia a disposizione almeno un backup dei file di Windows Server in Azure.

## <a name="initiate-recovery-of-individual-items"></a>Avviare il ripristino di singoli elementi

Insieme all'agente di Servizi di ripristino di Microsoft Azure (MARS) viene installata un'utile procedura guidata dell'interfaccia utente denominata Backup di Microsoft Azure. La procedura guidata di Backup di Microsoft Azure, insieme all'agente di Servizi di ripristino di Microsoft Azure (MARS), recupera i dati di backup dai punti di ripristino archiviati in Azure. Usare la procedura guidata di Backup di Microsoft Azure per identificare i file o le cartelle da ripristinare in Windows Server. 

1. Aprire lo snap-in di **Backup di Microsoft Azure** . È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. Nella procedura guidata fare clic su **Ripristina dati** nel **riquadro Azioni** della console dell'agente per avviare la procedura guidata di **ripristino dati**.

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Nella pagina **Attività iniziale** selezionare **Questo server (nome server)** e fare clic su **Avanti**.

4. Nella pagina **Seleziona modalità di ripristino** selezionare **Singoli file e cartelle** e fare clic su **Avanti** per iniziare il processo di selezione del punto di ripristino.
 
5. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file o le cartelle da ripristinare e fare clic su **Monta**. Selezionare una data e un'ora dal menu a discesa che corrisponde a un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino nel giorno specificato.

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Quando si seleziona **Monta**, Backup di Azure rende disponibile il punto di ripristino come disco. Selezionare e recuperare i file dal disco.

## <a name="restore-items-from-a-recovery-point"></a>Ripristinare gli elementi da un punto di ripristino

1. Dopo aver montato il volume di ripristino, fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle da recuperare. 

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    È possibile aprire i file direttamente dal volume di ripristino e verificare i file.

2. In Esplora risorse copiare i file e/o le cartelle che si vuole ripristinare e incollarli in una posizione qualsiasi nel server.

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Dopo aver terminato il ripristino di file e/o cartelle, nella pagina **Browse and Recovery File** (Cerca e ripristina file) della procedura guidata di **ripristino dati** fare clic su **Smonta**. 

    ![Backup in sospeso](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Fare clic su **Sì** per confermare che si vuole smontare il volume.

    Dopo aver smontato lo snapshot, viene visualizzato il messaggio **Processo completato** nel riquadro **Processi** nella console dell'agente.

## <a name="next-steps"></a>Passaggi successivi

L'argomento seguente completa le esercitazioni sul backup e il ripristino dei dati di Windows Server in Azure. Per altre informazioni su Backup di Azure, vedere l'esempio PowerShell per il backup di macchine virtuali crittografate.

> [!div class="nextstepaction"]
> [Back up encrypted VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md) (Eseguire il backup di VM crittografate)
