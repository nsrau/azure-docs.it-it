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
ms.date: 05/09/2017
ms.author: cynthn
ms.openlocfilehash: f0cf88a06c5470ef173b22e7213419a6c8760723
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Collegare un disco dati gestito a una macchina virtuale Windows nel portale di Azure

Questo articolo illustra come collegare dischi gestiti nuovi a una macchina virtuale Windows tramite il portale di Azure. Prima di procedere, rivedere i suggerimenti seguenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md).
* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.

È anche possibile [collegare un disco dati usando Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Aggiungere un disco dati
1. Nel menu a sinistra fare clic su **Macchine virtuali**.
2. Selezionare la macchina virtuale dall'elenco.
3. Nel pannello Macchine virtuali, fare clic su **Dischi**.
   4. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
5. Nell'elenco a discesa per il nuovo disco, selezionare **Crea vuoto...**.
6. Nel pannello **Crea disco gestito**, digitare un nome per il disco e regolare le altre impostazioni in base alle esigenze. Al termine dell'operazione fare clic su **Crea**.
7. Nel pannello **Dischi**, fare clic Salva per salvare la configurazione del nuovo disco della macchina virtuale.
6. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.


## <a name="initialize-a-new-data-disk"></a>Inizializzare un nuovo disco dati

1. Connettersi alla macchina virtuale.
1. Fare clic sul menu Start della macchina virtuale, digitare **diskmgmt.msc** e premere **Invio**. Verrà avviato lo snap-in Gestione disco.
2. Gestione disco rileva la disponibilità di un nuovo disco non inizializzato e viene visualizzata la finestra Inizializza disco.
3. Assicurarsi di selezionare il nuovo disco e fare clic su **OK** per l'inizializzazione.
4. Il nuovo disco verrà visualizzato come **Non allocato**. Fare clic sul disco e scegliere **Nuovo volume semplice...**. Si avvia così la **Creazione guidata nuovo volume semplice**.
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
