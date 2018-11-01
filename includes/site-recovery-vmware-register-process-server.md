---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164972"
---
* Connettersi alla macchina virtuale del server di elaborazione tramite Connessione Desktop remoto.
* È possibile avviare cspsconfigtool.exe facendo clic sul collegamento disponibile sul desktop. Se è la prima volta che si accede al server di elaborazione, lo strumento viene avviato automaticamente.
  - Nome completo (FQDN) o indirizzo IP del server di configurazione.
  - Porta su cui il server di configurazione è in ascolto. Il valore dovrebbe essere 443.
  - Passphrase di connessione al server di configurazione.
  - Porta di trasferimento dati da configurare per questo server di elaborazione. Lasciare invariato il valore predefinito, a meno che non sia stato specificato un numero di porta diverso nell'ambiente corrente.

    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Fare clic su Salva per salvare la configurazione e registrare il server di elaborazione.
