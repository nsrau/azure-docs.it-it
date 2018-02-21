---
title: Montare una condivisione file di Azure tramite SMB con macOS | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB con macOS.
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
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montare una condivisione file di Azure tramite SMB con macOS
[File di Azure](storage-files-introduction.md) è un servizio di Microsoft che consente di creare e usare condivisioni file di rete in Azure usando lo standard di settore. Le condivisioni file di Azure possono essere montate in macOS Sierra (10.12) ed El Capitan (10.11). Questo articolo illustra due diversi modi di montare una condivisione file di Azure in macOS con l'interfaccia utente del Finder e usando il terminale.

> [!Note]  
> Prima di montare una condivisione file di Azure tramite SMB, è consigliabile disabilitare la firma dei pacchetti SMB. In caso contrario, si potrebbe verificare una riduzione delle prestazioni quando si accede alla condivisione file di Azure da macOS. La connessione SMB verrà crittografata e la sicurezza della connessione non risulterà compromessa. Dal terminale i comandi seguenti disabiliteranno la firma dei pacchetti SMB, come illustrato da questo [articolo del supporto Apple sulla disabilitazione della firma dei pacchetti SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Prerequisiti per il montaggio di una condivisione file di Azure in macOS
* **Nome dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave dell'account primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445. Nel computer client (Mac) verificare che il firewall non blocchi la porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montare una condivisione file di Azure tramite il Finder
1. **Aprire Finder**: per impostazione predefinita, il Finder è aperto in macOS, ma è possibile assicurarsi che sia l'applicazione attualmente selezionata facendo clic sull'icona con il volto di macOS sul Dock:  
    ![Icona con il volto di macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Scegliere "Connect to Server" (Connetti al server) dal menu "Vai"**: usando il percorso UNC dei [prerequisiti](#preq), convertire la doppia barra rovesciata iniziale (`\\`) in `smb://` e tutte le altre barre rovesciate (`\`) in barre (`/`). Il collegamento sarà simile al seguente: ![Finestra di dialogo "Connect to Server" (Connetti al server)](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Usare il nome condivisione e la chiave dell'account di archiviazione quando vengono chiesti un nome utente e una password**: quando si fa clic su "Connessione" nella finestra di dialogo "Connect to Server" (Connetti al server), verranno chiesti il nome utente e la password. Verrà automaticamente inserito il nome utente macOS. È possibile inserire il nome condivisione o la chiave dell'account di archiviazione nel portachiavi di macOS.

4. **Usare la condivisione file di Azure nel modo desiderato**: dopo avere sostituito il nome utente e la password con il nome condivisione e la chiave dell'account di archiviazione, la condivisione verrà montata. È possibile usarla come qualsiasi altra cartella/condivisione file locale, anche ad esempio per trascinare e rilasciare file nella condivisione file:

    ![Snapshot di una condivisione file di Azure montata](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montare una condivisione file di Azure tramite il terminale
1. Sostituire `<storage-account-name>` con il nome del proprio account di archiviazione. Specificare la chiave dell'account di archiviazione come password quando viene chiesta. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Usare la condivisione file di Azure nel modo desiderato**: la condivisione file di Azure verrà montata nel punto di montaggio specificato dal comando precedente.  

    ![Snapshot della condivisione file di Azure montata](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti.

* [Articolo del supporto Apple: Come connettersi con Condivisione file sul Mac](https://support.apple.com/HT204445)
* [DOMANDE FREQUENTI](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Risoluzione dei problemi in Linux](storage-troubleshoot-linux-file-connection-problems.md)    