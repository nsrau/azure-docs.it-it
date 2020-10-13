---
title: Montare una condivisione file di Azure tramite SMB con macOS | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure su SMB con macOS usando il Finder o il terminale. File di Azure è il file system cloud facile da usare di Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326066"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montare una condivisione file di Azure tramite SMB con macOS
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate con il protocollo SMB 3 standard di settore di macOS High Sierra 10.13 +. Questo articolo descrive due diversi modi di montare una condivisione file di Azure in macOS: con l'interfaccia utente del Finder e usando il terminale.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Prerequisiti per il montaggio di una condivisione file di Azure in macOS
* **Nome account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave di archiviazione primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445. Nel computer client (Mac) verificare che il firewall non blocchi la porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montare una condivisione file di Azure tramite il Finder
1. **Aprire Finder**: per impostazione predefinita, il Finder è aperto in macOS, ma è possibile assicurarsi che sia l'applicazione attualmente selezionata facendo clic sull'icona con il volto di macOS sul Dock:  
    ![Icona con il volto di macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selezionare "Connetti al server" dal menu "Vai"**: usando il percorso UNC dei prerequisiti, convertire la doppia barra rovesciata iniziale () in `\\` `smb://` e tutte le altre barre rovesciate ( `\` ) per l'avanzamento delle barre ( `/` ). Il collegamento sarà simile al seguente: ![Finestra di dialogo "Connect to Server" (Connetti al server)](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Usare il nome e la chiave dell'account di archiviazione quando vengono chiesti un nome utente e una password**: quando si fa clic su "Connessione" nella finestra di dialogo "Connetti al server", verranno chiesti il nome utente e la password. Verrà automaticamente inserito il nome utente macOS. È possibile inserire il nome o la chiave dell'account di archiviazione nel keychain di macOS.

4. **Usare la condivisione file di Azure nel modo desiderato**: dopo avere sostituito il nome utente e la password con il nome condivisione e la chiave dell'account di archiviazione, la condivisione verrà montata. È possibile usarla come qualsiasi altra cartella/condivisione file locale, anche ad esempio per trascinare e rilasciare file nella condivisione file:

    ![Snapshot di una condivisione file di Azure montata](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montare una condivisione file di Azure tramite il terminale
1. Sostituire  `<storage-account-name>` , `<storage-account-key>` e `<share-name>`   con i valori appropriati per l'ambiente in uso.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Usare la condivisione file di Azure nel modo desiderato**: la condivisione file di Azure verrà montata nel punto di montaggio specificato dal comando precedente.  

    ![Snapshot della condivisione file di Azure montata](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passaggi successivi
* [Connettere il Mac a computer e server condivisi-supporto Apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)