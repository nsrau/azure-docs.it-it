---
title: Configurare Microsoft Azure Data Box Disk | Microsoft Docs
description: Usare questa esercitazione per imparare a configurare Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010362"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Esercitazione: Disimballare, collegare e sbloccare Azure Data Box Disk

Questa esercitazione descrive come disimballare, collegare e sbloccare Azure Data Box Disk.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Disimballare il Data Box Disk.
> * Collegare e sbloccare il Data Box Disk.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Sia stata completata l'[Esercitazione: Ordinare Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Sono stati ricevuti i dischi e lo stato del processo nel portale viene aggiornato a **Recapitato**.
3. Si dispone di un computer host su cui è possibile installare lo strumento di sblocco del Data Box Disk. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-disk-system-requirements.md).
    - Avere [Windows PowerShell 4 installato ](https://www.microsoft.com/download/details.aspx?id=40855).
    - Avere [.NET Framework 4.5 installato](https://www.microsoft.com/download/details.aspx?id=30653).

## <a name="unpack-your-disks"></a>Disimballare i dischi.

 Eseguire la procedura seguente per disimballare i dischi.

1. I Data Box Disk vengono spediti in una piccola scatola di spedizione. Aprire la scatola ed estrarre il contenuto. Controllare che la scatola contenga da 1 a 5 dischi SSD (Solid State Drive) e un cavo di collegamento USB per disco. Ispezionare la scatola per accertare eventuali prove di manomissione o qualsiasi altro danno evidente. 

    ![Imballaggio di spedizione del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Non aprire la scatola di spedizione se risulta manomessa o notevolmente danneggiata. Contattare il supporto tecnico Microsoft per valutare se i dischi funzionano correttamente o è necessaria una spedizione sostitutiva.
3. Verificare che la scatola contenga una custodia trasparente contenente un'etichetta indirizzo (sotto l'etichetta corrente) per la spedizione di ritorno. Se l'etichetta è stata persa o è danneggiata, è sempre possibile scaricarne e stamparne una nuova dal portale di Azure. 

    ![Etichetta indirizzo di Azure Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Conservare la scatola e la protezione in schiuma per la spedizione di ritorno dei dischi.

## <a name="connect-and-unlock-your-disks"></a>Collegare e sbloccare i dischi.

Eseguire la procedura seguente per collegare e sbloccare i dischi.

1. Usare il cavo in dotazione per collegare il disco a un computer Windows in cui è in esecuzione un sistema operativo supportato, come indicato nei prerequisiti. 

    ![Collegamento del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Nel portale di Azure passare a **Generale > Dettagli dispositivo**. 
3. Fare clic su **Scarica lo strumento di sblocco** del Data Box Disk. 

    ![Download dello strumento di sblocco del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Estrarre lo strumento sullo stesso computer che verrà usato per la copia dei dati.
5. Aprire una finestra del prompt dei comandi o eseguire Windows PowerShell come amministratore sullo stesso computer.
6. (Facoltativo) Per verificare se il computer in uso per sbloccare il disco soddisfa i requisiti del sistema operativo, eseguire il comando di verifica del sistema. Di seguito è riportato un output di esempio. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Nel portale di Azure passare a **Generale > Dettagli dispositivo**. Fare clic sull'icona di copia per copiare la passkey.
8. Eseguire `DataBoxDiskUnlock.exe` e fornire la passkey. Viene visualizzata la lettera di unità assegnata al disco. Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Ripetere i passaggi da 6 a 8 per tutti i reinserimenti futuri del disco. Usare il comando help se occorre assistenza con lo strumento di sblocco del Data Box Disk.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Dopo avere sbloccato il disco è possibile visualizzarne il contenuto.    

    ![Contenuto del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Disimballare il Data Box Disk.
> * Collegare e sbloccare i Data Box Disk.


Passare all'esercitazione successiva per informazioni su come copiare dati sul Data Box Disk.

> [!div class="nextstepaction"]
> [Copiare i dati sul Data Box Disk](./data-box-disk-deploy-copy-data.md)

