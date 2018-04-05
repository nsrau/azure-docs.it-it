---
title: Gestione di condivisioni file di Azure con il portale di Azure
description: Informazioni su come usare il portale di Azure per gestire File di Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 588d260bb939c8f6439ca66828296ea455f1524a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Gestione di condivisioni file di Azure con il portale di Azure 
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate in Windows, Linux e macOS. Questa guida contiene tutte le informazioni essenziali sull'uso delle condivisioni file di Azure con il [portale di Azure](https://portal.azure.com/). È possibile passare agli argomenti seguenti:

> [!div class="checklist"]
> * Creare un gruppo di risorse e un account di archiviazione
> * Creare una condivisione file di Azure 
> * Creare una directory
> * Caricare un file 
> * Scaricare un file
> * Creare e usare uno snapshot di condivisione

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Creare una condivisione file
Per creare una condivisione file:

1. Selezionare l'account di archiviazione dal proprio dashboard.
2. Nella sezione **Servizi** della pagina dell'account di archiviazione selezionare **File**.
    ![Screenshot della sezione Servizi dell'account di archiviazione; selezionare il servizio File](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. Nel menu in alto nella pagina **Servizio file** fare clic su **+ Condivisione file**. Verrà visualizzata la pagina **Nuova condivisione file**.
4. In **Nome** digitare *myshare*.
5. Fare clic su **OK** per creare la condivisione file di Azure.

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Modifica dei contenuti della condivisione file di Azure
Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile modificare la condivisione file di Azure con il portale di Azure. Tutte le richieste effettuate tramite il portale di Azure vengono eseguite con l'API REST di File, che consente di creare, modificare ed eliminare file e directory nei client senza accesso SMB.

### <a name="create-directory"></a>Creare la directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure:

1. Nella pagina **Servizio file** selezionare la condivisione file **myshare**. Verrà visualizzata la pagina della condivisione file.
2. Dal menu nella parte superiore della pagina selezionare **Aggiungi directory**. Verrà visualizzata la pagina **Nuova directory**.
3. Digitare *myDirectory* e quindi fare clic su **OK**.

### <a name="upload-a-file"></a>Caricare un file 
Per illustrare il caricamento di un file, è prima di tutto necessario creare o selezionare un file da caricare. È possibile farlo in qualsiasi modo desiderato. Dopo aver selezionato il file da caricare:

1. Fare clic sulla directory **myDirectory**. Si aprirà il pannello **myDirectory**.
2. Scegliere **Upload** (Carica) dal menu in alto. Si aprirà il pannello **Carica file**.  
    ![Screenshot del pannello di caricamento file](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Fare clic sull'icona della cartella per aprire una finestra per esplorare i file locali. 
4. Selezionare un file e fare clic su **Apri**. 
5. Nella pagina **Carica file** verificare il nome del file e quindi fare clic su **Upload** (Carica).
6. Al termine, il file sarà visualizzato nell'elenco nella pagina **myDirectory**.

### <a name="download-a-file"></a>Scaricare un file
È possibile scaricare una copia del file caricato facendo clic con il pulsante destro del mouse sul file. Dopo aver fatto clic sul pulsante di download, i passaggi successivi varieranno in base al sistema operativo e al browser in uso.

## <a name="create-and-modify-share-snapshots"></a>Creare e modificare gli snapshot di condivisione
Un'altra utile attività da eseguire con una condivisione file di Azure è la creazione degli snapshot di condivisione. Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di condivisione sono simili alle tecnologie del sistema operativo con cui probabilmente si ha già familiarità, ad esempio:
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) per i file system Windows come NTFS e ReFS
- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS. 

Per creare uno snapshot di condivisione:

1. Aprire la pagina della condivisione file accedendo all'account di archiviazione dal dashboard e selezionando **File** > **myshare**. 
2. Nella pagina della condivisione file fare clic sul pulsante **Snapshot** nel menu in alto nella pagina e quindi selezionare **Create a snapshot** (Crea snapshot).  
    ![Screenshot che illustra come trovare il pulsante di creazione snapshot](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Elencare ed esplorare gli snapshot di condivisione
Dopo la creazione dello snapshot, è possibile fare nuovamente clic su **Snapshot** e quindi selezionare **Visualizza snapshot** per elencare gli snapshot della condivisione. Verrà visualizzato un riquadro contenente gli snapshot di questa condivisione. Fare clic su uno snapshot di condivisione per esplorarlo.

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
Per illustrare il ripristino di un file da uno snapshot di condivisione, è prima di tutto necessario eliminare un file dalla condivisione file di Azure attiva. Passare alla cartella *myDirectory*, fare clic con il pulsante destro del mouse sul file caricato e quindi scegliere **Elimina**. Quindi, per ripristinare il file dallo snapshot di condivisione:

1. Fare clic su **Snapshot** nel menu in alto e scegliere **Visualizza snapshot**. 
2. Fare clic sullo snapshot creato in precedenza per aprirne il contenuto in una nuova pagina. 
3. Fare clic su **myDirectory** nello snapshot. Verrà visualizzato il file che era stato eliminato. 
4. Fare clic con il pulsante destro del mouse sul file eliminato e scegliere **Restore** (Ripristina).
5. Verrà visualizzata una finestra popup che consente di scegliere tra il ripristino del file come copia e la sovrascrittura del file originale. Poiché il file originale era stato eliminato, è possibile selezionare **Sovrascrivi file originale** per ripristinare il file nello stato in cui si trovava prima dell'eliminazione. Fare clic su **OK** per ripristinare il file nella condivisione file di Azure.  
    ![Screenshot della finestra di dialogo di ripristino file](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Al termine del ripristino del file, chiudere la pagina dello snapshot e tornare a **myshare** > **myDirectory**. Il file sarà nuovamente nella posizione originale.

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
Per eliminare uno snapshot di condivisione, [passare all'elenco degli snapshot di condivisione](#list-and-browse-a-share-snapshot). Fare clic sulla casella di controllo accanto al nome dello snapshot di condivisione e quindi fare clic sul pulsante **Elimina**.

![Screenshot dell'eliminazione di uno snapshot di condivisione](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Pulire le risorse
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
- [Gestione di condivisioni file con Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gestione di condivisioni file con l'interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md)
- [Gestione di condivisioni file con Azure Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)