---
title: Collegare un disco dati gestito a una macchina virtuale Windows - Azure | Microsoft Docs
description: Come collegare un disco dati gestito a una macchina virtuale Windows usando il portale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: a8378e2d22bbd361d6adb80fabf2819ad226ff19
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471974"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Collegare un disco dati gestito a una macchina virtuale Windows usando il portale di Azure

Questo articolo illustra come collegare un nuovo disco dati gestito a una macchina virtuale Windows usando il portale di Azure. Il numero di dischi dati che è possibile collegare dipende dalle dimensioni della macchina virtuale. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](sizes.md).


## <a name="add-a-data-disk"></a>Aggiungere un disco dati

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella pagina **Macchina virtuale** selezionare **Dischi**.
4. Nella pagina **Dischi** selezionare **Aggiungi disco dati**.
5. Nell'elenco a discesa per il nuovo disco selezionare **Crea disco**.
6. Nella pagina **Crea disco gestito** digitare un nome del disco e regolare le altre impostazioni in base alle esigenze. Al termine, selezionare **Crea**.
7. Nella pagina **Dischi** selezionare **Salva** per salvare la configurazione del nuovo disco della macchina virtuale.
8. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.


## <a name="initialize-a-new-data-disk"></a>Inizializzare un nuovo disco dati

1. Connettersi alla macchina virtuale.
1. Nella macchina virtuale in esecuzione selezionare il menu **Start** di Windows e immettere **diskmgmt.msc** nella casella di ricerca. Viene visualizzata la console **Gestione disco**.
2. Gestione disco rileva la presenza di un nuovo disco non inizializzato e viene quindi visualizzata la finestra **Inizializza disco**.
3. Verificare che il nuovo disco sia selezionato e fare clic su **OK** per inizializzarlo.
4. Il nuovo disco verrà visualizzato come **Non allocato**. Fare clic sul disco e scegliere **Nuovo volume semplice...**. Si avvia così la **Creazione guidata nuovo volume semplice**.
5. Eseguire la procedura guidata mantenendo tutti i valori predefiniti e, al termine, scegliere **Fine**.
6. Chiudere **Gestione disco**.
7. Viene visualizzata una finestra popup che indica la necessità di formattare il nuovo disco prima di poterlo usare. Selezionare **Formatta disco**.
8. Nella finestra **Formatta il nuovo disco** controllare le impostazioni e quindi selezionare **Avvia**.
9. Viene visualizzato un avviso per informare l'utente che la formattazione dei dischi cancella tutti i dati. Selezionare **OK**.
10. Al termine della formattazione, selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

- È anche possibile [collegare un disco dati usando PowerShell](attach-disk-ps.md).
- Se l'applicazione deve usare l'unità *D:*: per archiviare i dati, è possibile [modificare la lettera di unità del disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
