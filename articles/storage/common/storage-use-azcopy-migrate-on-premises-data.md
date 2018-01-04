---
title: Eseguire la migrazione di dati locali in archiviazione di Azure con AzCopy | Documenti Microsoft
description: Usare AzCopy per la migrazione dei dati o copiare dati da o in blob, tabelle e contenuto del file. Facilmente la migrazione dei dati dall'archivio locale all'archiviazione di Azure.
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: f2c0b80248ef706394b3f84df4c3da26fb79026a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Eseguire la migrazione di dati in locale nell'archiviazione cloud con AzCopy

AzCopy è un'utilità della riga di comando progettata per la copia dei dati a/da archiviazione Blob di Microsoft Azure, File e tabella, utilizzando comandi semplici progettati per prestazioni ottimali. È possibile copiare dati tra un file system e un account di archiviazione o tra gli account di archiviazione.  

Esistono due versioni di AzCopy che è possibile scaricare:

* [AzCopy in Linux](storage-use-azcopy.md) viene compilato con .NET Framework di base, destinato a piattaforme Linux offerta stile POSIX opzioni della riga di comando. 
* [AzCopy in Windows](../storage-use-azcopy.md) viene compilato con .NET Framework e offre opzioni della riga di comando in stile Windows. 
 
In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione 
> * Usare AzCopy per caricare tutti i dati
> * Modificare i dati a scopo di test
> * Creare un processo pianificato di attività o cron per identificare i nuovi file da caricare

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione: 

* Scaricare la versione più recente di AzCopy su [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) o [Windows](http://aka.ms/downloadazcopy). 

## <a name="log-in-to-the-azure-portalhttpsportalazurecom"></a>Accedere al [Portale di Azure](https://portal.azure.com/).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Se si desidera essere in grado di scaricare i BLOB da un'area secondaria per l'archiviazione locale e viceversa, impostare **replica** a **archiviazione lettura-accesso-con ridondanza geografica**. Se si seleziona questa opzione Crea un [archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) account. 
>
>

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. Contenitori consente di organizzare i gruppi di BLOB come organizzare i file nel computer in cartelle. 

Seguire questi passaggi per creare un contenitore:

1. Selezionare il **gli account di archiviazione** nella pagina principale e selezionare l'account di archiviazione creato.
2. Selezionare **BLOB** in **servizi**, quindi selezionare **contenitore**. 

![Creare un contenitore](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-). Per altre informazioni sulla denominazione dei BLOB e dei contenitori, vedere l'articolo relativo alla [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Caricare tutti i file in una cartella di archiviazione Blob

È possibile usare AzCopy per caricare tutti i file in una cartella di archiviazione Blob su [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) o [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Per caricare tutti i BLOB in una cartella, immettere il seguente comando di AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Sostituire  **\<chiave\>**  con la chiave dell'account. Nel portale di Azure, è possibile recuperare la chiave dell'account selezionando **le chiavi di accesso** in impostazioni nell'account di archiviazione. Selezionare una chiave e incollarlo nel comando AzCopy. Se il contenitore di destinazione specificato non esiste, AzCopy ne crea uno e vi carica il file. Aggiornare il percorso di origine nella directory di dati e sostituire **myaccount** nell'URL di destinazione per il nome di account di archiviazione.

Specificare il `--recursive` e `/S` opzioni, Linux e Windows, rispettivamente, per caricare il contenuto della directory specificata in modo ricorsivo archiviazione Blob. Quando si esegue AzCopy con una di queste opzioni, tutte le sottocartelle e i file vengono caricati anche.

## <a name="upload-modified-files-to-blob-storage"></a>Caricare i file modificati in archiviazione Blob
È possibile usare AzCopy per [caricare file](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) in base all'ora ultima modifica. Per provare questa, modificare o creare nuovi file nella directory di origine per scopi di test. Per caricare i file nuovi o aggiornati solo, aggiungere `--exclude-older`o `/XO` parametro per AzCopy Linux e le finestre di comando rispettivamente.

Se si desidera copiare le risorse di origine che non esistono nella destinazione, specificare sia `--exclude-older` e `--exclude-newer` o `/XO` e `/XN` comando rispettivamente di parametri nella AzCopy Linux e Windows. AzCopy carica solo i dati aggiornati in base al relativo timestamp.
 
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

## <a name="create-a-scheduled-task-or-cron-job"></a>Creare un processo pianificato di attività o cron 
È possibile creare un attività/cron processo che esegue uno script di comandi AzCopy che identifica e carica i nuovi dati in locale nell'archiviazione cloud a un intervallo di tempo specifico. 

Copiare il comando di AzCopy per un editor di testo. Aggiornare i valori dei parametri del comando di AzCopy per i valori appropriati. Salvare il file come `script.sh` o `script.bat` per AzCopy su Linux e Windows rispettivamente. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy viene eseguito con verbose `--verbose` (Linux) e `/V` opzione (Windows) e l'output viene reindirizzato in un file di log. 

In questa esercitazione, [senza](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) viene utilizzato per creare un'attività pianificata in Windows, e [Crontab](http://crontab.org/) comando viene utilizzato per creare un processo cron in Linux. 
 **Senza** consente agli amministratori di creare, eliminare, eseguire una query, modificare, eseguire e le attività pianificate nel computer locale o remoto. **Cron** consente agli utenti di Linux e Unix eseguire comandi o script alla data e ora utilizzando [espressione cron](https://en.wikipedia.org/wiki/Cron#CRON_expression)


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
**Per creare un processo cron in Linux**, immettere il comando seguente su Terminal Server. 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Specificare l'espressione cron `*/5 * * * * ` nel comando indica lo script della shell `script.sh` deve essere eseguita ogni cinque minuti. Lo script è possibile pianificare l'esecuzione in un momento specifico, giornaliera, mensile o annuo. Vedere [cron espressioni](https://en.wikipedia.org/wiki/Cron#CRON_expression) per ulteriori informazioni sull'impostazione di data e ora per l'esecuzione del processo. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
**Per creare un'attività pianificata in Windows**, immettere il comando seguente nel prompt dei comandi o PowerShell.

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Il comando Usa il `/SC` parametro per specificare una pianificazione minuta e `/MO` parametro per specificare un intervallo di cinque minuti. Il `/TN` parametro viene utilizzato per specificare il nome dell'attività, e `/TR` parametro per specificare il percorso di `script.bat` file. Visitare [senza](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes) per ulteriori informazioni sulla creazione di attività pianificate in Windows.

---
 
Per convalidare l'attività pianificata/cron processo viene eseguito correttamente, creare nuovi file nella directory di `myfolder`. Attendere cinque minuti per verificare che i nuovi file caricati all'account di archiviazione. Passare alla directory di log per visualizzare i log di output del processo pianificato attività/cron. 

Visitare [spostamento in locale dei dati](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) ulteriori informazioni sui modi per spostare i dati in locale nell'archiviazione di Azure e viceversa.  

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### <a name="azure-storage-documentation"></a>Documentazione di Archiviazione di Azure
* [Introduzione ad Archiviazione di Azure](../storage-introduction.md)
* [Trasferimento dati con AzCopy in Windows](storage-use-azcopy.md) 
* [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md) 
* [Creare un account di archiviazione](../storage-create-storage-account.md)
* [Gestire BLOB con Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

