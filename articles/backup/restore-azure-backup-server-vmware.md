---
title: Ripristinare le macchine virtuali VMware con server di Backup di Azure
description: Usare server di Backup di Azure (MAB) per ripristinare le macchine virtuali VMware in esecuzione su un server VMware vCenter/ESXi.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: dacurwin
ms.openlocfilehash: fd851822a7068928a2f332c240bc33b70b1e16f6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642305"
---
# <a name="restore-vmware-virtual-machines"></a>Ripristinare macchine virtuali VMware

Questo articolo illustra come usare Backup di Microsoft Azure Server (MAB) per ripristinare i punti di ripristino delle macchine virtuali VMware. Per una panoramica sull'uso di MAB per ripristinare i dati, vedere [ripristinare i dati protetti](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). Nel Console di amministrazione di MAB sono disponibili due modi per trovare i dati ripristinabili, ovvero la ricerca o l'esplorazione. Quando si recuperano i dati, è possibile o non si vuole ripristinare i dati o una macchina virtuale nello stesso percorso. Per questo motivo, MAB supporta tre opzioni di ripristino per i backup di macchine virtuali VMware:

• **Ripristino del percorso originale (OLR)** : usare OLR per ripristinare una macchina virtuale protetta nel percorso originale. È possibile ripristinare una macchina virtuale nel percorso originale solo se non sono stati aggiunti o eliminati dischi, perché il backup è stato eseguito. Se i dischi sono stati aggiunti o eliminati, è necessario utilizzare il ripristino del percorso alternativo.

• **Ripristino in un percorso alternativo (ALR)** : quando la macchina virtuale originale non è presente o non si vuole disturbare la VM originale, ripristinare la macchina virtuale in un percorso alternativo. Per ripristinare una macchina virtuale in un percorso alternativo, è necessario specificare il percorso di un host ESXi, un pool di risorse, una cartella e il percorso e l'archivio dati di archiviazione. Per distinguere la macchina virtuale ripristinata dalla VM originale, MAB aggiunge "-recovered" al nome della macchina virtuale.

• **Ripristino del percorso dei singoli file (ILR)** : se la macchina virtuale protetta è una macchina virtuale Windows Server, è possibile ripristinare singoli file o cartelle all'interno della macchina virtuale usando la funzionalità ILR di MAB. Per ripristinare singoli file, vedere la procedura più avanti in questo articolo.


## <a name="restore-a-recovery-point"></a>Ripristinare un punto di ripristino

1.  In Console di amministrazione di MAB fare clic su visualizzazione ripristino.

2.  Usare il riquadro Sfoglia, sfogliare o filtrare per trovare la VM che si vuole ripristinare. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro punti di ripristino vengono visualizzati i punti di ripristino disponibili. 

    ![Punti di ripristino disponibili](./media/restore-azure-backup-server-vmware/recovery-points.png)

3.  Nel campo **punti di ripristino per** usare il calendario e i menu a discesa per selezionare una data in cui è stato effettuato un punto di ripristino. Le date del calendario in grassetto hanno punti di ripristino disponibili.

