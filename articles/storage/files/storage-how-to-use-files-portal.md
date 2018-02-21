---
title: Come gestire File di Azure dal portale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure per gestire File di Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Come usare File di Azure dal portale di Azure
Il [portale di Azure](https://portal.azure.com) fornisce un'interfaccia utente per la gestione di File di Azure. Dal Web browser è possibile eseguire le azioni seguenti:

* Creare una condivisione file
* Caricare i file nella condivisione file e scaricarli.
* Monitorare l'uso effettivo di ogni condivisione file.
* Rettificare la quota delle dimensioni della condivisione file.
* Copiare i comandi di montaggio da usare per montare la condivisione file da un client Windows o Linux.

## <a name="create-file-share"></a>Creare la condivisione file
1. Accedere al portale di Azure.
2. Nel menu di navigazione fare clic su **Account di archiviazione** o su **Account di archiviazione (versione classica)**.
    
    ![Schermata che illustra come creare una condivisione file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Scegliere l'account di archiviazione.

    ![Schermata che illustra come creare una condivisione file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Scegliere il servizio "File".

    ![Schermata che illustra come creare una condivisione file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Fare clic su "Condivisioni file" e selezionare il collegamento per creare la prima condivisione file.

    ![Schermata che illustra come creare una condivisione file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Immettere il nome della condivisione e le dimensioni, fino a 5120 GB, per creare la prima condivisione file. Dopo che la condivisione file è stata creata, è possibile montarla da qualsiasi file system che supporti SMB 2.1 o SMB 3.0. È possibile fare clic su **Quota** nella condivisione file per modificare le dimensioni del file fino a 5120 GB. Vedere [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per un preventivo dei costi di archiviazione relativi all'uso di File di Azure.

    ![Schermata che illustra come creare una condivisione file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Caricare e scaricare file
1. Scegliere una condivisione file già creata.

    ![Schermata che illustra come caricare e scaricare file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Fare clic su **Carica** per aprire l'interfaccia utente per il caricamento dei file.

    ![Schermata che illustra come caricare file nel portale](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Connettersi alla condivisione file
-  Fare clic su **Connetti** per ottenere la riga di comando per montare la condivisione file da Windows e Linux. Gli utenti Linux possono vedere anche [Come usare File di Azure con Linux](../storage-how-to-use-files-linux.md) per altre istruzioni sul montaggio per le altre distribuzioni Linux.

    ![Schermata che illustra come montare la condivisione file](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  È possibile copiare i comandi per il montaggio della condivisione file in Windows o Linux ed eseguirla dalla VM di Azure o dal computer locale.

    ![Screenshot che illustra i comandi di montaggio per Windows e Linux](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Suggerimento:**  
Per trovare la chiave di accesso dell'account di archiviazione per il montaggio, fare clic su **Visualizza chiavi di accesso per questo account di archiviazione** nella parte inferiore della pagina della connessione.

## <a name="see-also"></a>Vedere anche 
Per altre informazioni su File di Azure, vedere i collegamenti seguenti.

* [DOMANDE FREQUENTI](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Risoluzione dei problemi in Linux](storage-troubleshoot-linux-file-connection-problems.md)    
