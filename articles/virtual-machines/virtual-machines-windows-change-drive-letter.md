---
title: "Usare l&quot;unità D come unità dati in una macchina virtuale Windows | Microsoft Docs"
description: "Descrive come modificare le lettere di unità per una VM Windows, in modo da poter usare l&quot;unità D: come unità dati."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 53e053fe12ea6753b6a47443ab91948c9a753a17
ms.lasthandoff: 03/25/2017


---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Usare l'unità D come unità dati in una macchina virtuale Windows
Se l'applicazione deve usare l'unità D per archiviare i dati, seguire le istruzioni seguenti per usare una lettera di unità diversa per il disco temporaneo. Non utilizzare mai il disco temporaneo per archiviare i dati da conservare.

Se si ridimensiona o si **Arresta (dealloca)** una macchina virtuale, potrebbe essere attivato il posizionamento della macchina virtuale su un nuovo hypervisor. Tale posizionamento può attivare un evento di manutenzione pianificato o non pianificato. In questo scenario il disco temporaneo sarà riassegnato alla prima lettera di unità disponibile. Se si dispone di un'applicazione che richiede specificamente l'unità D:, è necessario seguire questi passaggi per spostare temporaneamente pagefile.sys, collegare un nuovo disco dati e assegnargli la lettera D, quindi spostare di nuovo pagefile.sys nell’unità temporanea. Al termine, Azure non riprenderà D: se la VM viene spostata in un hypervisor diverso.

Per ulteriori informazioni sull'utilizzo del disco temporaneo in Azure, vedere [Informazioni sull'unità temporanea in Macchine virtuali di Microsoft Azure.](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Collegare il disco dati
Per prima cosa è necessario collegare il disco dati alla macchina virtuale. 

* Per usare il portale, vedere [Come collegare un disco dati nel portale di Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Per usare il portale classico, vedere [Come collegare un disco dati a una macchina virtuale Windows](windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Spostare temporaneamente pagefile.sys nell'unità C
1. Connettersi alla macchina virtuale. 
2. Fare clic con il pulsante destro del mouse sul menu **Start** e selezionare **Sistema**.
3. Nel menu a sinistra selezionare **Impostazioni di sistema avanzate**.
4. Nella sezione **Prestazioni** selezionare **Impostazioni**.
5. Selezionare la scheda **Avanzate** .
6. Nella sezione **Memoria virtuale** selezionare **Modifica**.
7. Selezionare l'unità **C** e quindi fare clic su **Dimensioni gestite dal sistema** e su **Imposta**.
8. Selezionare l'unità **D** e quindi fare clic su **Nessun file di paging** e su **Imposta**.
9. Fare clic su Applica. Verrà visualizzato un avviso che informa che è necessario riavviare il computer per rendere effettive le modifiche.
10. Riavviare la macchina virtuale.

## <a name="change-the-drive-letters"></a>Modificare le lettere di unità
1. Dopo il riavvio della macchina virtuale, accedere alla macchina virtuale.
2. Fare clic sul menu **Start**, digitare **diskmgmt.msc** e premere Invio. Verrà avviato Gestione disco.
3. Fare clic con il pulsante destro del mouse su **D**, l'unità di archiviazione temporanea, e quindi selezionare **Cambia lettera e percorso di unità**.
4. In Lettera unità selezionare una nuova unità, ad esempio **T**, e quindi fare clic su **OK**. 
5. Fare clic con il pulsante destro del mouse sul disco dati e selezionare **Cambia lettera e percorso di unità**.
6. In Lettera unità selezionare l'unità **D** e quindi fare clic su **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Spostare nuovamente pagefile.sys nell'unità di archiviazione temporanea
1. Fare clic con il pulsante destro del mouse sul menu **Start** e selezionare **Sistema**.
2. Nel menu a sinistra selezionare **Impostazioni di sistema avanzate**.
3. Nella sezione **Prestazioni** selezionare **Impostazioni**.
4. Selezionare la scheda **Avanzate** .
5. Nella sezione **Memoria virtuale** selezionare **Modifica**.
6. Selezionare l'unità del sistema operativo **C** e quindi fare clic su **Nessun file di paging** e su **Imposta**.
7. Selezionare l'unità di archiviazione temporanea **T** e quindi fare clic su **Dimensioni gestite dal sistema** e su **Imposta**.
8. Fare clic su **Apply**. Verrà visualizzato un avviso che informa che è necessario riavviare il computer per rendere effettive le modifiche.
9. Riavviare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
* È possibile aumentare la memoria disponibile per la macchina virtuale [collegando un disco dati aggiuntivo](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


