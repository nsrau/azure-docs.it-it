---
title: Gestire i backup di condivisioni file di Azure con CLI
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire e monitorare le condivisioni file di Azure supportate dal servizio backup di Azure.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294474"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Gestire i backup di condivisioni file di Azure con l'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. Si tratta di uno strumento straordinario per creare un'automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come eseguire le attività seguenti per la gestione e il monitoraggio delle condivisioni file di Azure di cui viene eseguito il backup dal [servizio backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview). È anche possibile eseguire questi passaggi con la [portale di Azure](https://portal.azure.com/).

* [Monitorare i processi](#monitor-jobs)
* [Modificare i criteri](#modify-policy)
* [Arrestare la protezione in una condivisione file](#stop-protection-on-a-file-share)
* [Riprendere la protezione in una condivisione file](#resume-protection-on-a-file-share)
* [Annullare la registrazione di un account di archiviazione](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che sia già presente una condivisione file di Azure di cui è stato eseguito il backup dal servizio [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Se non è già stato fatto, vedere [backup di condivisioni file di Azure con l'interfaccia](backup-afs-cli.md) della riga di comando per configurare il backup per le condivisioni file. Per questo articolo, verranno usate le risorse seguenti:

* **Gruppo di risorse**: *risorsa*
* **Recoveryservicesvault fino**: *azurefilesvault*
* **Account di archiviazione**: *afsaccount*
* **Condivisione file**: *risorsa*

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attivano le operazioni di backup o ripristino, il servizio di backup crea un processo per il rilevamento. Per monitorare i processi completati o attualmente in esecuzione, usare il cmdlet [AZ backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . L'interfaccia della riga di comando consente inoltre di [sospendere un processo attualmente in esecuzione](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) o [attendere il completamento di un processo](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

Nell'esempio seguente viene visualizzato lo stato dei processi di backup per l'insieme di credenziali di servizi di ripristino *azurefilesvault* :

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

## <a name="modify-policy"></a>Modifica criterio

È possibile modificare un criterio di backup per modificare la frequenza di backup o il periodo di mantenimento dati usando [AZ backup Item set-Policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Per modificare i criteri, definire i parametri seguenti:

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Name** è il nome della condivisione file per cui si desidera modificare il criterio. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** è il nome del criterio di backup che si vuole impostare per la condivisione file. È possibile usare il comando [AZ backup policy list](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) per visualizzare tutti i criteri per l'insieme di credenziali.

Nell'esempio seguente vengono impostati i criteri di backup di *schedule2* per la condivisione file *risorsa* presente nell'account di archiviazione *afsaccount* .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

È anche possibile eseguire il comando precedente usando i "nomi descrittivi" per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di modifica dei criteri. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="stop-protection-on-a-file-share"></a>Arrestare la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare* tutti i punti di ripristino
* Arrestare tutti i processi di backup futuri, *lasciando* i punti di ripristino

È possibile che vi sia un costo associato a lasciare i punti di ripristino nella risorsa di archiviazione, poiché verranno conservati gli snapshot sottostanti creati da backup di Azure. Tuttavia, il vantaggio di lasciare i punti di ripristino è l'opzione per ripristinare la condivisione file in un secondo momento, se necessario. Per informazioni sul costo associato al mantenimento dei punti di ripristino, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/storage/files). Se si sceglie di eliminare tutti i punti di ripristino, non sarà possibile ripristinare la condivisione file.

Per arrestare la protezione per la condivisione file, definire i parametri seguenti:

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** è il nome della condivisione file per cui si vuole arrestare la protezione. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Arrestare la protezione e mantenere i punti di ripristino

Per arrestare la protezione durante la conservazione dei dati, usare il cmdlet [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

Nell'esempio seguente viene arrestata la protezione per la condivisione file *risorsa* , ma vengono conservati tutti i punti di ripristino.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

È anche possibile eseguire il comando precedente usando il "nome descrittivo" per il contenitore e l'elemento fornendo i due parametri aggiuntivi seguenti:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di arresto della protezione dati. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="stop-protection-without-retaining-recovery-points"></a>Arrestare la protezione senza mantenere i punti di ripristino

Per arrestare la protezione senza mantenere i punti di ripristino, usare il cmdlet [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) con l' **opzione e Delete-backup-data** impostata su **true**.

Nell'esempio seguente viene arrestata la protezione per la condivisione file *risorsa* senza mantenere i punti di ripristino:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

È anche possibile eseguire il comando precedente usando il "nome descrittivo" per il contenitore e l'elemento specificando i seguenti due parametri aggiuntivi:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se è stata arrestata la protezione per una condivisione file di Azure ma sono stati conservati i punti di ripristino, è possibile riprendere la protezione in seguito. Se non si mantengono i punti di ripristino, non è possibile riprendere la protezione.

Per riprendere la protezione per la condivisione file, definire i parametri seguenti:

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** è il nome della condivisione file per cui si vuole riprendere la protezione. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** è il nome dei criteri di backup per i quali si desidera riprendere la protezione per la condivisione file.

L'esempio seguente usa il cmdlet [AZ backup Protection Resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) per riprendere la protezione per la condivisione file *risorsa* usando i criteri di backup di *schedule1* .

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

È anche possibile eseguire il comando precedente usando il "nome descrittivo" per il contenitore e l'elemento specificando i seguenti due parametri aggiuntivi:

* **--Backup-Management-Type**: *AzureStorage*
* **--tipo di carico di lavoro**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di riattivazione della protezione. Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="unregister-a-storage-account"></a>Annullare la registrazione di un account di archiviazione

Se si vuole proteggere le condivisioni file in un account di archiviazione specifico usando un insieme di credenziali di servizi di ripristino diverso, [arrestare prima di tutto la protezione per tutte le condivisioni file](#stop-protection-on-a-file-share) nell'account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali dei servizi di ripristino attualmente usato per la protezione.

È necessario specificare un nome di contenitore per annullare la registrazione dell'account di archiviazione. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .

Nell'esempio seguente viene annullata la registrazione dell'account di archiviazione *afsaccount* da *azurefilesvault* usando il cmdlet [AZ backup container Unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

È anche possibile eseguire il cmdlet precedente usando il "nome descrittivo" per il contenitore, specificando il parametro aggiuntivo seguente:

* **--Backup-Management-Type**: *AzureStorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [risolvere gli errori di backup/ripristino per le condivisioni file di Azure](troubleshoot-azure-files.md)
