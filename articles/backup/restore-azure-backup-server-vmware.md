---
title: Ripristinare macchine virtuali VMware con il server di Backup di Azure
description: Usare il server di Backup di Azure (MABS) per ripristinare le macchine virtuali VMware in esecuzione in un server VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212328"
---
# <a name="restore-vmware-virtual-machines"></a>Ripristinare macchine virtuali VMware

In questo articolo viene illustrato come utilizzare Microsoft Azure Backup Server (MABS) per ripristinare i punti di ripristino della macchina virtuale VMware. Per una panoramica sull'utilizzo di MABS per il ripristino dei dati, vedere [Recover protected data](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). Nella Console di amministrazione MABS, esistono due modi per trovare i dati recuperabili: cercare o sfogliare. Durante il ripristino, può essere necessario o meno ripristinare i dati o le macchine virtuali nella stessa posizione. Per questo motivo, MABS supporta tre opzioni di ripristino per i backup delle macchine virtuali VMware:For this reason, MABS supports three recovery options for VMware VM backups:

* **Ripristino nel percorso originale**: usare questa opzione per ripristinare una macchina virtuale protetta nel percorso originale. È possibile ripristinare una macchina virtuale nella posizione originale solo se non sono stati aggiunti o eliminati dischi, dal momento che il backup è stato eseguito. In caso di aggiunta o eliminazione di dischi, è necessario usare il ripristino in un percorso alternativo.

* **Ripristino in un percorso alternativo**: quando la macchina virtuale originale risulta mancante o non si vuole interferire con la macchina virtuale originale, eseguire il ripristino della macchina virtuale in un percorso alternativo. Per ripristinare una macchina virtuale in un percorso alternativo, è necessario specificare il percorso di un host ESXi, il pool di risorse, la cartella, nonché l'archivio dati e il percorso di archiviazione. Per differenziare la macchina virtuale ripristinata dalla macchina virtuale originale, MABS aggiunge "-Recovered" al nome della macchina virtuale.

* Ripristino della **posizione dei singoli file (ILR):** se la macchina virtuale protetta è una macchina virtuale Windows Server, è possibile ripristinare singoli file/cartelle all'interno della macchina virtuale usando la funzionalità ILR di MABS. Per ripristinare singoli file, vedere la procedura più avanti in questo articolo.

## <a name="restore-a-recovery-point"></a>Ripristinare un punto di ripristino

1. Nella Console di amministrazione MABS, fare clic su Visualizzazione ripristino.

