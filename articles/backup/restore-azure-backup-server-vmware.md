---
title: Ripristinare macchine virtuali VMware con il server di Backup di Azure
description: Usare server di Backup di Azure (MAB) per ripristinare le macchine virtuali VMware in esecuzione su un server VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263521"
---
# <a name="restore-vmware-virtual-machines"></a>Ripristinare macchine virtuali VMware

Questo articolo illustra come usare Backup di Microsoft Azure Server (MAB) per ripristinare i punti di ripristino delle macchine virtuali VMware. Per una panoramica sull'uso di MAB per ripristinare i dati, vedere [ripristinare i dati protetti](./backup-azure-alternate-dpm-server.md). Nel Console di amministrazione di MAB sono disponibili due modi per trovare i dati ripristinabili, ovvero la ricerca o l'esplorazione. Durante il ripristino, può essere necessario o meno ripristinare i dati o le macchine virtuali nella stessa posizione. Per questo motivo, MAB supporta tre opzioni di ripristino per i backup di macchine virtuali VMware:

* **Ripristino nel percorso originale**: usare questa opzione per ripristinare una macchina virtuale protetta nel percorso originale. È possibile ripristinare una macchina virtuale nel percorso originale solo se non sono stati aggiunti o eliminati dischi, perché il backup è stato eseguito. In caso di aggiunta o eliminazione di dischi, è necessario usare il ripristino in un percorso alternativo.

* **Ripristino in un percorso alternativo**: quando la macchina virtuale originale risulta mancante o non si vuole interferire con la macchina virtuale originale, eseguire il ripristino della macchina virtuale in un percorso alternativo. Per ripristinare una macchina virtuale in un percorso alternativo, è necessario specificare il percorso di un host ESXi, il pool di risorse, la cartella, nonché l'archivio dati e il percorso di archiviazione. Per distinguere la macchina virtuale ripristinata dalla VM originale, MAB aggiunge "-recovered" al nome della macchina virtuale.

* **Ripristino del percorso dei singoli file (ILR)** : se la macchina virtuale protetta è una macchina virtuale Windows Server, è possibile ripristinare singoli file o cartelle all'interno della macchina virtuale usando la funzionalità ILR di MAB. Per ripristinare singoli file, vedere la procedura più avanti in questo articolo.

## <a name="restore-a-recovery-point"></a>Ripristinare un punto di ripristino

1. In Console di amministrazione di MAB selezionare **visualizzazione ripristino**.

