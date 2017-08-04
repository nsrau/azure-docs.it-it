---
title: Usare l'archiviazione file di Azure con Linux | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB in Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 03efe81383a6c2fdfe50551355c33dc7af3837e8
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-file-storage-with-linux"></a>Usare l'archiviazione file di Azure con Linux
[Archiviazione file di Azure](storage-dotnet-how-to-use-files.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni di Linux usando il [pacchetto cifs-utils](https://wiki.samba.org/index.php/LinuxCIFS_utils) dal [progetto Samba](https://www.samba.org/). Questo articolo illustra due modi per montare una condivisione file di Azure: su richiesta con il comando `mount` e all'avvio creando una voce in `/etc/fstab`.

> [!NOTE]  
> Per montare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'area di Azure diversa, il sistema operativo deve supportare la funzionalità di crittografia di SMB 3.0. La funzionalità di crittografia per SMB 3.0 per Linux è stata introdotta nel kernel 4.11. Questa funzionalità consente di montare la condivisione file di Azure in locale o in un'altra area di Azure. Al momento della pubblicazione, di questa funzionalità è stato eseguito il backport in Ubuntu 17.04 e in Ubuntu 16.10.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Prerequisiti per il montaggio di una condivisione file di Azure con Linux e il pacchetto cifs-utils
* **Selezionare una distribuzione Linux in cui sia possibile installare il pacchetto cifs-utils**: Microsoft consiglia le distribuzioni Linux seguenti nella raccolta immagini di Azure:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12   

    > [!Note]  
    > Con la condivisione file di Azure è possibile usare qualsiasi distribuzione Linux che scarichi e installi o compili versioni recenti del pacchetto cifs-utils.

* <a id="install-cifs-utils"></a>**Il pacchetto cifs-utils è installato**: il pacchetto cifs-utils può essere installato tramite l'utilità di gestione dei pacchetti nella distribuzione Linux scelta. 

    Nelle distribuzioni basate su **Ubuntu** e **Debian** usare l'utilità di gestione dei pacchetti `apt-get`:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    In **RHEL** e **CentOS** usare l'utilità di gestione dei pacchetti `yum`:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    In **openSUSE** usare l'utilità di gestione dei pacchetti `zypper`:

    ```
    sudo zypper install samba*
    ```

    In altre distribuzioni usare l'utilità di gestione dei pacchetti appropriata o [compilare il codice sorgente](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Scegliere le autorizzazioni per le directory e i file della condivisione montata**: negli esempi seguenti viene usato 0777 per concedere le autorizzazioni di lettura, scrittura ed esecuzione a tutti gli utenti. È possibile sostituirlo con altre [autorizzazioni chmod](https://en.wikipedia.org/wiki/Chmod) in base alle proprie esigenze. 

* **Nome dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave dell'account primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445. Verificare che il firewall non blocchi le porte TCP 445 dal computer client.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montare la condivisione file di Azure su richiesta con `mount`
1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)**.

2. **Creare una cartella per il punto di montaggio**: questa operazione può essere eseguita in qualsiasi posizione nel file system.

    ```
    mkdir mymountpoint
    ```

3. **Usare il comando mount per montare la condivisione file di Azure**: ricordarsi di sostituire `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, con le informazioni appropriate.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Dopo che la condivisione file di Azure è stata completata, è possibile usare `sudo umount ./mymountpoint` per smontare la condivisione.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creare un punto di montaggio permanente per la condivisione file di Azure con `/etc/fstab`
1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)**.

2. **Creare una cartella per il punto di montaggio**: questa operazione può essere eseguita in qualsiasi posizione nel file system, ma è necessario prendere nota del percorso assoluto della cartella. L'esempio seguente crea una cartella nella radice.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Usare il comando seguente per accodare la riga seguente a `/etc/fstab`: ricordarsi di sostituire** , `<storage-account-name>`, `<share-name>`, `<storage-account-key>` con le informazioni appropriate.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Per montare la condivisione file di Azure dopo la modifica, è possibile usare `sudo mount -a` `/etc/fstab` anziché riavviare il computer.

## <a name="feedback"></a>Commenti e suggerimenti
Siamo interessati all'opinione degli utenti Linux.

L'archiviazione di file di Azure per il gruppo di utenti Linux fornisce un forum per condividere commenti e suggerimenti durante la valutazione e l'adozione dell'archiviazione di file su Linux. Inviare un messaggio di posta elettronica ad [Azure File storage Linux Users](mailto:azurefileslinuxusers@microsoft.com) per entrare nel gruppo degli utenti.

## <a name="next-steps"></a>Passaggi successivi
Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.
* [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md)
* [Come usare AzCopy con Archiviazione di Microsoft Azure](storage-use-azcopy.md)
* [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](storage-azure-cli.md#create-and-manage-file-shares)
* [Domande frequenti](storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-file-connection-problems.md)

