---
title: Ripristinare le condivisioni file di Azure con l'interfaccia della riga di comando
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per ripristinare le condivisioni file di Azure di backup nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757098"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Ripristinare le condivisioni file di Azure con l'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per gestire le risorse di Azure. Si tratta di uno strumento straordinario per creare un'automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) usando l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa procedura con [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) o nel [portale di Azure](backup-afs.md).

Alla fine di questo articolo si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:

* Visualizzare i punti di ripristino per una condivisione file di Azure di cui è stato eseguito il backup.
* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.

>[!NOTE]
> Backup di Azure supporta ora il ripristino di più file o cartelle nel percorso originale o in un percorso alternativo usando l'interfaccia della riga di comando di Azure. Per ulteriori informazioni, fare riferimento alla sezione [ripristinare più file o cartelle in un percorso originale o alternativo](#restore-multiple-files-or-folders-to-original-or-alternate-location) di questo documento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già presente una condivisione file di Azure di cui è stato eseguito il backup da backup di Azure. Se non si ha un file, vedere [eseguire il backup delle condivisioni file di Azure con l'interfaccia della](backup-afs-cli.md) riga di comando per configurare il backup per la condivisione file. Per questo articolo, è possibile usare le risorse seguenti:

| Condivisione file  | Account di archiviazione | Region | Dettagli                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *risorsa*  | *afsaccount*      | EastUS | Backup di origine originale con backup di Azure                 |
| *azurefiles1* | *afaccount1*      | EastUS | Origine di destinazione utilizzata per il ripristino del percorso alternativo |

È possibile usare una struttura simile per le condivisioni file per provare i diversi tipi di ripristini illustrati in questo articolo.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Recuperare i punti di ripristino per la condivisione file di Azure

Usare il cmdlet [AZ backup RecoveryPoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) per elencare tutti i punti di ripristino per la condivisione file di cui è stato eseguito il backup.

Nell'esempio seguente viene recuperato l'elenco dei punti di ripristino per la condivisione file *risorsa* nell'account di archiviazione *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

È anche possibile eseguire il cmdlet precedente usando il nome descrittivo per il contenitore e l'elemento fornendo i seguenti due parametri aggiuntivi:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Il set di risultati è un elenco di punti di ripristino con i dettagli relativi a tempo e coerenza per ogni punto di ripristino.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

L'attributo **Name** nell'output corrisponde al nome del punto di ripristino che può essere usato come valore per il parametro **--RP-Name** nelle operazioni di ripristino.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Ripristino con condivisione completa usando l'interfaccia della riga di comando di Azure

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.

Definire i parametri seguenti per eseguire le operazioni di ripristino:

* **--Container-Name**: nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name**: nome della condivisione file originale di cui è stato eseguito il backup che si desidera utilizzare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-a-full-share-to-the-original-location"></a>Ripristinare una condivisione completa nel percorso originale

Quando si esegue il ripristino in un percorso originale, non è necessario specificare i parametri relativi alla destinazione. È necessario fornire solo **conflitti di risoluzione** .

Nell'esempio seguente viene usato il cmdlet [AZ backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità di ripristino impostata su *originallocation* per ripristinare la condivisione file *risorsa* nel percorso originale. Usare il punto di ripristino 932883129628959823, ottenuto in [recuperare i punti di ripristino per la condivisione file di Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-a-full-share-to-an-alternate-location"></a>Ripristinare una condivisione completa in un percorso alternativo

È possibile usare questa opzione per ripristinare una condivisione file in un percorso alternativo e mantengono la condivisione file originale così come è. Specificare i seguenti parametri per il ripristino del percorso alternativo:

* **--target-storage-account**: l'account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share**: la condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **--Resolve-conflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

Nell'esempio seguente viene usato il comando [AZ backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità di ripristino come *AlternateLocation* per ripristinare la condivisione file *risorsa* nell'account di archiviazione *afsaccount* nella condivisione file *azurefiles1* nell'account di archiviazione *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

Definire i parametri seguenti per eseguire le operazioni di ripristino:

* **--Container-Name**: nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name**: nome della condivisione file originale di cui è stato eseguito il backup che si desidera utilizzare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Specificare i seguenti parametri per gli elementi che si desidera ripristinare:

* **Percorsofileorigine**: percorso assoluto del file, da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è lo stesso percorso usato nei comandi [AZ Storage file download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) o [AZ Storage file Show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) cli.
* **SourceFileType**: scegliere se selezionare una directory o un file. Accetta la **directory** o il **file**.
* **ResolveConflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Ripristinare singoli file o cartelle nel percorso originale

Usare il cmdlet [AZ backup restore-risorsa](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata su *originallocation* per ripristinare i file o le cartelle specifici nel percorso originale.

Nell'esempio seguente viene ripristinato il file *RestoreTest. txt* nel percorso originale, ovvero la condivisione file *risorsa* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Ripristinare singoli file o cartelle in un percorso alternativo

Per ripristinare file o cartelle specifiche in un percorso alternativo, usare il cmdlet [AZ backup restore-risorsa](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata su *AlternateLocation* e specificare i parametri relativi alla destinazione seguenti:

* **--target-storage-account**: l'account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share**: la condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto di cui è stato eseguito il backup deve essere ripristinato in una cartella radice, assegnare il valore della cartella di destinazione come stringa vuota.

Nell'esempio seguente viene ripristinato il file *RestoreTest. txt* originariamente presente nella condivisione file *risorsa* in un percorso alternativo, ovvero la cartella *RestoreData* nella condivisione file *azurefiles1* ospitata nell'account di archiviazione *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Ripristinare più file o cartelle nel percorso originale o in un percorso alternativo

Per eseguire il ripristino per più elementi, passare il valore per il parametro del **percorso del file di origine** come percorsi separati da **spazi** di tutti i file o le cartelle che si desidera ripristinare.

Nell'esempio seguente vengono ripristinati i file di *report. docx* *Restore. txt* e AFS che si verificano nel percorso originale.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

L'output sarà simile al seguente:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

Se si desidera ripristinare più elementi in un percorso alternativo, utilizzare il comando precedente specificando parametri correlati alla destinazione, come illustrato nella sezione [ripristinare singoli file o cartelle in un percorso alternativo](#restore-individual-files-or-folders-to-an-alternate-location) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire i backup di condivisioni file di Azure con l'interfaccia della riga](manage-afs-backup-cli.md)di comando di Azure.
