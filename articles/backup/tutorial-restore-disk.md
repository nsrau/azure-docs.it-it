---
title: 'Esercitazione: Ripristinare il disco di una macchina virtuale con Backup di Azure'
description: Informazioni su come ripristinare un disco e creare un ripristino di una macchina virtuale in Azure con i servizi di backup e ripristino.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114195"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Ripristinare un disco e creare una macchina virtuale ripristinata in Azure

Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si esegue il ripristino da un punto di recupero, è possibile ripristinare l'intera macchina virtuale o file singoli. Questo articolo descrive come ripristinare un'intera macchina virtuale usando l'interfaccia della riga di comando. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Visualizzare e selezionare i punti di ripristino
> * Ripristinare un disco da un punto di ripristino
> * Creare una macchina virtuale dal disco ripristinato

Per informazioni sull'uso di PowerShell per ripristinare un disco e creare una macchina virtuale ripristinata, vedere [Eseguire il back e il ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.18 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisites

Per questa esercitazione è necessaria una macchina virtuale Linux protetta con Backup di Azure. Per simulare un'eliminazione accidentale della macchina virtuale e il processo di recupero, viene creata una macchina virtuale da un disco in un punto di ripristino. Se si necessita di una macchina virtuale Linux protetta con Backup di Azure, vedere [Eseguire il backup di una macchina virtuale in Azure con l'interfaccia della riga di comando](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Panoramica del servizio Backup

Quando Azure avvia un backup, l'estensione di backup nella macchina virtuale acquisisce uno snapshot temporizzato. L'estensione di backup viene installata nella macchina virtuale quando viene richiesto il primo backup. Backup di Azure può anche acquisire uno snapshot dell'archiviazione sottostante se la macchina virtuale non è in esecuzione quando viene eseguito il backup.

Per impostazione predefinita, Backup di Azure esegue un backup coerente con il file system. Dopo che Backup di Azure ha acquisito lo snapshot, i dati vengono trasferiti nell'insieme di credenziali dei servizi di ripristino. Per offrire la massima efficienza, Backup di Azure identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

## <a name="list-available-recovery-points"></a>Visualizzare i punti di ripristino disponibili

Per ripristinare un disco, selezionare un punto di ripristino come origine dei dati di ripristino. Poiché i criteri predefiniti creano un punto di ripristino ogni giorno che viene conservato per 30 giorni, è possibile avere a disposizione un insieme di punti di ripristino che consente di selezionare un momento specifico per il ripristino.

Per visualizzare un elenco dei punti di ripristino disponibili, usare [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). Il valore **name** del punto di ripristino viene usato per il ripristino dei dischi. In questa esercitazione si vuole usare il punto di ripristino più recente disponibile. Il parametro `--query [0].name` seleziona il nome del punto di ripristino più recente come illustrato di seguito:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Ripristinare il disco di una macchina virtuale

> [!IMPORTANT]
> Si consiglia vivamente di usare l'interfaccia della riga di comando di Azure 2.0.74 o versioni successive per sfruttare tutti i vantaggi di un ripristino rapido, incluso il ripristino dei dischi gestiti. È preferibile che l'utente usi sempre la versione più recente.

### <a name="managed-disk-restore"></a>Ripristino di dischi gestiti

Se nella macchina virtuale di cui si esegue il backup sono presenti dischi gestiti e se lo scopo è quello di ripristinare i dischi gestiti dal punto di ripristino, è prima necessario fornire un account di archiviazione di Azure. Questo account di archiviazione viene usato per archiviare la configurazione della macchina virtuale e il modello di distribuzione che può essere usato in seguito per distribuire la macchina virtuale dai dischi ripristinati. È anche necessario specificare un gruppo di risorse di destinazione in cui verranno ripristinati i dischi gestiti.

1. Per creare un account di archiviazione, usare [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Il nome dell'account di archiviazione deve contenere solo caratteri minuscoli ed essere univoco globalmente. Sostituire *mystorageaccount* con il nome univoco:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Ripristinare il disco dal punto di ripristino con [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Sostituire *mystorageaccount* con il nome dell'account di archiviazione creato con il comando precedente. Sostituire *myRecoveryPointName* con il nome del punto di ripristino ottenuto nell'output del comando [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) precedente. ***Specificare anche il gruppo di risorse in cui vengono ripristinati i dischi gestiti***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Se il gruppo di risorse di destinazione non è indicato, i dischi gestiti verranno ripristinati come dischi non gestiti nell'account di archiviazione specificato. Le conseguenze per il tempo di ripristino saranno significative, dal momento che il tempo impiegato per ripristinare i dischi dipende esclusivamente dall'account di archiviazione specificato.

### <a name="unmanaged-disks-restore"></a>Ripristino di dischi non gestiti

Se nella macchina virtuale di cui si esegue il backup sono presenti dischi non gestiti e se lo scopo è quello di ripristinare i dischi dal punto di ripristino, è prima necessario fornire un account di archiviazione di Azure. Questo account di archiviazione viene usato per archiviare la configurazione della macchina virtuale e il modello di distribuzione che può essere usato in seguito per distribuire la macchina virtuale dai dischi ripristinati. Per impostazione predefinita, i dischi non gestiti verranno ripristinati nei relativi account di archiviazione originali. Se l'utente vuole ripristinare tutti i dischi non gestiti in un'unica posizione, è possibile usare l'account di archiviazione specificato come posizione di gestione temporanea per tali dischi.

Nei passaggi aggiuntivi il disco ripristinato viene usato per creare una macchina virtuale.

1. Per creare un account di archiviazione, usare [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Il nome dell'account di archiviazione deve contenere solo caratteri minuscoli ed essere univoco globalmente. Sostituire *mystorageaccount* con il nome univoco:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Ripristinare il disco dal punto di ripristino con [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Sostituire *mystorageaccount* con il nome dell'account di archiviazione creato con il comando precedente. Sostituire *myRecoveryPointName* con il nome del punto di ripristino ottenuto nell'output del comando [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) precedente:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Come indicato in precedenza, i dischi non gestiti verranno ripristinati nei relativi account di archiviazione originali. Questo consente di ottenere le migliori prestazioni di ripristino. Tuttavia, se tutti i dischi non gestiti devono essere ripristinati nell'account di archiviazione specificato, usare il flag pertinente come illustrato di seguito.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

L'output è simile all'esempio seguente che mostra che il processo di ripristino è *In corso*:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando lo *Stato* del processo di ripristino segnala *Completato*, le informazioni necessarie (configurazione della macchina virtuale e modello di distribuzione) sono state ripristinate nell'account di archiviazione.

## <a name="create-a-vm-from-the-restored-disk"></a>Creare una macchina virtuale dal disco ripristinato

Il passaggio finale consiste nel creare una macchina virtuale dai dischi ripristinati. Per creare la macchina virtuale, è possibile usare il modello di distribuzione scaricato nell'account di archiviazione specificato.

### <a name="fetch-the-job-details"></a>Recuperare i dettagli del processo

I dettagli del processo risultante includono l'URI del modello che può essere sottoposto a query e distribuito. Usare il comando job show per ottenere altri dettagli per il processo ripristinato attivato.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

L'output di questa query fornirà tutti i dettagli, ma ai fini dell'esercitazione sono rilevanti solo le informazioni relative al contenuto dell'account di archiviazione. È possibile usare la [funzionalità di query](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) dell'interfaccia della riga di comando di Azure per recuperare i dettagli pertinenti

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Recuperare il modello di distribuzione

Il modello non è direttamente accessibile perché si trova nell'account di archiviazione del cliente e nel contenitore specificato. Per accedere a questo modello, è necessario l'URL completo (insieme a un token di firma di accesso condiviso temporaneo).

È prima necessario estrarre l'URI del BLOB del modello dai dettagli del processo

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

L'URI del BLOB del modello presenterà questo formato e da qui verrà estratto il nome del modello

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Quindi, il nome del modello dell'esempio precedente sarà ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` e il nome del contenitore è ```myVM-daa1931199fd4a22ae601f46d8812276```

Ottenere ora il token di firma di accesso condiviso per il contenitore e il modello come descritto [qui](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Distribuire il modello per creare la macchina virtuale

A questo punto, distribuire il modello per creare la nuova macchina virtuale, come spiegato [qui](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Per verificare che la macchina virtuale sia stata creata dal disco ripristinato, visualizzare l'elenco delle macchine virtuali nel gruppo di risorse con [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) come illustrato di seguito:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato ripristinato un disco da un punto di ripristino ed è stata quindi creata una macchina virtuale dal disco. Si è appreso come:

> [!div class="checklist"]
>
> * Visualizzare e selezionare i punti di ripristino
> * Ripristinare un disco da un punto di ripristino
> * Creare una macchina virtuale dal disco ripristinato

Passare all'esercitazione successiva per apprendere come ripristinare singoli file da un punto di ripristino.

> [!div class="nextstepaction"]
> [Ripristinare file in una macchina virtuale in Azure](tutorial-restore-files.md)
