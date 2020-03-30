---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335025"
---
| Risorsa | Limite |
| --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per regione. |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regione. Contattare il supporto tecnico per aumentare il limite. |
| Totali core della macchina virtuale Azure Spot per [sottoscrizioneAzure](../articles/billing-buy-sign-up-azure-subscription.md) Spot VM total cores per subscription |20<sup>1</sup> per regione. Contattare il supporto tecnico per aumentare il limite. |
| VM per serie, ad esempio Dv2 e F, core per [sottoscrizioneVM](../articles/billing-buy-sign-up-azure-subscription.md) per series, such as Dv2 and F, cores per subscription |20<sup>1</sup> per regione. Contattare il supporto tecnico per aumentare il limite. |
| Set di disponibilità per [sottoscrizioneAvailability sets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per subscription |2.000 per regione. |
| Macchine virtuali per set di disponibilità | 200 |
| Certificati per sottoscrizione |Illimitato<sup>2</sup> |

<sup>1 : il</sup> nome del I limiti predefiniti variano in base al tipo di categoria dell'offerta, ad esempio Prova gratuita e Pagamento in base al costo e alla serie, ad esempio Dv2, F e G. Ad esempio, l'impostazione predefinita per le sottoscrizioni con contratto Enterprise Agreement è 350.

<sup>2 Il</sup> nome del sistema Con Azure Resource Manager i certificati vengono archiviati nell'insieme di credenziali delle chiavi di Azure.With Azure Resource Manager, certificates are stored in the Azure Key Vault. Il numero di certificati è illimitato per una sottoscrizione. Esiste un limite di 1 MB di certificati per distribuzione, costituito da una singola macchina virtuale o da un set di disponibilità.

> [!NOTE]
> I core di macchina virtuale hanno un limite totale regionale. Hanno anche un limite per le serie regionali per dimensione, come Dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione può distribuire 30 macchine virtuali A1 o 30 macchine virtuali D1 o una combinazione delle due per non superare un totale di 30 core. Un esempio di combinazione è 10 macchine virtuali A1 e 20 macchine virtuali D1.An example of a combination is 10 A1 VMs and 20 D1 VMs.  
> <!-- -->
> 
