---
title: 'Esercitazione: Eseguire il backup di macchine virtuali Windows nel portale di Azure'
description: In questa esercitazione viene illustrato come usare il portale di Azure per proteggere macchine virtuali Windows con Backup di Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 603bffe3d28214dbdcd51888925c3c653d0759e7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068190"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Esercitazione: Eseguire il backup e il ripristino di file per macchine virtuali Windows in Azure

È possibile proteggere i dati eseguendo backup a intervalli regolari. Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si esegue il ripristino da un punto di recupero, è possibile ripristinare la macchina virtuale intera o parziale. Questo articolo spiega come ripristinare un singolo file in una macchina virtuale che esegue Windows Server e IIS. Se non si dispone già di una macchina virtuale da usare, è possibile crearne una usando la [guida introduttiva di Windows](quick-create-portal.md). In questa esercitazione si apprenderà come:

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
1. Nel menu a sinistra selezionare **Macchine virtuali**. 
1. Dall'elenco selezionare la macchina virtuale di cui eseguire il backup.
1. Nel pannello della macchina virtuale, nella sezione **Operazioni**, fare clic su **Backup**. Verrà aperto il pannello **Abilita backup**.
1. In **Insieme di credenziali dei servizi di ripristino** fare clic su **Crea nuovo** e inserire un nome per il nuovo insieme di credenziali. Viene creato un nuovo insieme di credenziali nello stesso gruppo di risorse e nella stessa posizione della macchina virtuale.
1. In **Scegliere i criteri di backup** mantenere l'opzione predefinita **(Nuovo) DailyPolicy** e quindi fare clic su **Abilita backup**.
1. Per creare un punto di recupero iniziale, nel pannello **Backup** fare clic su **Esegui backup ora**.
1. Nel pannello **Esegui backup ora** fare clic sull'icona del calendario, usare il comando del calendario per scegliere la durata della conservazione del punto di ripristino e fare clic su **OK**.
1. Nel pannello **Backup** per la macchina virtuale viene visualizzato il numero di punti di ripristino completati.


    ![Punti di ripristino](./media/tutorial-backup-vms/backup-complete.png)
    
Il primo backup richiede circa 20 minuti. Al termine del backup, procedere con la parte successiva di questa esercitazione.

## <a name="recover-a-file"></a>Recupero di un file

Se accidentalmente si elimina o si apportano modifiche a un file, è possibile usare Ripristino file per ripristinare il file dall'insieme di credenziali di backup. Ripristino file usa uno script eseguito nella macchina virtuale, per montare il punto di recupero come unità locale. Queste unità rimangono montate per 12 ore in modo che sia possibile copiare i file dal punto di recupero e ripristinarli nella macchina virtuale.  

Questo esempio spiega come ripristinare il file di immagine usato nella pagina Web predefinita per IIS. 

1. Aprire un browser e connettersi all'indirizzo IP della macchina virtuale per visualizzare la pagina IIS predefinita.

    ![Pagina Web IIS predefinita](./media/tutorial-backup-vms/iis-working.png)

1. Connettersi alla macchina virtuale.
1. Nella macchina virtuale aprire **Esplora file**, passare a \inetpub\wwwroot ed eliminare il file **iisstart.png**.
1. Nel computer locale aggiornare il browser per verificare che l'immagine nella pagina IIS predefinita non sia più presente.

    ![Pagina Web IIS predefinita](./media/tutorial-backup-vms/iis-broken.png)

1. Nel computer locale, aprire una nuova scheda e passare al [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra selezionare **Macchine virtuali** e scegliere la macchina virtuale dall'elenco.
1. Nel pannello della macchina virtuale, nella sezione **Operazioni**, fare clic su **Backup**. Verrà visualizzato il pannello **Backup**. 
1. Dal menu nella parte superiore del pannello scegliere **Ripristino file**. Verrà aperto il pannello **Ripristino file**.
1. In **Passaggio 1: Selezionare il punto di recupero** selezionare un punto di recupero dall'elenco a discesa.
1. In **Passaggio 2: Scaricare lo script per cercare e ripristinare i file** fare clic sul pulsante **Scarica eseguibile**. Copiare la password per il file e salvarla in un luogo sicuro.
1. Nel computer locale aprire **Esplora file**, andare alla cartella **Downloads** e copiare il file con estensione exe scaricato. Il nome file verrà preceduto dal nome della macchina virtuale. 
1. Incollare il file con estensione exe sul desktop della macchina virtuale (tramite la connessione RDP). 
1. Passare al desktop della macchina virtuale e fare doppio clic sul file con estensione exe. Verrà avviato un prompt dei comandi. Il programma monta il punto di ripristino come condivisione file a cui è possibile accedere. Al termine della creazione della condivisione, digitare **q** per chiudere il prompt dei comandi.
1. Nella macchina virtuale aprire **Esplora file** e passare alla lettera di unità usata per la condivisione file.
1. Passare a \inetpub\wwwroot e copiare **iisstart.png** dalla condivisione file e incollarlo in \inetpub\wwwroot. Ad esempio, copiare F:\inetpub\wwwroot\iisstart.png e incollarlo in c:\inetpub\wwwroot per ripristinare il file.
1. Nel computer locale aprire la scheda del browser in cui si è connessi all'indirizzo IP della macchina virtuale che mostra la pagina IIS predefinita. Premere CTRL + F5 per aggiornare la pagina del browser. Si noterà ora che l'immagine è stata ripristinata.
1. Nel computer locale tornare alla scheda del browser per il portale di Azure e in **Passaggio 3: Smontare i dischi dopo il ripristino** fare clic sul pulsante **Smontare i dischi**. Se si dimentica di eseguire questo passaggio, la connessione al punto di montaggio viene chiusa automaticamente dopo 12 ore. Trascorse le 12 ore, è necessario scaricare un nuovo script per creare un nuovo punto di montaggio.





## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un backup di una macchina virtuale
> * Pianificare un backup giornaliero
> * Ripristinare un file da un backup

Passare all'esercitazione successiva per informazioni sul monitoraggio di macchine virtuali.

> [!div class="nextstepaction"]
> [Configurare la governance delle macchine virtuali](tutorial-govern-resources.md)









