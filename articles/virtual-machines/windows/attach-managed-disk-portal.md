---
title: Collegare un disco dati gestito a una macchina virtuale Windows - Azure | Microsoft Docs
description: Come collegare un disco dati gestito nuovo a una macchina virtuale Windows nel portale di Azure tramite il modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 1c57ea841080fd87c7014e4e4520f17ccf15b156
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Collegare un disco dati gestito a una macchina virtuale Windows nel portale di Azure

Questo articolo illustra come collegare dischi gestiti nuovi a una macchina virtuale Windows tramite il portale di Azure. Prima di procedere, rivedere i suggerimenti seguenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md).
* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.

È anche possibile [collegare un disco dati usando Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Aggiungere un disco dati
1. Nel menu a sinistra fare clic su **Macchine virtuali**.
2. Selezionare la macchina virtuale dall'elenco.
3. Nella pagina della macchina virtuale, fare clic su **dischi**.
4. Nel **dischi** pagina, fare clic su **+ Aggiungi disco di dati**.
5. Nell'elenco a discesa per il nuovo disco, selezionare **Crea disco**.
6. Nel **disco gestito crea** pagina, digitare un nome per il disco e regolare le altre impostazioni in base alle esigenze. Al termine dell'operazione fare clic su **Crea**.
7. Nel **dischi** pagina, fare clic su **salvare** per salvare la nuova configurazione del disco per la macchina virtuale.
6. Dopo che Azure crea il disco e la collega alla macchina virtuale, il nuovo disco è elencato nelle impostazioni del disco della macchina virtuale in **dischi dati**.


## <a name="initialize-a-new-data-disk"></a>Inizializzare un nuovo disco dati

1. Connettersi alla macchina virtuale.
1. Fare clic sul menu Start della macchina virtuale, digitare **diskmgmt.msc** e premere **Invio**. Viene aperto lo snap-in Gestione disco.
2. Gestione disco riconosce che si dispone di un disco non inizializzato e **Inizializza disco** finestra popup.
3. Assicurarsi di selezionare il nuovo disco e fare clic su **OK** per l'inizializzazione.
4. Il nuovo disco verrà visualizzato come **non allocato**. Fare clic sul disco e scegliere **Nuovo volume semplice...**. Il **semplice creazione guidata nuovo Volume** apre.
5. Esaminare la procedura guidata mantenendo tutti i valori predefiniti e al termine scegliere **Fine**.
6. Chiudere Gestione disco.
7. Verrà visualizzata una finestra popup che indica la necessità di formattare il nuovo disco prima di poterlo usare. Fare clic su **Formatta disco**.
8. Nella finestra di dialogo **Formatta il nuovo disco**, controllare le impostazioni, quindi fare clic su **Start**.
9. Verrà visualizzato un avviso che la formattazione dei dischi cancella tutti i dati. Fare clic su **OK**.
10. Dopo avere completato la formattazione, fare clic su **OK**.

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
