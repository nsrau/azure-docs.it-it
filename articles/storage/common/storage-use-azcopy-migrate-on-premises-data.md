---
title: Eseguire la migrazione di dati locali in Archiviazione di Azure tramite AzCopy| Microsoft Docs
description: Usare AzCopy per migrare o copiare dati da o verso BLOB, tabelle e contenuto di file. Migrare facilmente dati dalla risorsa di archiviazione locale ad Archiviazione di Azure.
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: 3dbfb935ac0b134e127a5dccb7bc76716c688e8a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrare dati locali in una risorsa di archiviazione cloud tramite AzCopy

AzCopy è uno strumento da riga di comando per la copia di dati da o verso l'archiviazione BLOB di Azure, File di Azure e Archiviazione tabelle di Azure usando semplici comandi progettati per garantire prestazioni ottimali. È possibile copiare dati tra un file system e un account di archiviazione o tra più account di archiviazione.  

Esistono due versioni di AzCopy che è possibile scaricare.

* [AzCopy in Linux](storage-use-azcopy.md), compilato con .NET Core Framework, offre opzioni della riga di comando di tipo POSIX per piattaforme Linux. 
* [AzCopy in Windows](../storage-use-azcopy.md), compilato con .NET Framework, offre opzioni della riga di comando di tipo Windows. 
 
In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione. 
> * Usare AzCopy per caricare tutti i dati.
> * Modificare i dati a scopo di test.
> * Creare un'attività pianificata o un processo cron per identificare nuovi file da caricare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, scaricare la versione più recente di AzCopy su [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) o [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Se si vuole poter scaricare elementi BLOB da un'area secondaria in una risorsa di archiviazione locale e viceversa, impostare **Replica** su **Archiviazione con ridondanza geografica e accesso in lettura**. In questo modo, viene creato un account di [archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. I contenitori consentono di organizzare i gruppi di elementi BLOB in modo simile a come si organizzano i file in cartelle sul computer. 

Per creare un contenitore, seguire questa procedura:

1. Selezionare il pulsante **Account di archiviazione** nella pagina principale e scegliere l'account di archiviazione creato.
2. Selezionare **BLOB** in **Servizi** e quindi selezionare **Contenitore**. 

   ![Creare un contenitore](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il segno meno (-). Per altre informazioni sulla denominazione dei BLOB e dei contenitori, vedere l'articolo relativo alla [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Caricare tutti i file di una cartella nell'archiviazione BLOB

È possibile usare AzCopy per caricare tutti i file di una cartella nell'archiviazione BLOB di [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) o [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Per caricare tutti i BLOB di una cartella, immettere il comando AzCopy seguente:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Sostituire `<key>` e `key` con la chiave dell'account. Nel portale di Azure è possibile recuperare la chiave dell'account selezionando **Chiavi di accesso** nelle **Impostazioni** dell'account di archiviazione. Selezionare una chiave e incollarla nel comando AzCopy. Se il contenitore di destinazione specificato non esiste, AzCopy ne crea uno e vi carica il file. Aggiornare il percorso di origine alla directory di dati e sostituire **myaccount** nell'URL di destinazione con il nome dell'account di archiviazione.

Per caricare in modo ricorsivo i contenuti della directory specificata nell'archiviazione BLOB, specificare l'opzione `--recursive` (Linux) o `/S` (Windows). Quando si esegue AzCopy con una di queste opzioni, vengono caricate anche tutte le sottocartelle e i relativi file.

## <a name="upload-modified-files-to-blob-storage"></a>Caricare i file modificati nell'archiviazione BLOB
È possibile usare AzCopy per [caricare i file](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) in base all'ora dell'ultima modifica. A questo scopo, modificare o creare nuovi file nella directory di origine a scopo di test. Per caricare solo i file nuovi o aggiornati, aggiungere al comando AzCopy il parametro `--exclude-older` (Linux) o `/XO` (Windows).

Se si vogliono copiare solo le risorse di origine non ancora disponibili nella destinazione, specificare nel comando AzCopy entrambi i parametri: `--exclude-older` e `--exclude-newer` (Linux) o `/XO` e `/XN` (Windows). AzCopy carica solo i dati aggiornati, in base al relativo timestamp.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Creare un'attività pianificata o un processo cron 
È possibile creare un'attività pianificata o un processo cron che esegue uno script di comandi AzCopy. Lo script identifica i nuovi dati locali e li carica nella risorsa di archiviazione cloud a specifici intervalli di tempo. 

Copiare il comando AzCopy in un editor di testo. Aggiornare i valori di parametro del comando AzCopy con i valori appropriati. Salvare il file come `script.sh` (Linux) o `script.bat` (Windows) per AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy viene eseguito con l'opzione verbose `--verbose` (Linux) o `/V` (Windows). L'output viene reindirizzato in un file di log. 

In questa esercitazione viene usato il comando [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) per creare un'attività pianificata in Windows, il comando [Crontab](http://crontab.org/) per creare un processo cron in Linux. 
 **Schtasks** consente agli amministratori di creare, eliminare, interrogare, modificare, eseguire e terminare attività pianificate in un computer locale o remoto. **Cron** consente agli utenti di Linux e Unix di eseguire comandi o script in una data e a un'ora specifiche usando [espressioni cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Per creare un processo cron in Linux, immettere il comando seguente su un terminale: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Se nel comando si specifica l'espressione cron `*/5 * * * * `, si indica che lo script di shell `script.sh` deve essere eseguito ogni cinque minuti. È possibile pianificare lo script in modo che venga eseguito a un'ora specifica ogni giorno, mese o anno. Per altre informazioni sull'impostazione della data e dell'ora di esecuzione del processo, vedere la sezione sulle [espressioni cron](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Per creare un'attività pianificata in Windows, immettere il comando seguente al prompt dei comandi o in PowerShell:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Il comando usa:
- Il parametro `/SC` per specificare una pianificazione minuta.
- Il parametro `/MO` per specificare un intervallo di cinque minuti.
- Il parametro `/TN` per specificare il nome dell'attività.
- Il parametro `/TR` per specificare il percorso al file `script.bat`. 

Per altre informazioni sulla creazione di un'attività pianificata in Windows, vedere [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Per verificare che l'attività pianificata o il processo cron venga eseguito correttamente, creare nuovi file nella directory `myfolder`. Attendere cinque minuti per verificare che i nuovi file siano stati caricati nell'account di archiviazione. Passare alla directory di log per visualizzare i log di output dell'attività pianificata o del processo cron. 

Per altre informazioni sulle modalità disponibili per spostare dati locali in Archiviazione di Azure e viceversa, vedere [Spostare dati da e verso Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

* [Introduzione ad Archiviazione di Azure](../storage-introduction.md)
* [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) 
* [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md) 
* [Creare un account di archiviazione](../storage-create-storage-account.md)
* [Gestire BLOB con Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

