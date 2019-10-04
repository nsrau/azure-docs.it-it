---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534646"
---
1. Stabilire una connessione Desktop remoto nel computer che esegue il server di elaborazione. 
2. Eseguire cspsconfigtool.exe per avviare lo strumento di configurazione del Server di elaborazione di Azure Site Recovery.
    - Lo strumento viene avviato automaticamente la prima volta che si accede al server di elaborazione.
    - Se non viene aperto automaticamente, fare clic sul relativo collegamento sul desktop.

3. Nelle **server di configurazione IP o FQDN**, specificare il nome o indirizzo IP del server di configurazione con cui registrare il server di elaborazione.
4. Nelle **porta Server di configurazione**, verificare che sia specificato 443. Questa è la porta su cui è in ascolto il server di configurazione per le richieste.
5. Nelle **Passphrase di connessione**, specificare la passphrase specificata quando si configura il server di configurazione. Per trovare la passphrase:
    -  Nel server di configurazione, passare alla cartella di installazione di Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Eseguire il comando seguente per stampare la passphrase corrente:
    ```
    genpassphrase.exe -n
    ```

6. Nelle **porta per il trasferimento dati**, lasciare il valore predefinito a meno che non è stata specificata una porta personalizzata.

7. Fare clic su **salvare** salvare le impostazioni e registrare il server di elaborazione.

    
    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
