---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908536"
---
La procedura per annullare la registrazione di un server di elaborazione varia a seconda dello stato di connessione con il server di configurazione.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Annullare la registrazione di un server di elaborazione in stato connesso

1. Accedere in remoto al server di elaborazione come amministratore.
2. Avviare il **Pannello di controllo** e aprire **programmi > Disinstalla un programma**.
3. Disinstalla un programma denominato **Server di destinazione di Microsoft Azure Site Recovery Mobility Service/Master**.
4. Disinstalla un programma denominato **Server di configurazione/elaborazione di Microsoft Azure Site Recovery**.
5. Se vengono disinstallati i programmi nei passaggi 3 e 4, è possibile disinstallare **dipendenze Server di configurazione/elaborazione di Microsoft Azure Site Recovery**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Annullare la registrazione di un server di elaborazione in stato disconnesso

> [!WARNING]
> Utilizzare la procedura seguente se non è possibile ripristinare la macchina virtuale in cui è stato installato il Server di elaborazione.

1. Accedere al server di configurazione come amministratore.
2. Aprire un prompt dei comandi amministrativo e passare alla directory `%ProgramData%\ASR\home\svsystems\bin`.
3. Eseguire il comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Il comando precedente restituirà l'elenco di server di elaborazione (possono essere più di uno, in caso di voci duplicate) con numero di serie (S.No), indirizzo IP (IP), nome della macchina virtuale in cui è distribuito il server di elaborazione (Name) ed heartbeat della macchina virtuale (Heartbeat), come illustrato di seguito.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. A questo punto, immettere il numero di serie del server di elaborazione di cui si desidera annullare la registrazione.
6. Questo Ripulisce i dettagli del server di elaborazione dal sistema e verrà visualizzato il messaggio: **È stata annullata la registrazione nome-server > (server-IP-address)**

