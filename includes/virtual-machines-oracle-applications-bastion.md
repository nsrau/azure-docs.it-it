---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361544"
---
### <a name="bastion-tier"></a>Livello Bastion

L'host Bastion è un componente facoltativo che è possibile usare come server di salto per accedere alle istanze dell'applicazione e del database. Alla VM dell'host Bastion può essere assegnato un indirizzo IP pubblico, sebbene sia consigliabile configurare una connessione ExpressRoute o una VPN da sito a sito con la rete locale per l'accesso sicuro. Inoltre, è necessario aprire solo SSH (porta 22, Linux) o RDP (porta 3389, Windows Server) per il traffico in ingresso. Per la disponibilità elevata, distribuire un host Bastion in due zone di disponibilità o in un singolo set di disponibilità.

È anche possibile abilitare l'invio dell'agente SSH sulle VM, che consente di accedere ad altre macchine virtuali nella rete virtuale inviando le credenziali dall'host Bastion. In alternativa, usare il tunneling SSH per accedere ad altre istanze.

Di seguito è riportato un esempio di invio dell'agente:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Questo comando si connette al Bastion e quindi viene eseguito `ssh` nuovamente immediatamente, in modo da ottenere un terminale nell'istanza di destinazione. Se il cluster è configurato in modo diverso, potrebbe essere necessario specificare un utente diverso dalla radice nell'istanza di destinazione. L' `-A` argomento Invia la connessione dell'agente in modo che la chiave privata nel computer locale venga utilizzata automaticamente. Si noti che l'invio di agenti è una catena, quindi `ssh` il secondo comando `-A` include anche in modo che le connessioni SSH successive avviate dall'istanza di destinazione usino anche la chiave privata locale.