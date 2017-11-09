---
title: Montare una condivisione file di Azure e accedere alla condivisione in Windows | Microsoft Docs
description: Montare una condivisione file di Azure e accedere alla condivisione in Windows.
services: storage
documentationcenter: na
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
ms.openlocfilehash: 5134fab447f1d1842369aeda4ebc1948a5d78262
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Montare una condivisione file di Azure e accedere alla condivisione in Windows
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate in Windows e Windows Server. Questo articolo illustra tre diversi modi per montare una condivisione file di Azure in Windows: con l'interfaccia utente di Esplora file, tramite PowerShell e tramite il prompt dei comandi. 

Per montare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'area di Azure diversa, il sistema operativo deve supportare SMB 3.0. 

È possibile montare le condivisioni file di Azure in un'installazione Windows in esecuzione in una VM di Azure o in locale. La tabella seguente elenca le versioni del sistema operativo che supportano il montaggio delle condivisioni in un determinato ambiente:

| Versione di Windows        | Versione SMB | Montabile in una VM di Azure | Montabile in locale |
|------------------------|-------------|-----------------------|----------------------|
| Canale semestrale Windows Server<sup>1</sup> | SMB 3.0 | Sì | Sì |
| Windows 10<sup>2</sup>  | SMB 3.0 | Sì | Sì |
| Windows Server 2016    | SMB 3.0     | Sì                   | Sì                  |
| Windows 8.1            | SMB 3.0     | Sì                   | Sì                  |
| Windows Server 2012 R2 | SMB 3.0     | Sì                   | Sì                  |
| Windows Server 2012    | SMB 3.0     | Sì                   | Sì                  |
| Windows 7              | SMB 2.1     | Sì                   | No                   |
| Windows Server 2008 R2 | SMB 2.1     | Sì                   | No                   |

<sup>1</sup>Windows Server versione 1709.  
<sup>2</sup>Windows 10 versioni 1507, 1607, 1703 e 1709.

> [!Note]  
> È sempre consigliabile seguire l'articolo della KB più recente per la propria versione di Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Prerequisiti per il montaggio della condivisione file di Azure con Windows 
* **Nome dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave dell'account primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: File di Azure usa il protocollo SMB. SMB comunica tramite la porta TCP 445: verificare che il firewall non blocchi le porte TCP 445 dal computer client.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Montare la condivisione file di Azure con Esplora file
> [!Note]  
> Tenere presente che le istruzioni seguenti si basano su Windows 10 e potrebbero essere leggermente diverse per le versioni precedenti. 

1. **Aprire Esplora file**: per eseguire questa operazione, aprire il menu Start o premere i tasti di scelta rapida Win+E.

2. **Passare all'elemento "Questo PC" a sinistra della finestra. Verranno cambiati i menu disponibili sulla barra multifunzione. Scegliere "Connetti unità di rete" dal menu Computer**.
    
    ![Screenshot del menu a discesa "Connetti unità di rete"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Copiare il percorso UNC dal riquadro "Connetti" nel portale di Azure**: per una descrizione dettagliata di come trovare queste informazioni, vedere [qui](storage-how-to-use-files-portal.md#connect-to-file-share).

    ![Il percorso UNC dal riquadro Connetti di File di Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **Selezionare la lettera di unità e immettere il percorso UNC.** 
    
    ![Screenshot della finestra di dialogo "Connetti unità di rete"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Usare il nome account di archiviazione preceduto da `Azure\` come nome utente e una chiave dell'account di archiviazione come password.**
    
    ![Screenshot della finestra di dialogo delle credenziali di rete](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Usare la condivisione file di Azure nel modo desiderato**.
    
    ![La condivisione file di Azure viene ora montata](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Per smontare (o disconnettere) la condivisione file di Azure, fare clic con il pulsante destro del mouse sulla voce relativa alla condivisione in "Percorsi di rete" in Esplora file e scegliere "Disconnetti"**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Montare la condivisione file di Azure con PowerShell
1. **Usare il comando seguente per montare la condivisione file di Azure**: ricordare di sostituire `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` con le informazioni appropriate.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Usare la condivisione file di Azure nel modo desiderato**.

3. **Al termine, smontare la condivisione file di Azure usando il comando seguente**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> È possibile usare il parametro `-Persist` in `New-PSDrive` per rendere la condivisione file di Azure visibile al resto del sistema operativo durante il montaggio.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Montare la condivisione file di Azure con il prompt dei comandi
1. **Usare il comando seguente per montare la condivisione file di Azure**: ricordare di sostituire `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` con le informazioni appropriate.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Usare la condivisione file di Azure nel modo desiderato**.

3. **Al termine, smontare la condivisione file di Azure usando il comando seguente**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> È possibile configurare la condivisione file di Azure per la riconnessione automatica al riavvio rendendo persistenti le credenziali in Windows. Il comando seguente renderà persistenti le credenziali:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti.

* [Domande frequenti](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>Articoli concettuali e video
* [File di Azure: un file system SMB nel cloud senza conflitti per Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Come usare File di Azure con Linux](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-files"></a>Supporto degli strumenti per File di Azure
* [Come usare AzCopy con Archiviazione di Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Risoluzione dei problemi di File di Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risoluzione dei problemi di File di Azure - Linux](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Post di BLOG
* [Azure Files is now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/) (File di Azure è ora disponibile a livello generale)
* [Inside Azure Files](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Analisi di File di Azure)
* [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File (Migrazione dei dati in File di Azure)](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Riferimento
* [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)
