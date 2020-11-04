---
title: Spostare i dati di archiviazione BLOB con Azure Storage Explorer - Processo di data science per i team
description: Informazioni su come usare Azure Storage Explorer per caricare e scaricare i dati dall'archiviazione BLOB di Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5750132280d69c946ce180c51e4de6507549fcb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318173"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Spostamento dei dati da e verso l'archiviazione BLOB di Azure usando Storage Explorer di Azure
Esplora archivi di Azure è uno strumento gratuito di Microsoft che consente di lavorare con i dati di Archiviazione di Azure in Windows, MacOS e Linux. Questo argomento descrive come usarlo per caricare e scaricare i dati dall'archiviazione BLOB di Azure. Lo strumento può essere scaricato da [Esplora archivi di Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](../data-science-virtual-machine/overview.md), allora Esplora archivi di Azure è già installato nella macchina virtuale.
> 
> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Introduzione all'archivio BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e [Servizio BLOB di Azure](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per quell'account. Prima di caricare o scaricare i dati, è necessario conoscerne il nome e la chiave dell'account di archiviazione di Azure. 

* Per configurare una sottoscrizione di Azure, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull'account e sulla chiave, vedere [informazioni sugli account di archiviazione di Azure](../../storage/common/storage-account-create.md). Annotare la chiave di accesso dell'account di archiviazione in quanto servirà per la connessione all'account con lo strumento Esplora archivi di Azure.
* Lo strumento Esplora archivi di Azure può essere scaricato da [Esplora archivi di Microsoft Azure](https://storageexplorer.com/). Durante l'installazione accettare le impostazioni predefinite.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usare Azure Storage Explorer
I passaggi seguenti documentano come caricare e scaricare i dati utilizzando Esplora archivi Azure. 

1. Avviare Esplora archivi di Microsoft Azure.
2. Per visualizzare la procedura guidata **Accedi al tuo account...** , selezionare l'icona delle **impostazioni dell'account Azure** , selezionare **Aggiungi un account** e immettere le credenziali. 
![Aggiungere un account di archiviazione di Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Per visualizzare la procedura guidata **Connetti ad archiviazione di Azure** , selezionare l'icona **Connetti ad archiviazione di Azure** . ![Fare clic su "Connetti ad archiviazione di Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Immettere la chiave di accesso dell'account di archiviazione di Azure nella procedura guidata **Connetti ad archiviazione di Azure** e quindi **Avanti**. ![Immettere la chiave di accesso dall'account di archiviazione di Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Immettere il nome dell'account di archiviazione nella casella **Nome account** e selezionare **Avanti**. ![Associa archiviazione esterna](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. L'account di archiviazione aggiunto verrà ora visualizzato. Per creare un contenitore BLOB in un account di archiviazione, fare clic con il pulsante destro del mouse sul nodo **Contenitori BLOB** in tale account, selezionare **Crea contenitore BLOB** e immettere un nome.
7. Per caricare dati in un contenitore, selezionare il contenitore di destinazione e fare clic sul pulsante **Carica**.
![Account di archiviazione](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Fare clic su **...** a destra della casella **File** , selezionare uno o più file da caricare dal file system e fare clic su **Carica** per iniziare a caricare i file.![Caricare i file](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Per scaricare i dati, selezionare il BLOB nel contenitore corrispondente da scaricare e fare clic su **Scarica**. ![Download dei file](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)