---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335025"
---
| Risorsa | Limite |
| --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per area. |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area. Per aumentare il limite, contattare il supporto tecnico. |
| Totale core VM Azure spot per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area. Per aumentare il limite, contattare il supporto tecnico. |
| VM per serie, ad esempio dv2 e F, Core per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area. Per aumentare il limite, contattare il supporto tecnico. |
| [Set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per sottoscrizione |2.000 per area. |
| Macchine virtuali per set di disponibilità | 200 |
| Certificati per sottoscrizione |Illimitato<sup>2</sup> |

<sup>1</sup> I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita e pagamento in base al consumo, e per serie, ad esempio dv2, F e G. Ad esempio, il valore predefinito per le sottoscrizioni Enterprise Agreement è 350.

<sup>2</sup> Con Azure Resource Manager, i certificati vengono archiviati nel Azure Key Vault. Il numero di certificati è illimitato per una sottoscrizione. Esiste un limite di 1 MB di certificati per ogni distribuzione, costituito da una singola macchina virtuale o un set di disponibilità.

> [!NOTE]
> I core della macchina virtuale hanno un limite totale per l'area. Hanno anche un limite per le serie locali per ogni dimensione, ad esempio dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione può distribuire 30 VM a1 o 30 VM D1 o una combinazione dei due che non superi un totale di 30 core. Un esempio di combinazione è 10 VM a1 e 20 VM D1.  
> <!-- -->
> 
