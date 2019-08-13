---
title: Eseguire il backup di condivisioni file di File di Azure con il servizio Backup di Azure
description: Questa esercitazione spiega come eseguire il backup di condivisioni file di Azure.
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: e63ad75effb03cf9dd5eb5c66b142cce629ea290
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736244"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Eseguire il backup di condivisioni file di Azure nel portale di Azure
Questa esercitazione illustra come usare il portale di Azure per eseguire il backup di [condivisioni file di Azure](../storage/files/storage-files-introduction.md).

Questa guida illustra come eseguire queste operazioni:
> [!div class="checklist"]
> * Configurare un insieme di credenziali di Servizi di ripristino per eseguire il backup di file di Azure
> * Eseguire un processo di backup su richiesta per creare un punto di ripristino


## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire il backup di una condivisione file di Azure, assicurarsi che la condivisione si trovi in uno dei [tipi di account di archiviazione supportati](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Dopo questa verifica è possibile proteggere le condivisioni file.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitazioni per il backup delle condivisioni file di Azure durante l'anteprima
Il backup per le condivisioni file di Azure è disponibile in anteprima. Le condivisioni file di Azure sono supportate negli account di archiviazione per utilizzo generico sia v1 che v2. Gli scenari di backup seguenti non sono supportati nelle condivisioni file di Azure:
- Non è possibile proteggere le condivisioni file di Azure negli account di archiviazione con reti virtuali o firewall abilitati.
- Per la protezione di File di Azure con Backup di Azure non è disponibile l'interfaccia della riga di comando.
- Il numero massimo di backup pianificati al giorno è uno.
- Il numero massimo di backup su richiesta al giorno è quattro.
- Usare i [blocchi delle risorse](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) nell'account di archiviazione per impedire l'eliminazione accidentale dei backup nell'insieme di credenziali di Servizi di ripristino.
- Non eliminare gli snapshot creati da Backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.
- Non eliminare le condivisioni file protette mediante Backup di Azure. La soluzione corrente elimina tutti gli snapshot creati da Backup di Azure dopo l'eliminazione della condivisione file, causando la perdita di tutti i punti di ripristino

Il backup per le condivisioni file di Azure negli account di archiviazione con replica di [archiviazione con ridondanza della zona](../storage/common/storage-redundancy-zrs.md) (ZRS) è attualmente disponibile solo nelle aree Stati Uniti centrali (CUS), Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Europa settentrionale (NE), Asia sudorientale (SEA), Europa occidentale (WE) e Stati Uniti occidentali 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configurazione del backup per una condivisione file di Azure
In questa esercitazione si presuppone che sia già disponibile una condivisione file di Azure. Per eseguire il backup della condivisione file di Azure:

1. Creare un insieme di credenziali di Servizi di ripristino nella stessa area della condivisione file. Se è già disponibile un insieme di credenziali, aprire la pagina Panoramica correlata e fare clic su **Backup**.

    ![Fare clic su Backup nella pagina Panoramica dell'insieme di credenziali](./media/backup-file-shares/overview-backup-page.png)

2. Nel menu **Obiettivo di backup** scegliere Condivisione file di Azure da **Elementi di cui eseguire il backup**.

    ![Scegliere Condivisione file di Azure come obiettivo di backup](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Fare clic su **Backup** per configurare la condivisione file di Azure nell'insieme di credenziali di Servizi di ripristino.

   ![Fare clic su Backup per associare la condivisione file di Azure all'insieme di credenziali](./media/backup-file-shares/set-backup-goal.png)

    Dopo aver associato l'insieme di credenziali alla condivisione file di Azure, si aprirà il menu Backup che chiede di selezionare un account di archiviazione. Il menu visualizza tutti gli account di archiviazione supportati presenti nell'area in cui si trova l'insieme di credenziali e non già associati a un insieme di credenziali di Servizi di ripristino.

   ![Selezionare l'account di archiviazione](./media/backup-file-shares/list-of-storage-accounts.png)

4. Nell'elenco degli account di archiviazione selezionare un account e fare clic su **OK**. Azure cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se sono state recentemente aggiunte condivisioni file e queste non vengono visualizzate nell'elenco, attendere qualche istante.

   ![Le condivisioni file vengono individuate](./media/backup-file-shares/discover-file-shares.png)

5. Dall'elenco **Condivisioni file** selezionare una o più condivisioni file delle quali eseguire il backup e fare clic su **OK**.

6. Dopo aver scelto le condivisioni file, il menu Backup passa a **Criteri di backup**. In questo menu selezionare un criterio di backup esistente oppure crearne uno nuovo, quindi fare clic su **Abilita backup**.

   ![Selezionare un criterio di backup o crearne uno nuovo](./media/backup-file-shares/apply-backup-policy.png)

    Dopo aver stabilito un criterio di backup verrà creato uno snapshot delle condivisioni file all'orario pianificato e il punto di ripristino verrà conservato per il periodo scelto.

## <a name="create-an-on-demand-backup"></a>Creare un backup su richiesta
Dopo aver configurato il criterio di backup, è opportuno creare un backup su richiesta per garantire la protezione dei dati fino al successivo backup pianificato.


### <a name="to-create-an-on-demand-backup"></a>Per creare un backup su richiesta

1. Aprire l'insieme di credenziali di Servizi di ripristino che contiene i punti di ripristino della condivisione file, quindi fare clic su **Elementi di backup**. Verrà visualizzato l'elenco degli elementi di backup.

   ![Elenco di elementi di backup](./media/backup-file-shares/list-of-backup-items.png)

2. Dall'elenco selezionare **Archiviazione di Azure (File di Azure)** . Verrà visualizzato l'elenco delle condivisioni file di Azure.

   ![Elenco delle condivisioni file di Azure](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Verrà visualizzato il menu Elemento di backup per la condivisione file selezionata.

   ![Menu Elemento di backup per la condivisione file selezionata](./media/backup-file-shares/backup-item-menu.png)

4. Dal menu Elemento di backup fare clic su **Esegui backup ora**. Trattandosi di un processo di backup su richiesta, non sono presenti criteri di conservazione associati al punto di ripristino. Verrà visualizzata la finestra di dialogo **Esegui backup ora**. Specificare l'ultimo giorno di conservazione del punto di ripristino.

   ![Scegliere una data per la conservazione dei punti di ripristino](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il portale di Azure per eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Configurare un insieme di credenziali di Servizi di ripristino per eseguire il backup di file di Azure
> * Eseguire un processo di backup su richiesta per creare un punto di ripristino

Passare all'articolo successivo per eseguire il ripristino da un backup di una condivisione file di Azure.

> [!div class="nextstepaction"]
> [Eseguire il ripristino da un backup di condivisioni file di Azure](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
