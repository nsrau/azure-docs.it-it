---
title: Ripristinare il disco di una macchina virtuale con Backup di Azure | Microsoft Docs
description: Informazioni su come ripristinare un disco e creare un ripristino di una macchina virtuale in Azure con i servizi di backup e ripristino.
services: virtual-machines, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines, azure-backup
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a704d3f9e37951e38fb395612e001501f322d754
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Ripristinare un disco e creare una macchina virtuale ripristinata in Azure
Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si esegue il ripristino da un punto di ripristino, è possibile ripristinare l'intera macchina virtuale o file singoli. Questo articolo descrive come ripristinare un'intera macchina virtuale. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare e selezionare i punti di ripristino
> * Ripristinare un disco da un punto di ripristino
> * Creare una macchina virtuale dal disco ripristinato

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.18 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Prerequisiti
Per questa esercitazione è necessaria una macchina virtuale Linux protetta con Backup di Azure. Per simulare un'eliminazione accidentale della macchina virtuale e il processo di recupero, viene creata una macchina virtuale da un disco in un punto di ripristino. Se si necessita di una macchina virtuale Linux protetta con Backup di Azure, vedere [Eseguire il backup di una macchina virtuale in Azure con l'interfaccia della riga di comando](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Panoramica del servizio Backup
Quando Azure avvia un backup, l'estensione di backup nella macchina virtuale acquisisce uno snapshot temporizzato. L'estensione di backup viene installata nella macchina virtuale quando viene richiesto il primo backup. Backup di Azure può anche acquisire uno snapshot dell'archiviazione sottostante se la macchina virtuale non è in esecuzione quando viene eseguito il backup.

Per impostazione predefinita, Backup di Azure esegue un backup coerente con il file system. Dopo che Backup di Azure ha acquisito lo snapshot, i dati vengono trasferiti nell'insieme di credenziali dei servizi di ripristino. Per offrire la massima efficienza, Backup di Azure identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.


## <a name="list-available-recovery-points"></a>Visualizzare i punti di ripristino disponibili
Per ripristinare un disco, selezionare un punto di ripristino come origine dei dati di ripristino. Poiché i criteri predefiniti creano un punto di ripristino ogni giorno che viene conservato per 30 giorni, è possibile avere a disposizione un insieme di punti di ripristino che consente di selezionare un momento specifico per il ripristino. 

Per visualizzare un elenco dei punti di ripristino disponibili, usare **az backup recoverypoint list**. Il valore **name** del punto di ripristino viene usato per il ripristino dei dischi. In questa esercitazione si vuole usare il punto di ripristino più recente disponibile. Il parametro `--query [0].name` seleziona il nome del punto di ripristino più recente come illustrato di seguito:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Ripristinare il disco di una macchina virtuale
Per ripristinare il disco dal punto di ripristino, è innanzitutto necessario creare un account di archiviazione di Azure. L'account di archiviazione viene usato per archiviare il disco ripristinato. Nei passaggi aggiuntivi il disco ripristinato viene usato per creare una macchina virtuale.

1. Per creare un account di archiviazione, usare [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). Il nome dell'account di archiviazione deve contenere solo caratteri minuscoli ed essere univoco globalmente. Sostituire *mystorageaccount* con il nome univoco:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Ripristinare il disco dal punto di ripristino con **az backup restore restore-disks**. Sostituire *mystorageaccount* con il nome dell'account di archiviazione creato con il comando precedente. Sostituire *myRecoveryPointName* con il nome del punto di ripristino ottenuto nell'output del comando **az backup recoverypoint list** precedente:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Monitorare il processo di ripristino
Per monitorare lo stato del processo di ripristino, usare **az backup job list**:

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

L'output è simile all'esempio seguente che mostra che il processo di ripristino è *In corso*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando lo *Stato* del processo di ripristino indica *Completato*, il disco è stato ripristinato nell'account di archiviazione.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Convertire il disco ripristinato in un disco gestito
Il processo di ripristino crea un disco non gestito. Per creare una macchina virtuale dal disco, è necessario innanzitutto convertire il disco in un disco gestito.

1. Ottenere le informazioni di connessione per l'account di archiviazione con [az storage account show-connection-string](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Sostituire *mystorageaccount* con il nome dell'account di archiviazione come illustrato di seguito:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Il disco non gestito è protetto nell'account di archiviazione. I comandi seguenti ottengono le informazioni sul disco non gestito e creano una variabile denominata *uri* usata nel passaggio successivo in cui viene creato il disco gestito.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. È ora possibile creare un disco gestito dal disco ripristinato con [az disk create](/cli/azure/disk?view=azure-cli-latest#az_disk_create). La variabile *uri* del passaggio precedente viene usata come origine del disco gestito.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Dopo aver creato un disco gestito dal disco ripristinato, cancellare il disco non gestito e l'account di archiviazione con [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Sostituire *mystorageaccount* con il nome dell'account di archiviazione come illustrato di seguito:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Creare una macchina virtuale dal disco ripristinato
Il passaggio finale consiste nel creare una macchina virtuale dal disco gestito.

1. Creare una macchina virtuale dal disco gestito con [az vm create](/cli/azure/vm?view=azure-cli-latest#az_vm_create) come illustrato di seguito:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Per verificare che la macchina virtuale sia stata creata dal disco ripristinato, visualizzare l'elenco delle macchine virtuali nel gruppo di risorse con [az vm list](/cli/azure/vm?view=azure-cli-latest#az_vm_list) come illustrato di seguito:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato ripristinato un disco da un punto di ripristino ed è stata quindi creata una macchina virtuale dal disco. Si è appreso come:

> [!div class="checklist"]
> * Visualizzare e selezionare i punti di ripristino
> * Ripristinare un disco da un punto di ripristino
> * Creare una macchina virtuale dal disco ripristinato

Passare all'esercitazione successiva per apprendere come ripristinare singoli file da un punto di ripristino.

> [!div class="nextstepaction"]
> [Ripristinare file in una macchina virtuale in Azure](tutorial-restore-files.md)


