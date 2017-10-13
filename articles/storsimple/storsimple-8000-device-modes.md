---
title: "Cambiare la modalità del dispositivo StorSimple | Microsoft Docs"
description: "Vengono descritte le modalità del dispositivo StorSimple e viene illustrato come utilizzare Windows PowerShell per StorSimple per modificare la modalità del dispositivo."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dd160ede1189b0de544c8cf5db3b13228d212419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Cambiare le modalità del dispositivo sul dispositivo StorSimple

In questo articolo viene fornita una breve descrizione delle varie modalità in cui può operare il dispositivo StorSimple. Il dispositivo StorSimple può funzionare in tre modalità: normale, manutenzione e ripristino.

Una volta letto l'articolo, si sarà in grado di:

* Quali sono le modalità del dispositivo StorSimple
* Come determinare la modalità corrente del dispositivo StorSimple
* Come cambiare la modalità da normale a manutenzione e *viceversa*

Le attività di gestione precedenti possono essere eseguite solo tramite l'interfaccia di Windows PowerShell del dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Informazioni sulle modalità del dispositivo StorSimple

Il dispositivo StorSimple può funzionare in modalità normale, manutenzione o ripristino. Ognuna di queste modalità viene brevemente descritta di seguito.

### <a name="normal-mode"></a>Modalità normale

Si tratta della modalità operativa normale di un dispositivo StorSimple completamente configurato. Per impostazione predefinita, il dispositivo deve essere in modalità normale.

### <a name="maintenance-mode"></a>Modalità di manutenzione

Talvolta potrebbe essere necessario avere il dispositivo StorSimple in modalità di manutenzione. Questa modalità consente di eseguire la manutenzione del dispositivo e installare gli aggiornamenti improvvisi, ad esempio quelli relativi al firmware del disco.

È possibile porre il sistema in modalità di manutenzione solo tramite Windows PowerShell per StorSimple. In questa modalità, tutte le richieste I/O sono sospese. Inoltre vengono arrestati servizi come la memoria ad accesso casuale non volatile (NVRAM) o il servizio cluster. Entrambi i controller vengono riavviati quando si entra o si esce dalla modalità. Quando si esce dalla modalità di manutenzione, tutti i servizi vengono ripresi e devono essere integri. L'operazione potrebbe richiedere alcuni minuti.

> [!NOTE]
> **La modalità di manutenzione è supportata solo in un dispositivo che funziona correttamente. Non è supportata in un dispositivo in cui uno o entrambi i controller non funzionano.**


### <a name="recovery-mode"></a>Modalità di ripristino

La modalità di ripristino può essere descritta come la "modalità provvisoria di Windows con supporto di rete". La modalità di ripristino interessa il team del supporto tecnico Microsoft e consente di eseguire la diagnostica del sistema. L'obiettivo principale della modalità di ripristino consiste nel recuperare i registri di sistema.

Se il sistema passa in modalità di ripristino, è necessario contattare il supporto tecnico Microsoft per i passaggi successivi. Per ulteriori informazioni, andare a [Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Non è possibile porre il dispositivo in modalità di ripristino. Se il dispositivo è in uno stato non valido, la modalità di ripristino tenta di impostare il dispositivo su uno stato che permetta al personale di supporto Microsoft di esaminarlo.**

## <a name="determine-storsimple-device-mode"></a>Determinare la modalità del dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Per determinare la modalità corrente del dispositivo

1. Accedere alla console seriale del dispositivo seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Esaminare il messaggio dell'intestazione nel menu della console seriale del dispositivo. Questo messaggio indica in modo esplicito se il dispositivo è in modalità di manutenzione o di ripristino. Se il messaggio non contiene informazioni specifiche relative alla modalità di sistema, il dispositivo è in modalità normale.

## <a name="change-the-storsimple-device-mode"></a>Cambiare la modalità del dispositivo StorSimple

Per eseguire la manutenzione o installare gli aggiornamenti in modalità manutenzione, è possibile porre il dispositivo StorSimple in modalità di manutenzione (dalla modalità normale). Eseguire le procedure seguenti per attivare o disattivare la modalità di manutenzione.

> [!IMPORTANT]
> Prima di entrare in modalità manutenzione, verificare che entrambi i controller del dispositivo siano integri effettuando l'accesso a **Impostazioni dispositivo > Integrità hardware**  nel Portale di Azure. Se uno o entrambi i controller non sono integri, contattare il supporto tecnico Microsoft per i passaggi successivi. Per ulteriori informazioni, andare a [Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Per attivare la modalità di manutenzione

1. Accedere alla console seriale del dispositivo seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è: `Password1`.
3. Al prompt dei comandi digitare 
   
    `Enter-HcsMaintenanceMode`
4. Viene visualizzato un messaggio di avviso indicante che la modalità di manutenzione interromperà tutte le richieste I/O e la connessione al portale di Azure e verrà richiesto di confermare che si intende procedere. Digitare **Y** per attivare la modalità di manutenzione.
5. Entrambi i controller verranno riavviati. Dopo aver completato il riavvio, il banner della console seriale indica che il dispositivo è in modalità di manutenzione. Di seguito è riportato un output di esempio.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Per uscire dalla modalità di manutenzione

1. Connettersi alla console seriale del dispositivo. Verificare nel messaggio dell'intestazione che il dispositivo è in modalità di manutenzione.
2. Al prompt dei comandi digitare:
   
    `Exit-HcsMaintenanceMode`
3. Verranno visualizzati un messaggio di avviso e un messaggio di conferma. Digitare **Y** per uscire dalla modalità di manutenzione.
4. Entrambi i controller verranno riavviati. Dopo aver completato il riavvio, il banner della console seriale indica che il dispositivo è in modalità normale. Di seguito è riportato un output di esempio.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [applicare gli aggiornamenti in modalità normale e manutenzione](storsimple-update-device.md) al dispositivo StorSimple.