2. Usare il riquadro Sfoglia per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, i punti di ripristino disponibili vengono visualizzati nel riquadro Punti di ripristino per.

    ![Punti di ripristino disponibili](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Nel campo **Punti di ripristino per** usare il calendario e i menu a discesa per selezionare una data di creazione di un punto di ripristino. Per le date visualizzate in grassetto nel calendario sono disponibili punti di ripristino.

4. Fare clic su **Ripristina** sulla barra multifunzione per avviare **Ripristino guidato**.

    ![Ripristino guidato, Selezione ripristino](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Fare clic su **Avanti** per passare alla schermata **Specifica opzioni di ripristino**.

6. Nella schermata **Specifica opzioni di ripristino** fare clic su **Modifica** se si vuole abilitare la limitazione della larghezza di banda di rete. Per lasciare disabilitata la limitazione della larghezza di banda di rete, fare clic su **Avanti**. In questa schermata della procedura guidata non sono disponibili alte opzioni per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo Limita larghezza di banda selezionare **Abilita limitazione all'utilizzo della larghezza di banda** per attivare l'opzione. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.

7. Nella schermata **Selezione tipo di ripristino** specificare se si vuole eseguire il ripristino nell'istanza originale o in un nuovo percorso e fare clic su **Avanti**.

     * Se si sceglie **Ripristina nell'istanza originale** non è necessario effettuare altre selezioni nella procedura guidata. Vengono usati i dati per l'istanza originale.

     * Se si sceglie **Ripristina come macchina virtuale in qualsiasi host,** nella schermata Specifica **destinazione** fornire le informazioni per **Host ESXi, Pool di risorse, Cartella** e **Percorso**.

      ![Selezione tipo di ripristino](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Nella schermata **Riepilogo** controllare le impostazioni e fare clic su **Ripristina** per avviare il processo di ripristino. La schermata **Stato ripristino** mostra l'avanzamento dell'operazione di ripristino.

## <a name="restore-an-individual-file-from-a-vm"></a>Ripristinare un singolo file da una macchina virtuale

È possibile ripristinare singoli file da un punto di ripristino di una macchina virtuale protetta. Questa funzionalità è disponibile solo per le macchine virtuali Windows Server. Il ripristino di singoli file è simile al ripristino dell'intera macchina virtuale, con l'eccezione della ricerca dei file desiderati nel disco VMDK prima di avviare il processo di ripristino. Per ripristinare un singolo file o una selezione di file da una macchina virtuale Windows Server:

>[!NOTE]
>Il ripristino di un singolo file da una macchina virtuale è disponibile solo per le macchine virtuali Windows e i punti di ripristino del disco.

1. Nella Console di amministrazione MABS, fare clic su Visualizzazione **ripristino.**

2. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, i punti di ripristino disponibili vengono visualizzati nel riquadro Punti di ripristino per.

    ![Punti di ripristino disponibili](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Nel riquadro **Punti di ripristino per** usare il calendario per selezionare la data che contiene i punti di ripristino desiderati. A seconda della configurazione dei criteri di backup, per le date possono essere disponibili più punti di ripristino. Dopo aver selezionato il giorno in cui è stato rilevato il punto di ripristino, assicurarsi di aver scelto il tempo di **ripristino**corretto. Se per la data selezionata esistono più punti di ripristino, scegliere il punto di ripristino selezionandolo nel menu a discesa Ora ripristino. Dopo aver scelto il punto di ripristino, nel riquadro **Percorso** viene visualizzato l'elenco degli elementi ripristinabili.

4. Per trovare i file da ripristinare, nel riquadro **Percorso** fare doppio clic sull'elemento nella colonna **Elemento ripristinabile** per aprirlo. Selezionare il file, i file o le cartelle da ripristinare. Per selezionare più elementi, premere **CTRL** mentre si seleziona ogni elemento. Usare il riquadro **Percorso** per eseguire una ricerca nell'elenco dei file o cartelle visualizzati nella colonna **Elemento ripristinabile**. L'opzione **Elenco di ricerca seguente** non esegue la ricerca nelle sottocartelle. Per eseguire la ricerca nelle sottocartelle, fare doppio clic sulla cartella. Usare il pulsante **Su** per spostarsi da una cartella figlio alla cartella padre. È possibile selezionare più elementi (file e cartelle), ma devono essere nella stessa cartella padre. Non è possibile ripristinare elementi da più cartelle nello stesso processo di ripristino.

    ![Verifica selezione per ripristino](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Dopo avere selezionato gli elementi per il ripristino, nella barra multifunzione della Console di amministrazione fare clic su **Ripristina** per aprire **Ripristino guidato**. Nel Ripristino guidato la schermata **Verifica selezione per ripristino** mostra gli elementi selezionati da ripristinare.

6. Nella schermata **Specifica opzioni di ripristino** fare clic su **Modifica** se si vuole abilitare la limitazione della larghezza di banda di rete. Per lasciare disabilitata la limitazione della larghezza di banda di rete, fare clic su **Avanti**. In questa schermata della procedura guidata non sono disponibili alte opzioni per le macchine virtuali VMware. Se si sceglie di modificare la limitazione della larghezza di banda di rete, nella finestra di dialogo Limita larghezza di banda selezionare **Abilita limitazione all'utilizzo della larghezza di banda** per attivare l'opzione. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.
7. Nella schermata **Selezione tipo di ripristino** fare clic su **Avanti**. È possibile ripristinare i file o le cartelle solo in una cartella di rete.
8. Nella schermata **Specifica destinazione** fare clic su **Sfoglia** per trovare un percorso di rete per i file o le cartelle. MABS crea una cartella in cui vengono copiati tutti gli elementi recuperati. Il nome della cartella ha il prefisso MABS_day al mese. Quando si seleziona un percorso per i file o le cartelle ripristinati, vengono visualizzati i dettagli per tale percorso (destinazione, percorso di destinazione e spazio disponibile).

    ![Specificare la posizione in cui recuperare i file](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Nella schermata **Specifica opzioni di ripristino** scegliere quale impostazione di sicurezza applicare. È possibile scegliere di modificare la limitazione per l'utilizzo della larghezza di banda di rete, ma la limitazione è disabilitata per impostazione predefinita. Anche le opzioni **Recupero basato su SAN** e **Notifica** non sono abilitate.
10. Nella schermata **Riepilogo** controllare le impostazioni e fare clic su **Ripristina** per avviare il processo di ripristino. La schermata **Stato ripristino** mostra l'avanzamento dell'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi relativi all'utilizzo del server di Backup di Azure, vedere la guida alla [risoluzione dei problemi per](./backup-azure-mabs-troubleshoot.md)il server di Backup di Azure.
