---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334775"
---
| Risorsa | Limite |
| --- | --- |
| [Macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per servizio cloud<sup>1</sup> |50 |
| Endpoint di input per servizio cloud<sup>2</sup> |150 |

<sup>1</sup> Le macchine virtuali create usando il modello di distribuzione classica anziché Azure Resource Manager vengono automaticamente archiviate in un servizio cloud. È possibile aggiungere più macchine virtuali al servizio cloud per il bilanciamento del carico e disponibilità. 

<sup>2</sup>Endpoint di input consentire le comunicazioni a una macchina virtuale da fuori servizio cloud della macchina virtuale. Le macchine virtuali nello stesso servizio cloud o rete virtuale può comunicare automaticamente tra loro. Per ulteriori informazioni, vedere [come configurare gli endpoint in una macchina virtuale](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
