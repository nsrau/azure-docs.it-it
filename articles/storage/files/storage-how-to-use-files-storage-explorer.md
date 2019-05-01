---
title: Guida introduttiva per la gestione di condivisioni file di Azure con Azure Storage Explorer
description: Questa guida introduttiva illustra come usare Azure Storage Explorer per gestire File di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bf87704a8055b5725178c846df71ed4d873417e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713883"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Guida introduttiva: Creare e gestire condivisioni file di Azure con Azure Storage Explorer
Questa guida contiene tutte le informazioni essenziali sull'uso delle [condivisioni file di Azure](storage-files-introduction.md) con Azure Storage Explorer. Le condivisioni file di Azure sono esattamente come le altre condivisioni file, ma vengono archiviate nel cloud e sono supportate dalla piattaforma Azure. Le condivisioni file di Azure supportano il protocollo SMB standard di settore e consentono la condivisione di file in più computer, applicazioni e istanze. 

Azure Storage Explorer è un popolare strumento client disponibile per Windows, macOS e Linux. È possibile usare Storage Explorer per gestire condivisioni file di Azure e altre risorse di archiviazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva richiede l'installazione di Storage Explorer. Per il download e l'installazione, visitare la pagina [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Non è possibile usare Storage Explorer per creare nuove risorse. Ai fini di questa dimostrazione, creare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Connettere Storage Explorer alle risorse di Azure
Al primo avvio di Storage Explorer, viene visualizzata la finestra **Microsoft Azure Storage Explorer - Connetti**. Storage Explorer offre diversi modi per connettersi agli account di archiviazione. 

- **Accedere con l'account Azure**: è possibile accedere usando le credenziali utente per la propria organizzazione o l'account Microsoft. 
- **Connettersi a uno specifico account di archiviazione con una stringa di connessione o un token di firma di accesso condiviso**: la stringa di connessione è una stringa speciale che contiene il nome dell'account di archiviazione e la chiave/token di firma di accesso condiviso corrispondente. Con il token, Storage Explorer accede direttamente all'account di archiviazione, anziché visualizzare tutti gli account di archiviazione in un account Azure. Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Connettersi a uno specifico account di archiviazione con un nome e una chiave dell'account di archiviazione**: usare il nome e la chiave dell'account di archiviazione per connettersi ad Archiviazione di Azure.

Ai fini di questa guida introduttiva, accedere con l'account Azure. Selezionare **Add an Azure Account** (Aggiungi un account Azure) e quindi **Accedi**. Seguire le istruzioni per accedere all'account Azure.

![Screenshot della finestra Microsoft Azure Storage Explorer - Connetti](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Creare una condivisione file
Per creare la prima condivisione file di Azure nell'account di archiviazione `storageacct<random number>`:

1. Espandere l'account di archiviazione creato.
2. Fare clic con il pulsante destro del mouse su **Condivisioni file** e quindi scegliere **Crea condivisione file**.  
    ![Screenshot della cartella delle condivisioni file e il menu di scelta rapida nel contesto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Per la condivisione file immettere *myshare* e quindi premere INVIO.

I nomi delle condivisioni possono contenere solo lettere minuscole, numeri e trattini singoli (non possono però iniziare con un trattino). Per informazioni dettagliate su come denominare le condivisioni e i file, vedere [Naming and referencing shares, directories, files, and metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Denominazione e riferimento a condivisioni, directory, file e metadati).

Al termine della creazione, nel riquadro destro viene visualizzata una scheda per la condivisione file. 

## <a name="use-your-azure-file-share"></a>Usare la condivisione file di Azure
Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile usare la condivisione file di Azure con Azure Storage Explorer. Rispetto al montaggio della condivisione file con SMB, l'uso di Azure Storage Explorer è vantaggioso perché tutte le richieste effettuate con Azure Storage Explorer vengono eseguite con l'API REST di File, che può essere usata per creare, modificare ed eliminare file e directory nei client senza accesso SMB.

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

## <a name="clean-up-resources"></a>Pulire le risorse
Non è possibile usare Storage Explorer per rimuovere le risorse. Per eseguire la pulizia delle risorse di questa guida introduttiva, è possibile usare il [portale di Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Che cos'è File di Azure?](storage-files-introduction.md)
