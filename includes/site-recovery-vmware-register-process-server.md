---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67534646"
---
1. Stabilire un Connessione Desktop remoto al computer che esegue il server di elaborazione. 
2. Eseguire cspsconfigtool.exe per avviare lo strumento di configurazione del server di elaborazione Azure Site Recovery.
    - Lo strumento viene avviato automaticamente la prima volta che si accede al server di elaborazione.
    - Se non viene aperto automaticamente, fare clic sul relativo collegamento sul desktop.

3. In **FQDN o IP del server di configurazione**specificare il nome o l'indirizzo IP del server di configurazione con cui registrare il server di elaborazione.
4. In **porta server di configurazione**assicurarsi che sia specificato 443. Si tratta della porta su cui il server di configurazione è in ascolto per le richieste.
5. In **passphrase di connessione**specificare la passphrase specificata durante la configurazione del server di configurazione. Per trovare la passphrase:
    -  Nel server di configurazione passare alla cartella di installazione di Site Recovery **\home\svssystems\bin \* *:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Eseguire il comando seguente per stampare la passphrase corrente:
    ```
    genpassphrase.exe -n
    ```

6. In **trasferimento dati porta**lasciare il valore predefinito a meno che non sia stata specificata una porta personalizzata.

7. Fare clic su **Salva** salvare le impostazioni e registrare il server di elaborazione.

    
    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
