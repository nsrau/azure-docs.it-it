---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361391"
---
Seguire la procedura relativa alle circostanze specifiche.

### <a name="unregister-a-connected-process-server"></a>Annullare la registrazione di un server di elaborazione connesso

1. Stabilire una connessione remota al server di elaborazione come amministratore.
2. In **Pannello di controllo** aprire **Programmi > Disinstalla un programma**.
3. Disinstallare il programma **Servizio Mobility/server di destinazione master di Microsoft Azure Site Recovery**.
4. Disinstallare il programma **Server di configurazione/elaborazione di Microsoft Azure Site Recovery**.
5. Dopo aver disinstallato i programmi ai passaggi 3 e 4, disinstallare le **dipendenze del server di configurazione/elaborazione di Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Annullare la registrazione di un server di elaborazione disconnesso

Usare questa procedura solo se non è possibile in alcun modo ripristinare il computer in cui è installato il server di elaborazione.

1. Accedere al server di configurazione come amministratore.
2. Aprire un prompt dei comandi amministrativo e passare a `%ProgramData%\ASR\home\svsystems\bin`.
3. Eseguire questo comando per ottenere un elenco di uno o più server di elaborazione.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - N. serie: numero di serie del server di elaborazione.
    - IP/Nome: indirizzo IP e nome del computer che esegue il server di elaborazione.
    - Heartbeat: ultimo heartbeat dal server di elaborazione.
    ![Screenshot che mostra informazioni in testo non crittografato sui server di elaborazione e la richiesta di selezionare uno dei server elencati per annullarne la registrazione.(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Specificare il numero di serie del server di elaborazione di cui si vuole annullare la registrazione.
5. Annullando la registrazione di un server di elaborazione vengono rimossi tutti i relativi dettagli dal sistema e viene visualizzato il messaggio: **Annullamento registrazione nome-server> (indirizzo-IP-server) completato**

