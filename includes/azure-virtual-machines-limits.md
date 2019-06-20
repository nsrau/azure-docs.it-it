---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180505"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| [Macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per servizio cloud<sup>1</sup> |50 |50 |
| Endpoint di input per servizio cloud<sup>2</sup> |150 |150 |

<sup>1</sup>le macchine virtuali create usando il modello di distribuzione classico invece di Azure Resource Manager vengono automaticamente archiviate in un servizio cloud. È possibile aggiungere più macchine virtuali al servizio cloud per il bilanciamento del carico e disponibilità. 

<sup>2</sup>Endpoint di input consentire le comunicazioni a una macchina virtuale da fuori servizio cloud della macchina virtuale. Le macchine virtuali nello stesso servizio cloud o rete virtuale può comunicare automaticamente tra loro. Per altre informazioni, vedere [Come configurare gli endpoint in una macchina virtuale](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
