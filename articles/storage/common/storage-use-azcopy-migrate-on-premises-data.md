---
title: 'Esercitazione: Eseguire la migrazione di dati locali in Archiviazione di Azure tramite AzCopy| Microsoft Docs'
description: In questa esercitazione verrà usato AzCopy per migrare o copiare dati da o verso BLOB, tabelle e contenuto di file. Migrare facilmente dati dalla risorsa di archiviazione locale ad Archiviazione di Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 64d79abd1e142a231c08e02e7d62e8bfbab7b90e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244732"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Esercitazione: Migrare dati locali in una risorsa di archiviazione cloud tramite AzCopy

AzCopy è uno strumento da riga di comando per la copia di dati da o verso l'archiviazione BLOB di Azure, File di Azure e Archiviazione tabelle di Azure usando semplici comandi progettati per garantire prestazioni ottimali. Con AzCopy è possibile copiare dati tra un file system e un account di archiviazione o tra più account di archiviazione. È possibile usare AzCopy per copiare i dati di uno spazio dati locale in un account di archiviazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione. 
> * Usare AzCopy per caricare tutti i dati.
> * Modificare i dati a scopo di test.
> * Creare un'attività pianificata o un processo cron per identificare nuovi file da caricare.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, scaricare l'ultima versione di AzCopy. Vedere [Introduzione ad AzCopy](storage-use-azcopy-v10.md).

Se si usa Windows, è necessario [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) perché viene usato in questa esercitazione per pianificare un'attività. Gli utenti di Linux useranno invece il comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Creare un contenitore

Il primo passaggio consiste nel creare un contenitore perché i BLOB devono sempre essere caricati in un contenitore. I contenitori consentono di organizzare i gruppi di BLOB in modo analogo a come si organizzano i file in cartelle del computer.

Per creare un contenitore, seguire questa procedura:

1. Selezionare il pulsante **Account di archiviazione** nella pagina principale e scegliere l'account di archiviazione creato.
2. Selezionare **BLOB** in **Servizi** e quindi selezionare **Contenitore**.

   ![Creare un contenitore](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il segno meno (-). Per altre informazioni sulla denominazione dei BLOB e dei contenitori, vedere l'articolo relativo alla [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Scaricare AzCopy

Scaricare il file eseguibile di AzCopy v10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Salvare il file di AzCopy in una posizione qualsiasi sul computer. Aggiungere la posizione del file alla variabile del percorso di sistema per poter fare riferimento a questo file eseguibile da qualsiasi cartella nel computer.

## <a name="authenticate-with-azure-ad"></a>Eseguire l'autenticazione con Azure AD

Assegnare per prima cosa il ruolo [Collaboratore ai dati dei BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) alla propria identità. Vedere [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Aprire quindi un prompt dei comandi, digitare il comando seguente e premere INVIO.

```azcopy
azcopy login
```

Questo comando restituisce un codice di autenticazione e l'URL di un sito Web. Aprire il sito Web, immettere il codice e quindi scegliere il pulsante **Avanti**.

![Creare un contenitore](media/storage-use-azcopy-v10/azcopy-login.png)

Verrà visualizzata una finestra di accesso. In tale finestra accedere al proprio account Azure usando le relative credenziali. Dopo aver completato l'accesso, è possibile chiudere la finestra del browser e iniziare a usare AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Caricare il contenuto di una cartella nell'archiviazione BLOB

È possibile usare AzCopy per caricare tutti i file di una cartella nell'archiviazione BLOB di [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) o [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Per caricare tutti i BLOB di una cartella, immettere il comando AzCopy seguente:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Sostituire il segnaposto `<local-folder-path>` con il percorso di una cartella contenente file, ad esempio `C:\myFolder` o `/mnt/myFolder`.

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il segnaposto `<container-name>` con il nome del contenitore creato.

Per caricare in modo ricorsivo il contenuto della directory specificata nell'archivio BLOB, specificare l'opzione `--recursive`. Quando si esegue AzCopy con questa opzione, vengono caricate anche tutte le sottocartelle con i relativi file.

## <a name="upload-modified-files-to-blob-storage"></a>Caricare i file modificati nell'archiviazione BLOB

È possibile usare AzCopy per caricare i file in base all'ora dell'ultima modifica. 

A questo scopo, modificare o creare nuovi file nella directory di origine a scopo di test. Usare quindi il comando `sync` di AzCopy.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Sostituire il segnaposto `<local-folder-path>` con il percorso di una cartella contenente file, ad esempio `C:\myFolder` o `/mnt/myFolder`.

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il segnaposto `<container-name>` con il nome del contenitore creato.

Per altre informazioni sul comando `sync`, vedere [Sincronizzare i file](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Creare un'attività pianificata

È possibile creare un'attività pianificata o un processo cron che esegue uno script di comandi AzCopy. Lo script identifica i nuovi dati locali e li carica nella risorsa di archiviazione cloud a specifici intervalli di tempo.

Copiare il comando AzCopy in un editor di testo. Aggiornare i valori di parametro del comando AzCopy con i valori appropriati. Salvare il file come `script.sh` (Linux) o `script.bat` (Windows) per AzCopy. 

Questi esempi presuppongono che la cartella sia denominata `myFolder`, che il nome dell'account di archiviazione sia `mystorageaccount` e che il nome del contenitore sia `mycontainer`.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

In questa esercitazione viene usato il comando [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) per creare un'attività pianificata in Windows, il comando [Crontab](http://crontab.org/) per creare un processo cron in Linux.

 **Schtasks** consente agli amministratori di creare, eliminare, interrogare, modificare, eseguire e terminare attività pianificate in un computer locale o remoto. **Cron** consente agli utenti di Linux e Unix di eseguire comandi o script in una data e a un'ora specifiche usando [espressioni cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Per creare un processo cron in Linux, immettere il comando seguente su un terminale:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Se nel comando si specifica l'espressione cron `*/5 * * * *`, si indica che lo script di shell `script.sh` deve essere eseguito ogni cinque minuti. È possibile pianificare lo script in modo che venga eseguito a un'ora specifica ogni giorno, mese o anno. Per altre informazioni sull'impostazione della data e dell'ora di esecuzione del processo, vedere la sezione sulle [espressioni cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Per creare un'attività pianificata in Windows, immettere il comando seguente al prompt dei comandi o in PowerShell:

Questo esempio presuppone che lo script si trovi nell'unità radice del computer, ma può trovarsi in qualsiasi posizione si preferisca.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Il comando usa:
- Il parametro `/SC` per specificare una pianificazione minuta.
- Il parametro `/MO` per specificare un intervallo di cinque minuti.
- Il parametro `/TN` per specificare il nome dell'attività.
- Il parametro `/TR` per specificare il percorso al file `script.bat`.

Per altre informazioni sulla creazione di un'attività pianificata in Windows, vedere [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Per verificare che l'attività pianificata o il processo cron venga eseguito correttamente, creare nuovi file nella directory `myFolder`. Attendere cinque minuti per verificare che i nuovi file siano stati caricati nell'account di archiviazione. Passare alla directory di log per visualizzare i log di output dell'attività pianificata o del processo cron.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle modalità disponibili per spostare dati locali in Archiviazione di Azure e viceversa, vedere l'articolo seguente:

* [Spostamento dei dati da e verso Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Per altre informazioni su AzCopy, vedere gli articoli seguenti:

* [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

* [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

* [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

* [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)
 
* [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