2. Usare il riquadro Sfoglia per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, i punti di ripristino disponibili vengono visualizzati nel riquadro Punti di ripristino per.

    ![Punti di ripristino disponibili](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Nel campo **Punti di ripristino per** usare il calendario e i menu a discesa per selezionare una data di creazione di un punto di ripristino. Per le date visualizzate in grassetto nel calendario sono disponibili punti di ripristino.

4. Sulla barra multifunzione dello strumento selezionare **Ripristina** per aprire il **Ripristino guidato**.

    ![Ripristino guidato, verifica selezione per ripristino](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Selezionare **Avanti** per passare alla schermata **Specifica opzioni di ripristino** .

6. Nella schermata **Specifica opzioni di ripristino** , se si vuole abilitare la limitazione della larghezza di banda di rete, selezionare **modifica**. Per lasciare disabilitata la limitazione della rete, fare clic su **Avanti**. In questa schermata della procedura guidata non sono disponibili alte opzioni per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo Limita larghezza di banda selezionare **Abilita limitazione all'utilizzo della larghezza di banda** per attivare l'opzione. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.

7. Nella schermata **Selezione tipo di ripristino** scegliere se eseguire il ripristino nell'istanza originale o in un nuovo percorso. Fare quindi clic su **Avanti**.

     * Se si sceglie **Ripristina nell'istanza originale** non è necessario effettuare altre selezioni nella procedura guidata. Vengono usati i dati per l'istanza originale.

     * Se si sceglie **Ripristina come macchina virtuale in qualsiasi host**, nella schermata **specifica destinazione specificare** le informazioni per l' **host ESXi, il pool di risorse** , la cartella e il **percorso**.

      ![Selezione tipo di ripristino](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Nella schermata **Riepilogo** , rivedere le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

## <a name="restore-an-individual-file-from-a-vm"></a>Ripristinare un singolo file da una macchina virtuale

È possibile ripristinare singoli file da un punto di ripristino di una macchina virtuale protetta. Questa funzionalità è disponibile solo per le macchine virtuali Windows Server. Il ripristino di singoli file è simile al ripristino dell'intera macchina virtuale, con l'eccezione della ricerca dei file desiderati nel disco VMDK prima di avviare il processo di ripristino. Per ripristinare un singolo file o una selezione di file da una macchina virtuale Windows Server:

>[!NOTE]
>Il ripristino di un singolo file da una macchina virtuale è disponibile solo per i punti di ripristino di macchine virtuali e dischi Windows.

1. In Console di amministrazione di MAB selezionare visualizzazione **ripristino** .

2. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, nel **riquadro punti di ripristino** vengono visualizzati i punti di ripristino disponibili.

    ![Riquadro "punti di ripristino per"](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Nel riquadro **Punti di ripristino per** usare il calendario per selezionare la data che contiene i punti di ripristino desiderati. A seconda della configurazione dei criteri di backup, per le date possono essere disponibili più punti di ripristino. Dopo aver selezionato il giorno in cui è stato creato il punto di ripristino, assicurarsi di aver scelto il **tempo di ripristino**corretto. Se per la data selezionata esistono più punti di ripristino, scegliere il punto di ripristino selezionandolo nel menu a discesa Ora ripristino. Dopo aver scelto il punto di ripristino, nel riquadro **Percorso** viene visualizzato l'elenco degli elementi ripristinabili.

4. Per trovare i file da ripristinare, nel riquadro **Percorso** fare doppio clic sull'elemento nella colonna **Elemento ripristinabile** per aprirlo. Selezionare il file, i file o le cartelle da ripristinare. Per selezionare più elementi, premere **CTRL** mentre si seleziona ogni elemento. Usare il riquadro **Percorso** per eseguire una ricerca nell'elenco dei file o cartelle visualizzati nella colonna **Elemento ripristinabile**. L' **elenco di ricerca seguente** non esegue la ricerca nelle sottocartelle. Per eseguire la ricerca nelle sottocartelle, fare doppio clic sulla cartella. Usare il pulsante **Su** per spostarsi da una cartella figlio alla cartella padre. È possibile selezionare più elementi (file e cartelle), ma devono essere nella stessa cartella padre. Non è possibile ripristinare gli elementi da più cartelle nello stesso processo di ripristino.

    ![Verifica selezione per ripristino](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Dopo aver selezionato gli elementi per il ripristino, nella barra multifunzione dello strumento Console di amministrazione selezionare **Ripristina** per aprire il **Ripristino guidato**. Nel Ripristino guidato la schermata **Verifica selezione per ripristino** mostra gli elementi selezionati da ripristinare.

6. Nella schermata **Specifica opzioni di ripristino** , se si vuole abilitare la limitazione della larghezza di banda di rete, selezionare **modifica**. Per lasciare disabilitata la limitazione della rete, fare clic su **Avanti**. In questa schermata della procedura guidata non sono disponibili alte opzioni per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo Limita larghezza di banda selezionare **Abilita limitazione all'utilizzo della larghezza di banda** per attivare l'opzione. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.
7. Nella schermata **Selezione tipo di ripristino** selezionare **Avanti**. È possibile ripristinare i file o le cartelle solo in una cartella di rete.
8. Nella schermata **specifica destinazione** selezionare **Sfoglia** per trovare un percorso di rete per i file o le cartelle. MAB crea una cartella in cui vengono copiati tutti gli elementi ripristinati. Il nome della cartella ha il prefisso MABS_day-month-year. Quando si seleziona un percorso per i file o le cartelle ripristinati, vengono visualizzati i dettagli per tale percorso (destinazione, percorso di destinazione e spazio disponibile).

    ![Specificare il percorso per il ripristino dei file](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Nella schermata **Specifica opzioni di ripristino** scegliere quale impostazione di sicurezza applicare. È possibile scegliere di modificare la limitazione per l'utilizzo della larghezza di banda di rete, ma la limitazione è disabilitata per impostazione predefinita. Inoltre, il **ripristino San** e la **notifica** non sono abilitati.
10. Nella schermata **Riepilogo** , rivedere le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi relativi all'uso di server di Backup di Azure, vedere la [Guida alla risoluzione dei problemi per server di backup di Azure](./backup-azure-mabs-troubleshoot.md).
