---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270695"
---
Le macchine virtuali IaaS e le istanze del ruolo PaaS in una rete virtuale ricevono automaticamente un indirizzo IP privato da un intervallo specificato, in base alla subnet a cui sono connessi. Tale indirizzo viene mantenuto per le macchine virtuali e le istanze del ruolo, fino a quando non vengono rimosse. Disabilitare un'istanza di macchina virtuale o un ruolo interrompendolo da PowerShell, CLI Azure o dal portale di Azure. In questi casi, una volta che l'istanza di macchina virtuale o del ruolo viene riavviata, riceverà un indirizzo IP disponibile dall'infrastruttura di Azure, che potrebbe non essere lo stesso che aveva in precedenza. Se si arresta la macchina virtuale o l’istanza del ruolo dal sistema operativo guest, esso mantiene comunque il suo indirizzo IP.  

In alcuni casi, si desidera che una macchina virtuale o istanza del ruolo disponga di un indirizzo IP statico, ad esempio, se la macchina virtuale eseguirà DNS o sarà un controller di dominio. È possibile farlo impostando un indirizzo IP statico privato.

