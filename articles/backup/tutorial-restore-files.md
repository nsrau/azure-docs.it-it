---
title: Ripristinare file in una macchina virtuale con Backup di Azure | Microsoft Docs
description: Informazioni su come eseguire un ripristino a livello di file su una macchina virtuale di Azure con servizi di Backup e ripristino.
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e1bbae56b70c50fcf691db47efd9dc587686e7da
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Ripristinare file in una macchina virtuale in Azure
Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si esegue il ripristino da un punto di recupero, è possibile ripristinare l'intera macchina virtuale o file singoli. In questo articolo viene illustrato come ripristinare singoli file. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare e selezionare i punti di ripristino
> * Connettere un punto di recupero a una macchina virtuale
> * Ripristinare file da un punto di recupero

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.18 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>prerequisiti
Per questa esercitazione è necessaria una macchina virtuale Linux protetta con Backup di Azure. Per simulare un processo di eliminazione accidentale e ripristino di un file, si elimina una pagina da un server Web. Se si necessita di una macchina virtuale Linux che esegue un server Web ed è protetta con Backup di Azure, vedere [Eseguire il backup di una macchina virtuale in Azure con l'interfaccia della riga di comando](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Panoramica del servizio Backup
Quando Azure avvia un backup, l'estensione di backup nella macchina virtuale acquisisce uno snapshot temporizzato. L'estensione di backup viene installata nella macchina virtuale quando viene richiesto il primo backup. Backup di Azure può anche acquisire uno snapshot dell'archiviazione sottostante se la macchina virtuale non è in esecuzione quando viene eseguito il backup.

Per impostazione predefinita, Backup di Azure esegue un backup coerente con il file system. Dopo che Backup di Azure ha acquisito lo snapshot, i dati vengono trasferiti nell'insieme di credenziali dei servizi di ripristino. Per offrire la massima efficienza, Backup di Azure identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.


## <a name="delete-a-file-from-a-vm"></a>Eliminare un file da una macchina virtuale
Se si elimina accidentalmente o si apportano modifiche a un file, è possibile ripristinare singoli file da un punto di recupero. Questo processo consente di cercare i file di backup in un punto di recupero e ripristinare solo i file necessari. In questo esempio si elimina un file da un server Web per illustrare il processo di ripristino a livello di file.

1. Per connettere la macchina virtuale, è possibile ottenerne l'indirizzo IP con [az vm show](/cli/azure/vm?view=azure-cli-latest#az_vm_show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Per verificare che il sito Web al momento funzioni, aprire un Web browser sull'indirizzo IP pubblico della macchina virtuale. Lasciare aperta la finestra del Web browser.

    ![Pagina Web di nginx predefinita](./media/tutorial-restore-files/nginx-working.png)

3. Connettersi alla macchina virtuale con SSH. Sostituire *publicIpAddress* con l'indirizzo IP pubblico ottenuto con un comando precedente:

    ```bash
    ssh publicIpAddress
    ```

4. Eliminare la pagina predefinita del server Web in */var/www/html/index.nginx-debian.html*, come indicato di seguito:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Nel Web browser, aggiornare la pagina Web. Il sito Web non carica più la pagina, come illustrato nell'esempio seguente:

    ![Il sito Web Nginx non carica più la pagina predefinita](./media/tutorial-restore-files/nginx-broken.png)

6. Chiudere la sessione SSH per la macchina virtuale come indicato di seguito:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Generare lo script di ripristino dei file
Per ripristinare i file, Backup di Azure fornisce uno script da eseguire nella macchina virtuale che si connette al punto di recupero come un'unità locale. È possibile individuare l'unità locale, ripristinare i file nella macchina virtuale stessa, quindi disconnettere il punto di recupero. Backup di Azure continua a eseguire il backup in base al criterio assegnato per la pianificazione e la conservazione dei dati.

1. Per elencare i punti di ripristino per la macchina virtuale, usare l'[elenco az backup recoverypoint](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). In questo esempio verrà seleziona il punto di recupero più recente per la macchina virtuale denominata *myVM* protetta in *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Per ottenere lo script che si connette a, o contiene, il punto di recupero per la macchina virtuale, usare l'opzione [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp). Nell'esempio seguente si ottiene lo script per la macchina virtuale denominata *myVM* protetta in *myRecoveryServicesVault*.

    Sostituire *myRecoveryPointName* con il nome del punto di recupero ottenuto nel comando precedente:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Lo script viene scaricato e viene visualizzata una password, come nell'esempio seguente:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Per trasferire lo script nella macchina virtuale, usare Secure Copy (SCP). Specificare il nome dello script scaricato e sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale. Assicurarsi di includere il carattere finale `:` alla fine del comando SCP, come segue:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Ripristinare i file nella macchina virtuale
Con lo script di recupero copiato nella macchina virtuale è possibile collegare il punto di recupero e ripristinare i file.

1. Connettersi alla macchina virtuale con SSH. Sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale, come segue:

    ```bash
    ssh publicIpAddress
    ```

2. Per consentire la corretta esecuzione dello script, aggiungere delle autorizzazioni di esecuzione con **chmod**. Immettere il nome del proprio script:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Per installare il punto di recupero, eseguire lo script. Immettere il nome del proprio script:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Durante l'esecuzione dello script viene chiesto di immettere una password per accedere al punto di recupero. Immettere la password illustrata nell'output dal precedente comando [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp), che ha generato lo script di ripristino.

    L'output dello script fornisce il percorso per il punto di recupero. L'output di esempio seguente mostra che il punto di recupero è montato su */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Usare **cp** per copiare la pagina Web predefinita NGINX dal punto di recupero montato nuovamente nel percorso del file originale. Sostituire il punto di montaggio */home/azureuser/myVM-20170919213536/Volume1* con il proprio percorso:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. Nel Web browser, aggiornare la pagina Web. Il sito Web carica adesso correttamente la pagina, come illustrato nell'esempio seguente:

    ![Il sito web NGINX viene ora caricato correttamente](./media/tutorial-restore-files/nginx-restored.png)

7. Chiudere la sessione SSH per la macchina virtuale come indicato di seguito:

    ```bash
    exit
    ```

8. Smontare il punto di recupero dalla macchina virtuale tramite il comando [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp). Nell'esempio seguente viene smontato il punto di recupero dalla macchina virtuale denominata *myVM* in *myRecoveryServicesVault*.

    Sostituire *myRecoveryPointName* con il nome del punto di recupero ottenuto con i comandi precedenti:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti la connessione di un punto di recupero con una macchina virtuale e il ripristino dei file per un server Web. Si è appreso come:

> [!div class="checklist"]
> * Visualizzare e selezionare i punti di ripristino
> * Connettere un punto di recupero a una macchina virtuale
> * Ripristinare file da un punto di recupero

Passare all'esercitazione successiva per informazioni su come eseguire il backup di Windows Server in Azure.

> [!div class="nextstepaction"]
> [Eseguire il backup di Windows Server in Azure](tutorial-backup-windows-server-to-azure.md)

