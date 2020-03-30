---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361544"
---
### <a name="bastion-tier"></a>Livello Bastione

L'host bastion è un componente facoltativo che è possibile utilizzare come server di collegamento per accedere alle istanze dell'applicazione e del database. Alla macchina virtuale dell'host bastion può essere assegnato un indirizzo IP pubblico, anche se è consigliabile configurare una connessione ExpressRoute o una VPN da sito a sito con la rete locale per un accesso sicuro. Inoltre, solo SSH (porta 22, Linux) o RDP (porta 3389, Windows Server) deve essere aperto per il traffico in ingresso. Per la disponibilità elevata, distribuire un host bastion in due zone di disponibilità o in un singolo set di disponibilità.

È inoltre possibile abilitare l'inoltro dell'agente SSH nelle macchine virtuali, che consente di accedere ad altre macchine virtuali nella rete virtuale inoltrando le credenziali dall'host bastion. In alternativa, usare il tunneling SSH per accedere ad altre istanze.

Di seguito è riportato un esempio di inoltro degli agenti:Here's an example of agent forwarding:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Questo comando si connette al bastione `ssh` e quindi viene eseguito immediatamente di nuovo, in modo da ottenere un terminale nell'istanza di destinazione. Potrebbe essere necessario specificare un utente diverso da root nell'istanza di destinazione se il cluster è configurato in modo diverso. L'argomento `-A` inoltra la connessione dell'agente in modo che la chiave privata nel computer locale venga utilizzata automaticamente. Si noti che l'inoltro `ssh` degli `-A` agenti è una catena, pertanto il secondo comando include anche in modo che tutte le connessioni SSH successive avviate dall'istanza di destinazione utilizzino anche la chiave privata locale.