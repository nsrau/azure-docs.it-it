---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165074"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Per installare aggiornamenti regolari tramite Windows PowerShell per StorSimple
1. Aprire la console seriale del dispositivo e scegliere l'opzione 1, **Accedi con accesso completo**. Digitare la password. La password predefinita è *Password1*. 
2. Al prompt dei comandi digitare:
   
     `Get-HcsUpdateAvailability`
   
    Verrà notificato se sono disponibili aggiornamenti e se gli aggiornamenti sono problematici o meno.
3. Al prompt dei comandi digitare:
   
     `Start-HcsUpdate`
   
    Il processo di aggiornamento verrà avviato.

> [!IMPORTANT]
> * Questo comando si applica solo agli aggiornamenti regolari. È possibile eseguire questo comando in un solo controller, ma verranno aggiornati entrambi i controller. 
> * Durante il processo di aggiornamento è possibile che venga eseguito il failover di un controller. Tale failover, tuttavia, non avrà effetti sulla disponibilità o sul funzionamento del sistema.
> 
> 

