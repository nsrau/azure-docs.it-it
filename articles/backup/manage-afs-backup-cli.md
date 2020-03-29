---
title: Gestire i backup della condivisione file di Azure con l'interfaccia della riga di comando di AzureManage Azure file share backups with the Azure CLI
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire e monitorare le condivisioni file di Azure di cui è stato eseguito il backup da Backup di Azure.Learn how to use the Azure CLI to manage and monitor Azure file shares backed up by Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934885"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gestire i backup della condivisione file di Azure con l'interfaccia della riga di comando di AzureManage Azure file share backups with the Azure CLI

L'interfaccia della riga di comando di Azure offre un'esperienza della riga di comando per la gestione delle risorse di Azure.The Azure CLI provides a command-line experience for managing Azure resources. È un ottimo strumento per la creazione di automazione personalizzata per usare le risorse di Azure.It's a great tool for building custom automation to use Azure resources. Questo articolo illustra come eseguire attività per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup da Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview) È anche possibile eseguire questi passaggi con il portale di Azure.You can also perform these steps with the [Azure portal](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.18 o successiva. Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che il backup di Backup di [Azure](https://docs.microsoft.com/azure/backup/backup-overview)sia già stato eseguito in un elenco di file di Azure. Se non si dispone di uno, vedere Eseguire il backup delle condivisioni file di [Azure con l'interfaccia della riga di comando](backup-afs-cli.md) per configurare il backup per le condivisioni file. Per questo articolo, si utilizzano le risorse seguenti:For this article, you use the following resources:

* **Gruppo di risorse:** *azurefilesResource* group : azurefiles
* **RecoveryServicesVault**: *azurefilesVault*
* **Account di archiviazione**: *afsaccount*
* **Condivisione file**: *file di azureFile* Share : azurefiles

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attivano operazioni di backup o ripristino, il servizio di backup crea un processo per il rilevamento. Per monitorare i processi completati o attualmente in esecuzione, utilizzare il cmdlet elenco processi di [backup az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Con l'interfaccia della riga di comando è inoltre possibile [sospendere un processo attualmente in esecuzione](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) o attendere il completamento di un [processo.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

Nell'esempio seguente viene visualizzato lo stato dei processi di backup per l'insieme di credenziali *azurefilesvault* Recovery Services:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modifica dei criteri

È possibile modificare un criterio di backup per modificare la frequenza di backup o l'intervallo di conservazione utilizzando [az backup item set-policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Per modificare il criterio, definire i seguenti parametri:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--name**: Nome della condivisione file per cui si desidera modificare il criterio. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name:** nome del criterio di backup che si desidera impostare per la condivisione file. È possibile utilizzare l'elenco dei criteri di [backup degli utenti](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) per visualizzare tutti i criteri per l'insieme di credenziali.

L'esempio seguente imposta i criteri di backup *schedule2* per la condivisione file *azurefiles* presente nell'account di archiviazione *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

È inoltre possibile eseguire il comando precedente utilizzando i nomi descrittivi per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di modifica dei criteri. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare* tutti i punti di ripristino.
* Arrestare tutti i processi di backup futuri ma *lasciare* i punti di ripristino.

Potrebbe esserci un costo associato all'abbandono dei punti di ripristino nell'archiviazione, perché gli snapshot sottostanti creati da Backup di Azure verranno mantenuti. Il vantaggio di lasciare i punti di ripristino è l'opzione per ripristinare la condivisione file in un secondo momento, se lo si desidera. Per informazioni sul costo dell'uscita dai punti di ripristino, vedere i dettagli sui [prezzi.](https://azure.microsoft.com/pricing/details/storage/files) Se si sceglie di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

Per interrompere la protezione per la condivisione file, definire i parametri seguenti:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Nome della condivisione file per cui si desidera interrompere la protezione. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Arrestare la protezione e mantenere i punti di ripristino

Per arrestare la protezione durante la conservazione dei dati, utilizzare il cmdlet [az backup protection disable.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

L'esempio seguente arresta la protezione per la condivisione file *azurefiles* ma mantiene tutti i punti di ripristino.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

È inoltre possibile eseguire il comando precedente utilizzando il nome descrittivo per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di protezione di arresto. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Arrestare la protezione senza mantenere i punti di ripristino

Per arrestare la protezione senza mantenere i punti di ripristino, utilizzare il cmdlet [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) con l'opzione **delete-backup-data** impostata su **true**.

L'esempio seguente interrompe la protezione per la condivisione file *azurefiles* senza mantenere i punti di ripristino.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

È inoltre possibile eseguire il comando precedente utilizzando il nome descrittivo per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se la protezione per una condivisione file di Azure è stata interrotta ma i punti di ripristino mantenuti, è possibile riprendere la protezione in un secondo momento. Se non si conservano i punti di ripristino, non è possibile riprendere la protezione.

Per riprendere la protezione per la condivisione file, definire i parametri seguenti:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: nome della condivisione file per cui si desidera riprendere la protezione. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: nome del criterio di backup per il quale si desidera riprendere la protezione per la condivisione file.

Nell'esempio seguente viene utilizzato il cmdlet [az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) per riprendere la protezione per la condivisione file *azurefiles* usando i criteri di backup *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

È inoltre possibile eseguire il comando precedente utilizzando il nome descrittivo per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di protezione del ripristino. Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="unregister-a-storage-account"></a>Annullare la registrazione di un account di archiviazioneUnregister a storage account

Se si desidera proteggere le condivisioni file in un account di archiviazione specifico usando un insieme di credenziali di Servizi di ripristino diverso, arrestare innanzitutto la [protezione per tutte le condivisioni file](#stop-protection-on-a-file-share) in tale account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali dei servizi di ripristino attualmente utilizzato per la protezione.

È necessario fornire un nome di contenitore per annullare la registrazione dell'account di archiviazione. Per recuperare il **nome** o il **nome descrittivo** del contenitore, utilizzare il comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

Nell'esempio seguente viene annullata la registrazione dell'account di archiviazione *afsaccount* da *azurefilesvault* usando il cmdlet [az backup container unregister.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

È inoltre possibile eseguire il cmdlet precedente utilizzando il nome descrittivo per il contenitore fornendo il seguente parametro aggiuntivo:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Risolvere i problemi di backup delle condivisioni file](troubleshoot-azure-files.md)di Azure.For more information, see Troubleshoot Azure file shares backup .
