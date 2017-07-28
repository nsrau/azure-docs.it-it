---
title: Collegare un disco dati non gestito a una macchina virtuale Windows - Azure | Microsoft Docs
description: Come collegare un disco dati non gestito nuovo o esistente a una macchina virtuale Windows nel portale di Azure tramite il modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c0886302c82641f8cc1a00d3972870d58ba8afb4
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Collegare un disco dati non gestito a una macchina virtuale Windows nel portale di Azure

Questo articolo illustra come collegare dischi non gestiti nuovi o esistenti a una macchina virtuale Windows tramite il portale di Azure. È anche possibile [collegare un disco dati usando PowerShell](./attach-disk-ps.md). Prima di procedere, rivedere i suggerimenti seguenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md).
* Per usare l'archiviazione Premium, è necessaria una macchina virtuale della serie DS o GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.


È anche possibile [collegare un disco dati usando Powershell](attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Trovare la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nel pannello Macchine virtuali, fare clic su **Dischi**.
   
Continuare seguendo le istruzioni per il collegamento di un [nuovo disco](#option-1-attach-a-new-disk) o un [disco esistente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opzione 1: Connettere e inizializzare un nuovo disco
1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. Nel pannello **Attach managed disk** (Connetti disco gestito), digitare un nome per il disco in **Nome**, quindi selezionare  **Nuovo (disco vuoto)** in **Tipo di origine**.
3. In **Contenitore di archiviazione**, fare clic sul pulsante **Sfoglia**, quindi passare all'account di archiviazione e al contenitore in cui si desidera che il nuovo disco rigido virtuale sia archiviato e fare clic su **Seleziona**. 
  
   ![Esaminare le impostazioni del disco](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. Dopo aver completato le impostazioni per il disco dati, fare clic su **OK**.
4. Nel pannello **Dischi**, fare clic su **Salva** per aggiungere il disco alla configurazione della macchina virtuale.


### <a name="initialize-a-new-data-disk"></a>Inizializzare un nuovo disco dati

1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Fare clic sul menu **Start**, digitare **diskmgmt.msc** e premere **Invio**. Verrà avviato lo snap-in Gestione disco.
2. Gestione disco rileva la disponibilità di un nuovo disco non inizializzato e viene visualizzata la finestra Inizializza disco.
3. Assicurarsi di selezionare il nuovo disco e fare clic su **OK** per l'inizializzazione.
4. Il nuovo disco verrà visualizzato come **Non allocato**. Fare clic sul disco e scegliere **Nuovo volume semplice...**. Si avvia così la **Creazione guidata nuovo volume semplice**.
5. Esaminare la procedura guidata mantenendo tutti i valori predefiniti e al termine scegliere **Fine**.
6. Chiudere Gestione disco.
7. Verrà visualizzata una finestra popup che indica la necessità di formattare il nuovo disco prima di poterlo usare. Fare clic su **Formatta disco**.
8. Nella finestra di dialogo **Formatta il nuovo disco**, controllare le impostazioni, quindi fare clic su **Start**.
9. Verrà visualizzato un avviso che la formattazione dei dischi cancella tutti i dati. Fare clic su **OK**.
10. Dopo avere completato la formattazione, fare clic su **OK**.


## <a name="option-2-attach-an-existing-disk"></a>Opzione 2: Collegare un disco esistente
1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. Nel pannello**Connetti disco non gestito**, in **Tipo di origine** selezionare **BLOB esistente**.

    ![Esaminare le impostazioni del disco](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. Fare clic sul pulsante **Sfoglia** per passare all'account e al contenitore di archiviazione in cui si trova il disco rigido virtuale esistente. Fare sul disco rigido virtuale, quindi fare clic su **Seleziona**.
4. Fare clic su **OK** nel pannello **Collega disco non gestito**.
5. Nel pannello **Dischi**, fare clic **Salva** per aggiungere il disco alla configurazione della macchina virtuale.
   


## <a name="use-trim-with-standard-storage"></a>Usare TRIM con l'archiviazione Standard

Se si usa l'archiviazione Standard (HDD), è consigliabile abilitare TRIM. TRIM ignora i blocchi inutilizzati nel disco facendo in modo che venga addebitato solo lo spazio di archiviazione usato effettivamente. In questo modo è possibile risparmiare sui costi quando si creano file di grandi dimensioni per poi eliminarli. 

Per verificare l'impostazione TRIM, è possibile eseguire questo comando. Aprire un prompt dei comandi nella macchina virtuale Windows e digitare:

```
fsutil behavior query DisableDeleteNotify
```

Se il comando restituisce 0, l'impostazione TRIM è abilitata correttamente. Se restituisce 1, eseguire questo comando per abilitare TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

Dopo l'eliminazione di dati dal disco è possibile verificare che le operazioni TRIM avvengano correttamente eseguendo la deframmentazione con TRIM:

```
defrag.exe <volume:> -l
```

È possibile garantire anche che l'intero volume sia tagliato formattandolo.


## <a name="next-steps"></a>Passaggi successivi
Se l'applicazione deve usare l'unità D: per archiviare i dati, è possibile [modificare la lettera di unità del disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