4.  Sulla barra multifunzione dello strumento fare clic su **Ripristina** per aprire il **Ripristino guidato**. 

    ![Ripristino guidato, verifica selezione per ripristino](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5.  Fare clic su **Avanti** per passare alla schermata **Specifica opzioni di ripristino** .

6.  Nella schermata **Specifica opzioni di ripristino** , se si desidera abilitare la limitazione della larghezza di banda di rete, fare clic su **modifica**. Per lasciare disabilitata la limitazione della rete, fare clic su **Avanti**. Non sono disponibili altre opzioni in questa schermata della procedura guidata per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo limitazione selezionare **Abilita limitazione all'utilizzo della larghezza di banda di rete** per attivarla. Dopo l'abilitazione, configurare le **Impostazioni** e la **pianificazione del lavoro**.

7.  Nella schermata **Selezione tipo di ripristino** scegliere se eseguire il ripristino nell'istanza originale o in un nuovo percorso e fare clic su **Avanti**.

     * Se si sceglie **Ripristina nell'istanza originale**, non è necessario effettuare altre scelte nella procedura guidata. Vengono utilizzati i dati per l'istanza originale.

    * Se si sceglie **Ripristina come macchina virtuale in qualsiasi host**, nella schermata **specifica destinazione specificare** le informazioni per l' **host ESXi, il pool di risorse** , la cartella e il **percorso**. 

      ![Seleziona tipo di ripristino](./media/restore-azure-backup-server-vmware/recovery-type.png)

8.    Nella schermata **Riepilogo** , rivedere le impostazioni e fare clic su **Ripristina** per avviare il processo di ripristino. La schermata **Stato ripristino** Mostra l'avanzamento dell'operazione di ripristino.

## <a name="restore-an-individual-file-from-a-vm"></a>Ripristinare un singolo file da una macchina virtuale

È possibile ripristinare singoli file da un punto di ripristino della macchina virtuale protetto. Questa funzionalità è disponibile solo per le macchine virtuali Windows Server. Il ripristino di singoli file è simile al ripristino dell'intera macchina virtuale, ad eccezione del fatto che si accede al VMDK e si trovano i file desiderati, prima di avviare il processo di ripristino. Per ripristinare un singolo file o selezionare i file da una macchina virtuale Windows Server:

1.  In Console di amministrazione di MAB fare clic su visualizzazione **ripristino** .

2.  Usare il riquadro **Sfoglia** , sfogliare o filtrare per trovare la VM che si vuole ripristinare. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro punti di ripristino vengono visualizzati i punti di ripristino disponibili.

    ![Punti di ripristino disponibili](./media/restore-azure-backup-server-vmware/recovery-points.png)
 
3.  Nel riquadro **punti di ripristino per:** usare il calendario per selezionare la data che contiene i punti di ripristino desiderati. A seconda di come è stato configurato il criterio di backup, le date possono avere più di un punto di ripristino. Dopo aver selezionato il giorno in cui è stato creato il punto di ripristino, assicurarsi di aver scelto il **tempo di ripristino**corretto. Se la data selezionata include più punti di ripristino, scegliere il punto di ripristino selezionandolo nel menu a discesa ora ripristino. Una volta scelto il punto di ripristino, l'elenco degli elementi ripristinabili viene visualizzato nel riquadro **percorso:** .

4.  Per trovare i file che si desidera ripristinare, nel riquadro **percorso** fare doppio clic sull'elemento nella colonna **elemento ripristinabile** per aprirlo. Selezionare il file, i file o le cartelle che si desidera ripristinare. Per selezionare più elementi, premere il tasto **CTRL** mentre si seleziona ogni elemento. Utilizzare il riquadro **percorso** per eseguire una ricerca nell'elenco di file o cartelle presenti nella colonna **elemento ripristinabile** . L' **elenco di ricerca seguente** non esegue la ricerca nelle sottocartelle. Per eseguire la ricerca nelle sottocartelle, fare doppio clic sulla cartella. Utilizzare il pulsante **freccia su** per spostarsi da una cartella figlio alla cartella padre. È possibile selezionare più elementi (file e cartelle), ma devono trovarsi nella stessa cartella padre. Non è possibile recuperare elementi da più cartelle nello stesso processo di ripristino.

5.  Dopo aver selezionato gli elementi per il ripristino, nella barra multifunzione dello strumento Console di amministrazione fare clic su **Ripristina** per aprire il **Ripristino guidato**. Nel ripristino guidato la schermata **Verifica selezione per ripristino** Mostra gli elementi selezionati da ripristinare. 
    ![Verifica selezione per ripristino](./media/restore-azure-backup-server-vmware/review-recovery.png)

6.  Nella schermata **Specifica opzioni di ripristino** , se si desidera abilitare la limitazione della larghezza di banda di rete, fare clic su **modifica**. Per lasciare disabilitata la limitazione della rete, fare clic su **Avanti**. Non sono disponibili altre opzioni in questa schermata della procedura guidata per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo limitazione selezionare **Abilita limitazione all'utilizzo della larghezza di banda di rete** per attivarla. Dopo l'abilitazione, configurare le **Impostazioni** e la **pianificazione del lavoro**.
7.  Nella schermata **Selezione tipo di ripristino** fare clic su **Avanti**. È possibile ripristinare solo i file o le cartelle in una cartella di rete.
8.  Nella schermata **specifica destinazione** fare clic su **Sfoglia** per trovare un percorso di rete per i file o le cartelle. MAB crea una cartella in cui vengono copiati tutti gli elementi ripristinati. Il nome della cartella ha il prefisso MABS_day-month-year. Quando si seleziona un percorso per i file o la cartella ripristinati, vengono forniti i dettagli relativi a tale percorso (destinazione, percorso di destinazione e spazio disponibile). 

       ![Specificare il percorso per il ripristino dei file](./media/restore-azure-backup-server-vmware/specify-destination.png)

9.  Nella schermata **Specifica opzioni di ripristino** scegliere le impostazioni di sicurezza da applicare. È possibile scegliere di modificare la limitazione per l'utilizzo della larghezza di banda di rete, ma la limitazione è disabilitata per impostazione predefinita. Inoltre, il ripristino e la **notifica** **San** non sono abilitati.
10. Nella schermata **Riepilogo** , rivedere le impostazioni e fare clic su **Ripristina** per avviare il processo di ripristino. La schermata **Stato ripristino** Mostra l'avanzamento dell'operazione di ripristino.


## <a name="next-steps"></a>Passaggi successivi
Per la risoluzione dei problemi relativi all'uso di server di Backup di Azure, vedere la [Guida alla risoluzione dei problemi per server di backup di Azure](./backup-azure-mabs-troubleshoot.md).

