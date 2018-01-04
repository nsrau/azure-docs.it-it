---
title: "Usare l'unità D come unità dati in una macchina virtuale Windows | Microsoft Docs"
description: "Descrive come modificare le lettere di unità per una VM Windows, in modo da poter usare l'unità D: come unità dati."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: a4e29ce7ebb20753c3313e1148e1c91e853f49f5
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Usare l'unità D come unità dati in una macchina virtuale Windows
Se l'applicazione deve usare l'unità D per archiviare i dati, seguire le istruzioni seguenti per usare una lettera di unità diversa per il disco temporaneo. Non utilizzare mai il disco temporaneo per archiviare i dati da conservare.

Se si ridimensiona o si **Arresta (dealloca)** una macchina virtuale, potrebbe essere attivato il posizionamento della macchina virtuale su un nuovo hypervisor. Tale posizionamento può attivare un evento di manutenzione pianificato o non pianificato. In questo scenario il disco temporaneo sarà riassegnato alla prima lettera di unità disponibile. Se si dispone di un'applicazione che richiede specificamente l'unità D:, è necessario seguire questi passaggi per spostare temporaneamente pagefile.sys, collegare un nuovo disco dati e assegnargli la lettera D, quindi spostare di nuovo pagefile.sys nell’unità temporanea. Al termine, Azure non riprenderà D: se la VM viene spostata in un hypervisor diverso.

Per ulteriori informazioni sull'utilizzo del disco temporaneo in Azure, vedere [Informazioni sull'unità temporanea in Macchine virtuali di Microsoft Azure.](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Collegare il disco dati
Per prima cosa è necessario collegare il disco dati alla macchina virtuale. Per eseguire questa operazione tramite il portale, vedere [How to attach a managed data disk in the Azure portal](attach-managed-disk-portal.md) (Come collegare un disco dati gestito nel Portale di Azure).

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
* È possibile aumentare la memoria disponibile per la macchina virtuale [collegando un disco dati aggiuntivo](attach-managed-disk-portal.md).

