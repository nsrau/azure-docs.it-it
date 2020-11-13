---
title: Backup di condivisioni file di Azure con l'interfaccia della riga di comando
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per eseguire il backup di condivisioni file di Azure nell'insieme di credenziali
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 34eea8daa6a0a8920c842178664055838b06a78a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565892"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Backup di condivisioni file di Azure con l'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. Si tratta di uno strumento straordinario per creare un'automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come eseguire il backup delle condivisioni file di Azure con l'interfaccia della riga di comando di Azure È anche possibile eseguire questa procedura con [Azure PowerShell](./backup-azure-afs-automation.md) o nel [portale di Azure](backup-afs.md).

Al termine di questa esercitazione si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:

* Creare un insieme di credenziali di Servizi di ripristino
* Abilitare il backup per le condivisioni file di Azure
* Attivare un backup su richiesta per le condivisioni file

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino è un'entità che offre una visualizzazione consolidata e funzionalità di gestione in tutti gli elementi di backup. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Per creare un insieme di credenziali di servizi di ripristino, seguire questa procedura:

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [AZ Group create](/cli/azure/group#az-group-create) . In questa esercitazione viene creato il nuovo gruppo di risorse *risorsa* nell'area Stati Uniti orientali.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Usare il cmdlet [AZ backup Vault create](/cli/azure/backup/vault#az-backup-vault-create) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    Nell'esempio seguente viene creato un insieme di credenziali di servizi di ripristino denominato *azurefilesvault* nell'area Stati Uniti orientali.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Abilitare il backup per le condivisioni file di Azure

Questa sezione presuppone che sia già disponibile una condivisione file di Azure per cui si vuole configurare il backup. Se non si ha un file, creare una condivisione file di Azure usando il comando [AZ storage Share create](/cli/azure/storage/share#az-storage-share-create) .

Per abilitare il backup per le condivisioni file, è necessario creare criteri di protezione che definiscono quando viene eseguito un processo di backup e la durata dell'archiviazione dei punti di ripristino. È possibile creare un criterio di backup usando il cmdlet [AZ backup policy create](/cli/azure/backup/policy#az-backup-policy-create) .

L'esempio seguente usa il cmdlet [AZ backup Protection Enable-for-azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) per abilitare il backup per la condivisione file *risorsa* nell'account di archiviazione *afsaccount* usando il criterio di backup *Schedule 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di **Abilitazione del backup** . Per tenere traccia dello stato del processo, usare il cmdlet [AZ backup job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Attivare un backup su richiesta per la condivisione file

Se si vuole attivare un backup su richiesta per la condivisione file anziché attendere che i criteri di backup eseguano il processo all'orario pianificato, usare il cmdlet [AZ backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

Per attivare un backup su richiesta, è necessario definire i parametri seguenti:

* **--Container-Name** è il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome** o il **nome descrittivo** del contenitore, usare il comando [AZ backup container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name** è il nome della condivisione file per cui si vuole attivare un backup su richiesta. Per recuperare il **nome** o il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare il comando [AZ backup Item List](/cli/azure/backup/item#az-backup-item-list) .
* **--Mantieni-fino** a quando non viene specificata la data fino a quando non si desidera mantenere il punto di ripristino. Il valore deve essere impostato in formato ora UTC (gg-mm-aaaa).

Nell'esempio seguente viene attivato un backup su richiesta per la condivisione file *risorsa* nell'account di archiviazione *afsaccount* con conservazione fino al *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

L'attributo **Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di backup su richiesta. Per tenere traccia dello stato di un processo, usare il cmdlet [AZ backup job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare condivisioni file di Azure con l'interfaccia](restore-afs-cli.md) della riga di comando
* Informazioni su come [gestire i backup di condivisione file di Azure con l'interfaccia della riga di](manage-afs-backup-cli.md) comando
