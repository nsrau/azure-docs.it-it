---
title: "Cambiare la modalità del dispositivo StorSimple | Microsoft Docs"
description: "Vengono descritte le modalità del dispositivo StorSimple e viene illustrato come utilizzare Windows PowerShell per StorSimple per modificare la modalità del dispositivo."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 815d4bd539db8aebf2ee1eb62aef13b554b9cc47
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Cambiare le modalità del dispositivo sul dispositivo StorSimple
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per la versione di questo articolo per il nuovo portale di Azure, vedere [Cambiare le modalità del dispositivo sul dispositivo StorSimple](storsimple-8000-device-modes.md). Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

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
> </br>
> 
> 

### <a name="recovery-mode"></a>Modalità di ripristino
La modalità di ripristino può essere descritta come la "modalità provvisoria di Windows con supporto di rete". La modalità di ripristino interessa il team del supporto tecnico Microsoft e consente di eseguire la diagnostica del sistema. L'obiettivo principale della modalità di ripristino consiste nel recuperare i registri di sistema.

Se il sistema passa in modalità di ripristino, è necessario contattare il supporto tecnico Microsoft per i passaggi successivi. Per ulteriori informazioni, andare a [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Non è possibile porre il dispositivo in modalità di ripristino. Se il dispositivo è in uno stato non valido, la modalità di ripristino tenta di impostare il dispositivo su uno stato che permetta al personale di supporto Microsoft di esaminarlo.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Determinare la modalità del dispositivo StorSimple
#### <a name="to-determine-the-current-device-mode"></a>Per determinare la modalità corrente del dispositivo
1. Accedere alla console seriale del dispositivo seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Esaminare il messaggio dell'intestazione nel menu della console seriale del dispositivo. Questo messaggio indica in modo esplicito se il dispositivo è in modalità di manutenzione o di ripristino. Se il messaggio non contiene informazioni specifiche relative alla modalità di sistema, il dispositivo è in modalità normale.

## <a name="change-the-storsimple-device-mode"></a>Cambiare la modalità del dispositivo StorSimple
Per eseguire la manutenzione o installare gli aggiornamenti in modalità manutenzione, è possibile porre il dispositivo StorSimple in modalità di manutenzione (dalla modalità normale). Eseguire le procedure seguenti per attivare o disattivare la modalità di manutenzione.

> [!IMPORTANT]
> Prima di entrare in modalità manutenzione, verificare che entrambi i controller di dispositivi siano integri effettuando l'accesso a **Stato Hardware** nella pagina **Manutenzione** nel Portale di Azure classico. Se uno o entrambi i controller non sono integri, contattare il supporto tecnico Microsoft per i passaggi successivi. Per ulteriori informazioni, andare a [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Per attivare la modalità di manutenzione
1. Accedere alla console seriale del dispositivo seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è: `Password1`.
3. Al prompt dei comandi digitare 
   
    `Enter-HcsMaintenanceMode`
4. Verrà visualizzato un messaggio di avviso indicante che la modalità di manutenzione interromperà tutte le richieste I/O e la connessione al portale di Azure classico e verrà richiesto di confermare che si desidera procedere. Digitare **Y** per attivare la modalità di manutenzione.
5. Entrambi i controller verranno riavviati. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità di manutenzione.

#### <a name="to-exit-maintenance-mode"></a>Per uscire dalla modalità di manutenzione
1. Connettersi alla console seriale del dispositivo. Verificare nel messaggio dell'intestazione che il dispositivo è in modalità di manutenzione.
2. Al prompt dei comandi digitare:
   
    `Exit-HcsMaintenanceMode`
3. Verranno visualizzati un messaggio di avviso e un messaggio di conferma. Digitare **Y** per uscire dalla modalità di manutenzione.
4. Entrambi i controller verranno riavviati. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità normale.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [applicare gli aggiornamenti in modalità normale e manutenzione](storsimple-update-device.md) al dispositivo StorSimple.

