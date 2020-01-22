---
title: Ripristinare le condivisioni file di Azure con la CLI di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per ripristinare le condivisioni file di Azure di backup nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294409"
---
# <a name="restore-azure-file-shares-with-cli"></a>Ripristinare le condivisioni file di Azure con CLI

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. Si tratta di uno strumento straordinario per creare un'automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato dal servizio [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) usando l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa procedura con [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) o nel [portale di Azure](backup-afs.md).

Al termine di questa esercitazione si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:

* Visualizzare i punti di ripristino per una condivisione file di Azure di cui è stato eseguito il backup
* Ripristinare una condivisione file di Azure completa
* Ripristinare singoli file o cartelle

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per trovare la versione dell'interfaccia della riga di comando, `run az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già presente una condivisione file di Azure di cui è stato eseguito il backup dal servizio backup di Azure. Se non è già stato fatto, vedere [backup di condivisioni file di Azure con l'interfaccia](backup-afs-cli.md) della riga di comando per configurare il backup per la condivisione file. Per questo articolo verranno usate le risorse seguenti:

| Condivisione file  | Account di archiviazione | Area | Dettagli                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *risorsa*  | *afsaccount*      | EastUS | Backup di origine originale con backup di Azure                 |
| *azurefiles1* | *afaccount1*      | EastUS | Origine di destinazione utilizzata per il ripristino del percorso alternativo |

Per provare i diversi tipi di ripristini illustrati in questo documento, è possibile usare una struttura simile per le condivisioni file.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Recuperare i punti di ripristino per la condivisione file di Azure

Usare il cmdlet [AZ backup RecoveryPoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) per elencare tutti i punti di ripristino per la condivisione file di cui è stato eseguito il backup.

Nell'esempio seguente viene recuperato l'elenco dei punti di ripristino per la condivisione file *risorsa* nell'account di archiviazione *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

È anche possibile eseguire il cmdlet precedente usando il "nome descrittivo" per il contenitore e l'elemento fornendo i seguenti due parametri aggiuntivi:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Il set di risultati sarà un elenco di punti di ripristino con i dettagli relativi a tempo e coerenza per ogni punto di ripristino.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

L'attributo **Name** nell'output corrisponde al nome del punto di ripristino che può essere usato come valore per il parametro **--RP-Name** nelle operazioni di ripristino.

## <a name="full-share-recovery-using-azure-cli"></a>Ripristino con condivisione completa con l'interfaccia della riga di comando

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.

Definire i parametri seguenti per eseguire le operazioni di ripristino:

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** è il nome della condivisione file originale di cui è stato eseguito il backup che si desidera utilizzare per l'operazione di ripristino. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Ripristinare la condivisione completa nel percorso originale

Quando si esegue il ripristino in un percorso originale, non è necessario specificare i parametri relativi alla destinazione. È necessario fornire solo **conflitti di risoluzione** .

L'esempio seguente usa il comando [AZ backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità di ripristino impostata su *originallocation* per ripristinare la condivisione file *risorsa* nel percorso originale, usando il punto di ripristino 932883129628959823, ottenuto con il recupero dei [punti di ripristino per la condivisione file di Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-full-share-to-an-alternate-location"></a>Ripristinare la condivisione completa in un percorso alternativo

È possibile usare questa opzione per ripristinare una condivisione file in un percorso alternativo e mantengono la condivisione file originale così come è. Specificare i seguenti parametri per il ripristino del percorso alternativo:

* **--target-storage-account**: l'account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share**: la condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **--Resolve-conflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

L'esempio seguente usa [AZ backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) con la modalità di ripristino come *AlternateLocation* per ripristinare la condivisione file *risorsa* nell'account di archiviazione *afsaccount* in *azurefiles1 "* condivisione file nell'account di archiviazione *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
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

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** è il nome della condivisione file originale di cui è stato eseguito il backup che si vuole usare per l'operazione di ripristino. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Specificare i seguenti parametri per gli elementi che si desidera ripristinare:

* **Percorsofileorigine**: percorso assoluto del file, da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è lo stesso percorso usato nei comandi [AZ Storage file download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) o [AZ Storage file Show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) cli.
* **SourceFileType**: scegliere se selezionare una directory o un file. Accetta **Directory** o **File**.
* **ResolveConflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Ripristinare singoli file o cartelle nel percorso originale

Usare il cmdlet [AZ backup restore-risorsa](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata su *originallocation* per ripristinare i file o le cartelle specifici nel percorso originale.

Nell'esempio seguente viene ripristinato il file *"RestoreTest. txt"* nel percorso originale: *risorsa* FileShare.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Ripristinare singoli file e cartelle in un percorso alternativo

Per ripristinare cartelle e file specifici in un percorso alternativo, usare il cmdlet [AZ backup restore-risorsa](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) con la modalità di ripristino impostata su *AlternateLocation* e specificare i parametri relativi alla destinazione seguenti:

* **--target-storage-account**: l'account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share**: la condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto di cui è stato eseguito il backup deve essere ripristinato in una cartella radice, assegnare il valore della cartella di destinazione come stringa vuota.

Nell'esempio seguente viene ripristinato il file *RestoreTest. txt* originariamente presente nella condivisione file *risorsa* in un percorso alternativo, ovvero la cartella *RestoreData* nella condivisione file *azurefiles1* ospitata nell'account di archiviazione *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire i backup di condivisioni file di Azure con l'interfaccia della riga](manage-afs-backup-cli.md) di comando
