---
title: Eseguire il backup di macchine virtuali Windows in Azure | Microsoft Docs
description: Proteggere le macchine virtuali Windows eseguendo il backup con Backup di Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 20760650b093216a2929de580f5971c45e0534a8
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Eseguire il backup di macchine virtuali Windows in Azure

È possibile proteggere i dati eseguendo backup a intervalli regolari. Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si ripristina da un punto di recupero, è possibile ripristinare la macchina virtuale intera o parziale. Questo articolo spiega come ripristinare un singolo file in una macchina virtuale che esegue Windows Server e IIS. Se non si dispone già di una macchina virtuale da usare, è possibile crearne una usando la [guida introduttiva di Windows](quick-create-portal.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un backup di una macchina virtuale
> * Pianificare un backup giornaliero
> * Ripristinare un file da un backup




## <a name="backup-overview"></a>Panoramica del servizio Backup

Quando il servizio Backup di Azure avvia un processo di backup, attiva l'estensione per il backup per acquisire uno snapshot temporizzato. Il servizio Backup di Azure usa l'estensione _VMSnapshot_. L'estensione viene installata durante il primo backup della macchina virtuale se la macchina virtuale è in esecuzione. Se la macchina virtuale non è in esecuzione, il servizio Backup crea uno snapshot dell'archivio sottostante (poiché non si verifica alcuna scrittura di applicazione durante l'arresto della macchina virtuale).

Quando crea uno snapshot delle macchine virtuali di Windows, il servizio Backup si coordina con il servizio Copia Shadow del volume (VSS) per ottenere uno snapshot coerente dei dischi delle macchine virtuali. Dopo che il servizio Backup di Azure crea lo snapshot, i data vengono trasferiti nell'insieme di credenziali. Per offrire la massima efficienza, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

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

## <a name="recover-a-file"></a>Recupero di un file

Se accidentalmente si elimina o si apportano modifiche a un file, è possibile usare Ripristino file per ripristinare il file dall'insieme di credenziali di backup. Ripristino file usa uno script eseguito nella macchina virtuale, per montare il punto di recupero come unità locale. Queste unità rimarranno montate per 12 ore in modo che sia possibile copiare i file dal punto di recupero e ripristinarli nella macchina virtuale.  

Questo esempio spiega come ripristinare il file di immagine usato nella pagina Web predefinita per IIS. 

1. Aprire un browser e connettersi all'indirizzo IP della macchina virtuale per visualizzare la pagina IIS predefinita.

    ![Pagina Web IIS predefinita](./media/tutorial-backup-vms/iis-working.png)

2. Connettersi alla macchina virtuale.
3. Nella macchina virtuale aprire **Esplora file**, passare a \inetpub\wwwroot ed eliminare il file **iisstart.png**.
4. Nel computer locale aggiornare il browser per verificare che l'immagine nella pagina IIS predefinita non sia più presente.

    ![Pagina Web IIS predefinita](./media/tutorial-backup-vms/iis-broken.png)

5. Nel computer locale aprire una nuova scheda e passare al [portale di Azure](https://portal.azure.com).
6. Nel menu a sinistra, selezionare **macchine virtuali** e selezionare la macchina virtuale dall'elenco.
8. Nel pannello della macchina virtuale, nella sezione **Impostazioni** fare clic su **Backup**. Verrà visualizzato il pannello **Backup**. 
9. Dal menu nella parte superiore del pannello scegliere **Ripristino file**. Verrà aperto il pannello **Ripristino file**.
10. In **Passaggio 1: Selezionare il punto di recupero** selezionare un punto di recupero dall'elenco a discesa.
11. In **Passaggio 2: Scaricare lo script per cercare e ripristinare i file** fare clic sul pulsante **Scarica eseguibile**. Salvare il file nella cartella **Downloads**.
12. Nel computer locale aprire **Esplora file**, andare alla cartella **Downloads** e copiare il file con estensione exe scaricato. Il nome del file verrà preceduto dal nome della macchina virtuale. 
13. Incollare il file con estensione exe sul desktop della macchina virtuale (tramite la connessione RDP). 
14. Passare al desktop della macchina virtuale e fare doppio clic sul file con estensione exe. Verrà aperto un prompt dei comandi e il punto di recupero sarà montato come condivisione di file a cui è possibile accedere. Al termine della creazione della condivisione, digitare **q** per chiudere il prompt dei comandi.
15. Nella macchina virtuale aprire **Esplora file** e passare alla lettera di unità usata per la condivisione file.
16. Passare a \inetpub\wwwroot e copiare **iisstart.png** dalla condivisione file e incollarlo in \inetpub\wwwroot. Ad esempio, copiare F:\inetpub\wwwroot\iisstart.png e incollarlo in c:\inetpub\wwwroot per ripristinare il file.
17. Nel computer locale aprire la scheda del browser in cui si è connessi all'indirizzo IP della macchina virtuale che mostra la pagina IIS predefinita. Premere CTRL + F5 per aggiornare la pagina del browser. Si noterà ora che l'immagine è stata ripristinata.
18. Nel computer locale tornare alla scheda del browser per il portale di Azure e in **Passaggio 3: Smontare i dischi dopo il ripristino** fare clic sul pulsante **Smontare i dischi**. Se si dimentica di eseguire questo passaggio, la connessione per il punto di montaggio viene chiusa automaticamente dopo 12 ore. Trascorse le 12 ore, è necessario scaricare un nuovo script per creare un nuovo punto di montaggio.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un backup di una macchina virtuale
> * Pianificare un backup giornaliero
> * Ripristinare un file da un backup

Passare all'esercitazione successiva per informazioni sul monitoraggio di macchine virtuali.

> [!div class="nextstepaction"]
> [Monitorare le macchine virtuali](tutorial-monitoring.md)









