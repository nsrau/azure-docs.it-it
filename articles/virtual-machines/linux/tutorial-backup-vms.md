---
title: Eseguire il backup di macchine virtuali Linux in Azure | Microsoft Docs
description: Proteggere le macchine virtuali Linux eseguendo il backup con Backup di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: d0cbf7883a8737bcb10e9dd251c9792a12993f77
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="back-up-linux--virtual-machines-in-azure"></a>Eseguire il backup di macchine virtuali Linux in Azure

È possibile proteggere i dati eseguendo backup a intervalli regolari. Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si ripristina da un punto di recupero, è possibile ripristinare la macchina virtuale intera o parziale. Questo articolo spiega come ripristinare un singolo file in una VM Linux che esegue nginx. Se non si dispone già di una macchina virtuale da usare, è possibile crearne una usando la [Guida introduttiva Linux](quick-create-cli.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un backup di una macchina virtuale
> * Pianificare un backup giornaliero
> * Ripristinare un file da un backup



## <a name="backup-overview"></a>Panoramica del servizio Backup

Quando il servizio Backup di Azure avvia un processo di backup, attiva l'estensione per il backup per acquisire uno snapshot temporizzato. Il servizio Backup di Azure usa l'estensione _VMSnapshotLinux_ in Linux. L'estensione viene installata durante il primo backup della macchina virtuale se la macchina virtuale è in esecuzione. Se la macchina virtuale non è in esecuzione, il servizio Backup crea uno snapshot dell'archivio sottostante (poiché non si verifica alcuna scrittura di applicazione durante l'arresto della macchina virtuale).

Per impostazione predefinita, Backup di Azure esegue un backup coerente del file system per macchine virtuali Linux ma può essere configurato per eseguire un [backup coerente dell'applicazione tramite un framework di script pre e post-backup](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). Dopo che il servizio Backup di Azure crea lo snapshot, i data vengono trasferiti nell'insieme di credenziali. Per offrire la massima efficienza, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.


## <a name="create-a-backup"></a>Creare un backup
Creare un semplice backup giornaliero pianificato per un insieme di credenziali di Servizi di ripristino. 

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra selezionare **Macchine virtuali**. 
3. Dall'elenco selezionare la macchina virtuale di cui eseguire il backup.
4. Nel pannello della macchina virtuale, nella sezione **Impostazioni** fare clic su **Backup**. Verrà aperto il pannello **Abilita backup**.
5. In **Insieme di credenziali dei servizi di ripristino** fare clic su **Crea nuovo** e inserire un nome per il nuovo insieme di credenziali. Viene creato un nuovo insieme di credenziali nello stesso gruppo di risorse e nello stesso percorso della macchina virtuale.
6. Fare clic su **Criterio di Backup**. Per questo esempio, mantenere le impostazioni predefinite e fare clic su **OK**.
7. Nel pannello **Abilita backup** fare clic su **Abilita backup**. Verrà creato un backup giornaliero in base alla pianificazione predefinita.
10. Per creare un punto di recupero iniziale, nel pannello **Backup** fare clic su **Esegui backup ora**.
11. Nel pannello **Esegui backup ora** fare clic sull'icona del calendario, usare il comando del calendario per selezionare l'ultimo giorno di conservazione di tale punto di recupero e fare clic su **Backup**.
12. Nel pannello **Backup** per la macchina virtuale in uso verrà visualizzato il numero di punti di recupero completati.

    ![Punti di ripristino](./media/tutorial-backup-vms/backup-complete.png)

Il primo backup richiede circa 20 minuti. Al termine del backup, procedere con la parte successiva di questa esercitazione.

## <a name="restore-a-file"></a>Ripristinare un file

Se accidentalmente si elimina o si apportano modifiche a un file, è possibile usare Ripristino file per ripristinare il file dall'insieme di credenziali di backup. Ripristino file usa uno script eseguito nella macchina virtuale, per montare il punto di recupero come unità locale. Queste unità rimarranno montate per 12 ore in modo che sia possibile copiare i file dal punto di recupero e ripristinarli nella macchina virtuale.  

Questo esempio illustra come ripristinare la pagina Web di nginx predefinita /var/www/html/index.nginx-debian.html. L'indirizzo IP pubblico della macchina virtuale in questo esempio è *13.69.75.209*. È possibile trovare l'indirizzo IP della macchina virtuale tramite:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Nel computer locale aprire un browser e digitare l'indirizzo IP pubblico della macchina virtuale per visualizzare la pagina Web di nginx predefinita.

    ![Pagina Web di nginx predefinita](./media/tutorial-backup-vms/nginx-working.png)

1. Stabilire una connessione SSH alla VM.

    ```bash
    ssh 13.69.75.209
    ```
2. Eliminare /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Nel computer locale aggiornare il browser premendo CTRL + F5 per verificare che la pagina di nginx predefinita non è più presente.

    ![Pagina Web di nginx predefinita](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Nel computer locale accedere al [portale di Azure](https://portal.azure.com/).
6. Nel menu a sinistra selezionare **Macchine virtuali**. 
7. Selezionare la macchina virtuale dall'elenco.
8. Nel pannello della macchina virtuale, nella sezione **Impostazioni** fare clic su **Backup**. Verrà visualizzato il pannello **Backup**. 
9. Dal menu nella parte superiore del pannello scegliere **Ripristino file**. Verrà aperto il pannello **Ripristino file**.
10. In **Passaggio 1: Selezionare il punto di recupero** selezionare un punto di recupero dall'elenco a discesa.
11. In **Passaggio 2: Scaricare lo script per cercare e ripristinare i file** fare clic sul pulsante **Scarica eseguibile**. Salvare il file scaricato nel computer locale in uso.
7. Fare clic su **Scarica script** per scaricare il file di script in locale.
8. Aprire un prompt Bash e digitare quanto segue, sostituendo *Linux_myVM_05-05-2017.sh* con il percorso corretto e il nome file per lo script che è stato scaricato, *azureuser* con il nome utente per la macchina virtuale e *13.69.75.209* con l'indirizzo IP pubblico per la macchina virtuale.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Nel computer locale aprire una connessione SSH alla macchina virtuale.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Nella macchina virtuale aggiungere le autorizzazioni di esecuzione per il file di script.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Nella macchina virtuale eseguire lo script per montare il punto di recupero come file system.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. L'output dello script fornisce il percorso per il punto di montaggio. L'output è simile al seguente:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Nella macchina virtuale copiare la pagina Web predefinita di nginx dal punto di montaggio in cui è stato eliminato il file.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Nel computer locale aprire la scheda del browser in cui si è connessi all'indirizzo IP della macchina virtuale che mostra la pagina predefinita di nginx. Premere CTRL + F5 per aggiornare la pagina del browser. Si noterà ora che la pagina predefinita funziona di nuovo.

    ![Pagina Web di nginx predefinita](./media/tutorial-backup-vms/nginx-working.png)

18. Nel computer locale tornare alla scheda del browser per il portale di Azure e in **Passaggio 3: Smontare i dischi dopo il ripristino** fare clic sul pulsante **Smontare i dischi**. Se si dimentica di eseguire questo passaggio, la connessione per il punto di montaggio viene chiusa automaticamente dopo 12 ore. Trascorse le 12 ore, è necessario scaricare un nuovo script per creare un nuovo punto di montaggio.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un backup di una macchina virtuale
> * Pianificare un backup giornaliero
> * Ripristinare un file da un backup

Passare all'esercitazione successiva per informazioni sul monitoraggio di macchine virtuali.

> [!div class="nextstepaction"]
> [Monitorare le macchine virtuali](tutorial-monitoring.md)


