---
title: Ripristinare le condivisioni file di Azure con l'interfaccia della riga di comando di AzureRestore Azure file shares with the Azure CLI
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per ripristinare le condivisioni file di Azure di cui è stato eseguito il backup nell'insieme di credenziali dei servizi di ripristino
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757098"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Ripristinare le condivisioni file di Azure con l'interfaccia della riga di comando di AzureRestore Azure file shares with the Azure CLI

L'interfaccia della riga di comando di Azure offre un'esperienza della riga di comando per la gestione delle risorse di Azure.The Azure CLI provides a command-line experience for managing Azure resources. È un ottimo strumento per la creazione di automazione personalizzata per usare le risorse di Azure.It's a great tool for building custom automation to use Azure resources. Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da Backup di Azure usando l'interfaccia della riga di comando di Azure.This article explains how to restore an entire file share or specific files from a restore point created by [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) by using the Azure CLI. È anche possibile eseguire questa procedura con [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) o nel [portale di Azure](backup-afs.md).

Alla fine di questo articolo, si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:By the end of this article, you'll learn how to perform the following operations with the Azure CLI:

* Visualizzare i punti di ripristino per una condivisione file di Azure di cui è stato eseguito il backup.
* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.

>[!NOTE]
> Azure Backup now supports restoring multiple files or folders to the original or an alternate location using Azure CLI. Per ulteriori informazioni, fare riferimento alla sezione Ripristinare più file o cartelle nel [percorso originale o in un percorso alternativo](#restore-multiple-files-or-folders-to-original-or-alternate-location) di questo documento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che tu abbia già una condivisione file di Azure di cui è stato eseguito il backup da Backup di Azure.This article assumes that you already have an Azure file share that's backed up by Azure Backup. Se non si dispone di uno, vedere Eseguire il backup delle condivisioni file di [Azure con l'interfaccia della riga di comando](backup-afs-cli.md) per configurare il backup per la condivisione file. Per questo articolo, si utilizzano le risorse seguenti:For this article, you use the following resources:

| Condivisione file  | Account di archiviazione | Region | Dettagli                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *file di azure*  | *afsaccount*      | EastUS | Origine originale di cui è stato eseguito il backup tramite Backup di AzureOriginal source backed up by using Azure Backup                 |
| *file di azure1* | *afaccount1*      | EastUS | Origine di destinazione utilizzata per il ripristino di percorsi alternativi |

È possibile utilizzare una struttura simile per le condivisioni file per provare i diversi tipi di ripristini illustrati in questo articolo.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Recuperare i punti di ripristino per la condivisione file di AzureFetch recovery points for the Azure file share

Utilizzare il cmdlet [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) per elencare tutti i punti di ripristino per la condivisione file di cui è stato eseguito il backup.

L'esempio seguente recupera l'elenco dei punti di ripristino per la condivisione file *azurefiles* nell'account di archiviazione *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

È inoltre possibile eseguire il cmdlet precedente utilizzando il nome descrittivo per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Il set di risultati è un elenco di punti di ripristino con dettagli relativi a tempo e coerenza per ogni punto di ripristino.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

L'attributo **Name** nell'output corrisponde al nome del punto di ripristino che può essere utilizzato come valore per il parametro **--rp-name** nelle operazioni di ripristino.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Ripristino di condivisione completo tramite l'interfaccia della riga di comando di AzureFull share recovery by using the Azure CLI

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o alternativo.

Definire i seguenti parametri per eseguire le operazioni di ripristino:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name:** nome della condivisione file originale di cui è stato eseguito il backup che si desidera utilizzare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Ripristinare una condivisione completa nel percorso originale

Quando si esegue il ripristino in una posizione originale, non è necessario specificare i parametri relativi alla destinazione. Deve essere fornito solo **Risolvi conflitto.**

L'esempio seguente usa il cmdlet [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità di ripristino impostata *su originallocation* per ripristinare la condivisione file *azurefiles* nel percorso originale. Utilizzare il punto di ripristino 932883129628959823, ottenuto in Recupera punti di [ripristino per la condivisione file](#fetch-recovery-points-for-the-azure-file-share)di Azure:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Ripristinare una condivisione completa in un percorso alternativo

È possibile utilizzare questa opzione per ripristinare una condivisione file in un percorso alternativo e mantenere la condivisione file originale così com'è. Specificare i seguenti parametri per il ripristino della posizione alternativa:

* **--target-storage-account:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share:** condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: La cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **--resolve-conflict**: Istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

L'esempio seguente usa [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità restore come *alternatelocation* per ripristinare la condivisione file *azurefiles* nell'account di archiviazione *afsaccount* nella condivisione file *azurefiles1"* nell'account di archiviazione *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nella posizione originale o alternativa.

Definire i seguenti parametri per eseguire le operazioni di ripristino:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name:** nome della condivisione file originale di cui è stato eseguito il backup che si desidera utilizzare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Specificare i seguenti parametri per gli elementi che si desidera ripristinare:

* **SourceFilePath**: il percorso assoluto del file, da ripristinare all'interno della condivisione file, come stringa. Questo percorso è lo stesso percorso usato nel download del [file di archiviazione az](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) o nel file di archiviazione az che mostra [i](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) comandi dell'interfaccia della riga di comando.
* **SourceFileType**: scegliere se selezionare una directory o un file. Accetta **directory** o **file**.
* **ResolveConflict**: Istruzioni in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Ripristinare singoli file o cartelle nella posizione originale

Usare il cmdlet [az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata *su originallocation* per ripristinare file o cartelle specifici nella posizione originale.

L'esempio seguente ripristina il file RestoreTest.txt nel percorso originale, ovvero la condivisione file *azurefiles.The* following example restores the *RestoreTest.txt* file in its original location: the azurefiles file share.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Ripristinare singoli file o cartelle in un percorso alternativo

Per ripristinare file o cartelle specifici in un percorso alternativo, usare il cmdlet [az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata su *alternatelocation* e specificare i parametri correlati alla destinazione seguenti:

* **--target-storage-account:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share:** condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: La cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto di cui è stato eseguito il backup deve essere ripristinato in una cartella radice, assegnare il valore della cartella di destinazione come stringa vuota.

L'esempio seguente ripristina il file *RestoreTest.txt* originariamente presente nella condivisione file *azurefiles* in un percorso alternativo: la cartella *restoredata* nella condivisione file *azurefiles1* ospitata nell'account di archiviazione *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Ripristinare più file o cartelle nel percorso originale o alternativo

Per eseguire il ripristino per più elementi, passare il valore per il parametro **source-file-path** come percorsi separati da **spazi** di tutti i file o le cartelle che si desidera ripristinare.

Nell'esempio riportato di seguito vengono ripristinati i file *Report.docx* di prova *Di Restore.txt* e AFS nel percorso originale.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

L'output sarà simile al seguente:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

Se si desidera ripristinare più elementi in una posizione alternativa, utilizzare il comando precedente specificando i parametri relativi alla destinazione, come illustrato nella sezione Ripristinare singoli file o cartelle in [un percorso alternativo.](#restore-individual-files-or-folders-to-an-alternate-location)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire i backup di condivisione file di [Azure con l'interfaccia della riga di comando](manage-afs-backup-cli.md)di Azure.
