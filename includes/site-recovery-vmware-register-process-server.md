---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008480"
---
1. Stabilire una connessione di Desktop remoto al computer che esegue il server di elaborazione. 
2. Eseguire cspsconfigtool.exe per avviare lo strumento di configurazione del server di elaborazione di Azure Site Recovery.
    - Lo strumento viene avviato automaticamente la prima volta che si accede al server di elaborazione.
    - Se non viene aperto automaticamente, fare clic sul relativo collegamento sul desktop.

3. In **Configuration server FQDN or IP** (FQDN o indirizzo IP del server di configurazione) specificare il nome o l'indirizzo IP del server di configurazione con cui registrare il server di elaborazione.
4. In **Configuration Server Port** (Porta del server di configurazione) assicurarsi che sia specificata la porta 443. Si tratta della porta in cui il server di configurazione resta in ascolto di richieste.
5. In **Connection Passphrase** (Passphrase di connessione) inserire la passphrase specificata quando è stato configurato il server di configurazione. Per trovare la passphrase:
    -  Nel server di configurazione passare alla cartella di installazione di Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Eseguire il comando seguente per stampare la passphrase corrente:
    ```
    genpassphrase.exe -n
    ```

6. In **Data Transfer Port** (Porta di trasferimento dei dati) lasciare il valore predefinito a meno che non sia stata specificata una porta personalizzata.

7. Fare clic su **Salva** per salvare le impostazioni e registrare il server di elaborazione.

    
    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
