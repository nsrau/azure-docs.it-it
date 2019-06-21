---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180675"
---
Seguire i passaggi alle circostanze specifiche.

### <a name="unregister-a-connected-process-server"></a>Annullare la registrazione di un server di elaborazione connesso

1. Stabilire una connessione remota al server di elaborazione come amministratore.
2. Nel **Pannello di controllo**aprire **programmi > Disinstalla un programma**.
3. Il programma di disinstallazione **Server di destinazione di Microsoft Azure Site Recovery Mobility Service/Master**.
4. Il programma di disinstallazione **Server di configurazione/elaborazione di Microsoft Azure Site Recovery**.
5. Dopo che i programmi nei passaggi 3 e 4 vengono disinstallati, disinstallare **dipendenze Server di configurazione/elaborazione di Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Annullare la registrazione di un server di elaborazione disconnesso

Usare questi passaggi solo se non è possibile ripristinare la macchina in cui è installato il server di elaborazione.

1. Accedere come amministratore del server di configurazione.
2. Aprire un prompt dei comandi amministrativo e passare a `%ProgramData%\ASR\home\svsystems\bin`.
3. Eseguire questo comando per ottenere un elenco di uno o più server di elaborazione.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Numero No: il numero di serie di server di processo.
    - IP/nome: L'indirizzo IP e nome del computer che esegue il server di elaborazione.
    - Heartbeat: Data ultimo heartbeat dal computer server di elaborazione.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Specificare il numero di serie del server di elaborazione che si desidera annullare la registrazione.
5. Rimuovere tutti i dettagli dal sistema, l'annullamento della registrazione di un server di elaborazione e viene visualizzato il messaggio: **È stata annullata la registrazione nome-server > (server-IP-address)**

