---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67180675"
---
Attenersi alla procedura per le circostanze specifiche.

### <a name="unregister-a-connected-process-server"></a>Annulla la registrazione di un server di elaborazione connesso

1. Stabilire una connessione remota al server di elaborazione come amministratore.
2. Nel **Pannello di controllo**aprire **programmi > disinstallare un programma**.
3. Disinstallare il programma **Microsoft Azure Site Recovery servizio Mobility/Server di destinazione master**.
4. Disinstallare il programma **Microsoft Azure Site Recovery configurazione/server di elaborazione**.
5. Dopo la disinstallazione dei programmi nei passaggi 3 e 4, disinstallare **Microsoft Azure Site Recovery le dipendenze del server di configurazione/elaborazione**.

### <a name="unregister-a-disconnected-process-server"></a>Annulla la registrazione di un server di elaborazione disconnesso

Usare questa procedura solo se non è possibile ripristinare il computer in cui è installato il server di elaborazione.

1. Accedere al server di configurazione come amministratore.
2. Aprire un prompt dei comandi amministrativo e passare a `%ProgramData%\ASR\home\svsystems\bin` .
3. Eseguire questo comando per ottenere un elenco di uno o più server di elaborazione.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. No: numero di serie del server di elaborazione.
    - IP/nome: indirizzo IP e nome del computer che esegue il server di elaborazione.
    - Heartbeat: ultimo heartbeat dal computer del server di elaborazione.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Specificare il numero di serie del server di elaborazione di cui si desidera annullare la registrazione.
5. L'annullamento della registrazione di un server di elaborazione determina la rimozione di tutti i relativi dettagli dal sistema e viene visualizzato il messaggio: Impossibile **annullare la registrazione del nome server> (Server-IP-address)**

