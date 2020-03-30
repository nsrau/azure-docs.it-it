---
title: Eseguire il backup delle condivisioni file di Azure con l'interfaccia della riga di comando di AzureBack up Azure file shares with Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali dei servizi di ripristino
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844042"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Eseguire il backup delle condivisioni file di Azure con l'interfaccia della riga di comandoBack up Azure file shares with

L'interfaccia della riga di comando (CLI) di Azure offre un'esperienza della riga di comando per la gestione delle risorse di Azure.The Azure command-line interface (CLI) provides a command-line experience for managing Azure resources. È un ottimo strumento per la creazione di automazione personalizzata per usare le risorse di Azure.It's a great tool for building custom automation to use Azure resources. Questo articolo descrive in dettaglio come eseguire il backup delle condivisioni file di Azure con l'interfaccia della riga di comando di Azure.This article details how to back up Azure file shares with Azure CLI. È anche possibile eseguire questa procedura con [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) o nel [portale di Azure](backup-afs.md).

Alla fine di questa esercitazione si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:By the end of this tutorial, you will learn how to perform below operations with Azure CLI:

* Creare un insieme di credenziali dei servizi di ripristino
* Abilitare il backup per le condivisioni file di AzureEnable backup for Azure file shares
* Attivare un backup su richiesta per le condivisioni fileTrigger an on-demand backup for file shares

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per trovare la `run az --version`versione dell'interfaccia della riga di comando, . Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Creazione di un archivio dei servizi di ripristino

Un insieme di credenziali del servizio di ripristino è un'entità che offre una funzionalità di visualizzazione e gestione consolidata per tutti gli elementi di backup. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Per creare un insieme di credenziali dei servizi di ripristino, attenersi alla seguente procedura:

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . In questa esercitazione vengono creati i nuovi *file di azure* del gruppo di risorse nell'area Stati Uniti orientali.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Utilizzare il cmdlet [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    Nell'esempio seguente viene creato un insieme di credenziali dei servizi di ripristino denominato *azurefilesvault* nell'area Stati Uniti orientali.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Specificare il tipo di ridondanza da utilizzare per l'archiviazione dell'insieme di credenziali. È possibile usare l'[archiviazione con ridondanza locale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) o l'[archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    L'esempio seguente imposta l'opzione di ridondanza *dell'archiviazione per azurefilesvault* su **Georedundant** usando il cmdlet [set di proprietà del vault di backup az backup.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Per verificare se l'insieme di credenziali è stato creato correttamente, è possibile utilizzare il cmdlet [az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) per ottenere i dettagli dell'insieme di credenziali. Nell'esempio seguente vengono visualizzati i dettagli del *file azurefilesvault* creato nei passaggi precedenti.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    L'output sarà simile alla seguente risposta:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Abilitare il backup per le condivisioni file di AzureEnable backup for Azure file shares

In questa sezione si presuppone che si disponga già di una condivisione file di Azure per la quale si vuole configurare il backup. Se non si dispone di una condivisione file di Azure usando il comando [az storage share create.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Per abilitare il backup per le condivisioni file, è necessario creare un criterio di protezione che definisca quando viene eseguito un processo di backup e per quanto tempo vengono archiviati i punti di ripristino. È possibile creare criteri di backup utilizzando il cmdlet [az backup policy create.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)

L'esempio seguente usa il cmdlet az backup protection enable-for-azurefileshare per abilitare il backup per la condivisione file azurefiles nell'account di archiviazione afsaccount usando i criteri di backup *della pianificazione 1:The* following example uses the az [backup protection enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet to enable for *azurefilesshare* cmdlet to enable for azurefiles file share cmdlet to enable backup for the azurefiles file share in the *afsaccount* storage account using the schedule 1 backup policy:

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di **abilitazione del backup.** Per tenere traccia dello stato del processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Attivare un backup su richiesta per la condivisione fileTrigger an on-demand backup for file share

Se si desidera attivare un backup su richiesta per la condivisione file anziché attendere che i criteri di backup esedano il processo all'ora pianificata, utilizzare il cmdlet [az backup protection backup-now.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

È necessario definire i seguenti parametri per attivare un backup su richiesta:

* **--container-name** è il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, utilizzare il comando [az backup container list.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name** è il nome della condivisione file per la quale si desidera attivare un backup su richiesta. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, utilizzare il comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--retain-until** specifica la data fino alla quale si desidera mantenere il punto di ripristino. Il valore deve essere impostato nel formato UTC (dd-mm-yyyy).

Nell'esempio seguente viene attivato un backup su richiesta per la condivisione file *azuresfiles* nell'account di archiviazione *afsaccount* con conservazione fino al *20-01-2020.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di "backup su richiesta". Per tenere traccia dello stato di un processo, utilizzare il cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come ripristinare le condivisioni file di [Azure con l'interfaccia della riga di comandoLearn](restore-afs-cli.md) how to Restore Azure file shares with CLI
* Informazioni su come gestire gli utenti della [condivisione file](manage-afs-backup-cli.md) di Azure con l'interfaccia della riga di comando
