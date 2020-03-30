---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180675"
---
Segui i passaggi per le tue circostanze specifiche.

### <a name="unregister-a-connected-process-server"></a>Annullare la registrazione di un server di elaborazione connessoUnregister a connected process server

1. Stabilire una connessione remota al server di elaborazione come amministratore.
2. Nel **Pannello di controllo**aprire **Programmi > Disinstalla un programma**.
3. Disinstallare il programma **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Disinstallare il programma **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Dopo la disinstallazione dei programmi nei passaggi 3 e 4, disinstallare **Microsoft Azure Site Recovery Configuration/Process Server Dependencies**.

### <a name="unregister-a-disconnected-process-server"></a>Annullare la registrazione di un server di elaborazione disconnessoUnregister a disconnected process server

Utilizzare questi passaggi solo se non è possibile rianimare la macchina in cui è installato il server di elaborazione.

1. Accedere al server di configurazione come amministratore.
2. Aprire un prompt dei comandi `%ProgramData%\ASR\home\svsystems\bin`amministrativo e passare a .
3. Eseguire questo comando per ottenere un elenco di uno o più server di elaborazione.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. No: il numero di serie del server di elaborazione.
    - IP/Name: l'indirizzo IP e il nome del computer che esegue il server di elaborazione.
    - Heartbeat: ultimo heartbeat dal computer del server di elaborazione.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Specificare il numero di serie del server di elaborazione di cui si desidera annullare la registrazione.
5. Annullamento della registrazione di un server di elaborazione rimuovere tutti i relativi dettagli dal sistema e viene visualizzato il messaggio: **Nome server non registrato> (indirizzo IP del server)**

