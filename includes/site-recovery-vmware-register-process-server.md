---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925582"
---
1. Stabilire una connessione Desktop remoto nel computer che esegue il server di elaborazione. 
2. Eseguire cspsconfigtool.exe per avviare lo strumento di configurazione del Server di elaborazione di Azure Site Recovery.
    - Lo strumento viene avviato automaticamente la prima volta che si accede al server di elaborazione.
    - Se non viene aperto automaticamente, fare clic sul relativo collegamento sul desktop.

3. Nelle **server di configurazione IP o FQDN**, specificare il nome o indirizzo IP del server di configurazione con cui registrare il server di elaborazione.
4. Nelle **porta Server di configurazione**, verificare che sia specificato 443. Questa è la porta su cui è in ascolto il server di configurazione per le richieste.
5. Nelle **Passphrase di connessione**, specificare la passphrase specificata quando si configura il server di configurazione. Per trovare la passphrase:
    -  Nel server di configurazione, passare alla cartella di installazione di Site Recovery **\home\svssystems\bin\**. 
    - Eseguire questo comando: **genpassphrase.exe.n**. Vengono mostrati il percorso della passphrase, che può quindi prendere nota.

6. Nelle **porta per il trasferimento dati**, lasciare il valore predefinito a meno che non è stata specificata una porta personalizzata.

7. Fare clic su **salvare** salvare le impostazioni e registrare il server di elaborazione.

    
    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
