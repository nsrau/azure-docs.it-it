---
title: Gestire condivisioni file di Azure con Azure Storage Explorer
description: Informazioni su come usare Azure Storage Explorer per gestire File di Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: f628f393f1ff5922d9d2c82b30d4fdbf4b5caf23
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738174"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Gestire condivisioni file di Azure con Azure Storage Explorer 
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Questo articolo contiene tutte le informazioni essenziali sull'uso delle condivisioni file di Azure con [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Storage Explorer è un popolare strumento client per Windows, macOS e Linux. È possibile usare Storage Explorer per gestire condivisioni file di Azure e altre risorse di archiviazione.

Questa guida introduttiva richiede l'installazione di Storage Explorer. Per il download e l'installazione, visitare la pagina [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gruppo di risorse e un account di archiviazione
> * Creare una condivisione file di Azure 
> * Creare una directory
> * Caricare un file
> * Scaricare un file
> * Creare e usare uno snapshot di condivisione

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Non è possibile usare Storage Explorer per creare nuove risorse. Ai fini di questa dimostrazione, creare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Connettere Storage Explorer alle risorse di Azure
Al primo avvio di Storage Explorer, viene visualizzata la finestra **Microsoft Azure Storage Explorer - Connetti**. Storage Explorer offre diversi modi per connettersi agli account di archiviazione. 

- **Accedere con l'account Azure**: è possibile accedere usando le credenziali utente per la propria organizzazione o l'account Microsoft. 
- **Connettersi a un account di archiviazione specifico usando una stringa di connessione o un token di firma di accesso condiviso**: una stringa di connessione è una speciale stringa contenente il nome e la chiave o il token di firma di accesso condiviso di un account di archiviazione. Con il token, Storage Explorer accede direttamente all'account di archiviazione, anziché visualizzare tutti gli account di archiviazione in un account Azure. Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Connettersi a un account di archiviazione specifico con il nome e la chiave dell'account di archiviazione**: usare il nome e la chiave del proprio account di archiviazione per connettersi all'archivio di Azure.

Ai fini di questa guida introduttiva, accedere con l'account Azure. Selezionare **Add an Azure Account** (Aggiungi un account Azure) e quindi **Accedi**. Seguire le istruzioni per accedere all'account Azure.

![Screenshot della finestra Microsoft Azure Storage Explorer - Connetti](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Creare una condivisione file
Per creare la prima condivisione file di Azure nell'account di archiviazione *storageacct<random number>*:

1. Espandere l'account di archiviazione creato.
2. Fare clic con il pulsante destro del mouse su **Condivisioni file** e quindi scegliere **Crea condivisione file**.  
    ![Screenshot della cartella delle condivisioni file e il menu di scelta rapida nel contesto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Per la condivisione file immettere *myshare* e quindi premere INVIO.

> [!IMPORTANT]  
> I nomi delle condivisioni possono contenere solo lettere minuscole, numeri e trattini singoli (non possono però iniziare con un trattino). Per informazioni dettagliate su come denominare le condivisioni e i file, vedere [Naming and referencing shares, directories, files, and metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Denominazione e riferimento a condivisioni, directory, file e metadati).

Al termine della creazione, nel riquadro destro viene visualizzata una scheda per la condivisione file. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Usare il contenuto di una condivisione file di Azure
Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile usare la condivisione file di Azure con l'interfaccia della riga di comando di Azure. Rispetto al montaggio della condivisione file con SMB, l'uso dell'interfaccia della riga di comando di Azure è vantaggioso perché tutte le richieste effettuate con l'interfaccia della riga di comando di Azure vengono eseguite con l'API REST di File, che può essere usata per creare, modificare ed eliminare file e directory nei client senza accesso SMB.

### <a name="create-a-directory"></a>Creare una directory
Una directory consente di strutturare in modo gerarchico una condivisione file di Azure. Nella directory è possibile creare più livelli. Prima di creare sottodirectory, tuttavia, è necessario assicurarsi che esistano le directory padre. Per il percorso myDirectory/mySubDirectory, ad esempio, è prima di tutto necessario creare la directory *myDirectory*. Si può quindi creare *mySubDirectory*. 

1. Nella scheda della condivisione file selezionare il pulsante **Nuova cartella** nel menu in alto. Verrà visualizzato il riquadro **Crea nuova directory**.
    ![Screenshot del pulsante Nuova cartella nel contesto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Come nome della directory immettere *myDirectory* e quindi selezionare **OK**. 

La directory *myDirectory* verrà elencata nella scheda della condivisione file *myshare*.

### <a name="upload-a-file"></a>Caricare un file 
È possibile caricare un file dal computer locale alla nuova directory nella condivisione file. Si può caricare un'intera cartella o un singolo file.

1. Nel menu in alto selezionare **Carica**. Questo comando consente di caricare una cartella o un file.
2. Selezionare **Carica file** e quindi un file da caricare dal computer locale.
3. In **Upload to a directory** (Carica in una directory) immettere *myDirectory* e quindi selezionare **Carica**. 

Al termine, il file verrà visualizzato nell'elenco nel riquadro *myDirectory*.

### <a name="download-a-file"></a>Scaricare un file
Per scaricare una copia di un file dalla condivisione file, fare clic con il pulsante destro del mouse sul file e quindi selezionare **Scarica**. Scegliere la posizione in cui si vuole inserire il file nel computer locale e quindi selezionare **Salva**.

Lo stato di avanzamento del download verrà visualizzato nel riquadro **Attività** nella parte inferiore della finestra.

## <a name="create-and-modify-share-snapshots"></a>Creare e modificare gli snapshot di condivisione
Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di una condivisione file sono simili ad altre tecnologie con cui probabilmente si ha già familiarità:
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) per i file system Windows, come NTFS e ReFS
- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS

Per creare uno snapshot di condivisione:

1. Selezionare la scheda della condivisione file *myshare*.
2. Nel menu in alto selezionare **Crea snapshot**. A seconda delle dimensioni della finestra di Storage Explorer, per visualizzare questa opzione potrebbe essere prima di tutto necessario selezionare **Altro**.  
    ![Screenshot del pulsante Crea snapshot nel contesto](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Elencare ed esplorare gli snapshot di condivisione
Dopo la creazione dello snapshot, per visualizzare l'elenco degli snapshot per la condivisione selezionare **View Snapshots for File Share** (Visualizza snapshot per la condivisione file). A seconda delle dimensioni della finestra di Storage Explorer, per visualizzare questa opzione potrebbe essere prima di tutto necessario selezionare **Altro**. Per esplorare uno snapshot di condivisione, fare doppio clic su di esso.

![Screenshot della finestra per l'esplorazione degli snapshot](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
Per una dimostrazione del ripristino di un file da uno snapshot di condivisione, è prima di tutto necessario eliminare un file dalla condivisione file di Azure attiva. Passare alla cartella *myDirectory*, fare clic con il pulsante destro del mouse sul file caricato e quindi scegliere **Elimina**. Per ripristinare il file dallo snapshot di condivisione:

1. Selezionare **View Snapshots for File Share** (Visualizza snapshot per la condivisione file). A seconda delle dimensioni della finestra di Storage Explorer, per visualizzare questa opzione potrebbe essere prima di tutto necessario selezionare **Altro**.
2. Nell'elenco degli snapshot di condivisione fare clic sullo snapshot.
3. Esplorare lo snapshot fino a trovare il file che è stato eliminato. Selezionare la condivisione file e quindi **Ripristina snapshot**. A seconda delle dimensioni della finestra di Storage Explorer, per visualizzare questa opzione potrebbe essere prima di tutto necessario selezionare **Altro**. Verrà visualizzata una finestra contenente un avviso che informa che il ripristino del file sovrascriverà il contenuto della condivisione file e non potrà essere annullato. Selezionare **OK**.
4. Il file si troverà ora nella posizione originale nella condivisione file di Azure attiva.

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
Per eliminare uno snapshot, passare all'[elenco degli snapshot di condivisione](#list-and-browse-share-snapshots). Fare clic con il pulsante destro del mouse sullo snapshot di condivisione che si vuole eliminare e quindi scegliere **Elimina**.

## <a name="clean-up-resources"></a>Pulire le risorse
Non è possibile usare Storage Explorer per rimuovere le risorse. Per eseguire la pulizia delle risorse di questa guida introduttiva, è possibile usare il [portale di Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
- [Gestire condivisioni file con il portale di Azure](storage-how-to-use-files-portal.md)
- [Gestire condivisioni file con Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gestire condivisioni file con l'interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md)
- [Pianificare la distribuzione di File di Azure](storage-files-planning.md)