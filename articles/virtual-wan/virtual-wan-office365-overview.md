---
title: Piano di controllo di Office 365 nella rete WAN virtuale di Azure
description: Informazioni sul piano di controllo di Office 365 nella rete WAN virtuale.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: cb91c1364a91c101ecf8362acd7aab01440143fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685273"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Piano di controllo di Office 365 nella rete WAN virtuale

I clienti della rete WAN virtuale con dispositivi SDWAN specifici possono configurare i criteri di Internet breakout di O365 per il traffico attendibile nel portale di Azure. In questo modo:
- Il traffico di Office 365 entra nella rete Microsoft vicina all'utente, offrendo un'esperienza utente ottimale.
- Il backhauling e l'hairpinning del traffico vengono evitati, consentendo così di risparmiare i costi della WAN.
- I risultati sono commisurati ai principi di connettività di O365.

## <a name="faqs"></a>Domande frequenti
### <a name="what-is-the-customer-benefit"></a>Qual è il vantaggio per i clienti?
Usando questa funzionalità nella rete WAN virtuale, i clienti possono ora specificare le categorie di traffico di Office 365 che ritengono attendibili per un Internet breakout diretto. Questo traffico attendibile di O365 ignorerà i proxy ed effettuerà il routing direttamente dalla posizione dell'utente al POP di Microsoft più vicino. Ciò consente di evitare il backhauling e l'hairpinning del traffico, offrendo pertanto un'esperienza utente ottimale e permettendo di risparmiare i costi della WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Quali sono le categorie di traffico di Office 365?
Gli endpoint di Office 365 rappresentano le subnet e gli indirizzi di rete. Gli endpoint possono essere URL, indirizzi IP, o intervalli IP. Gli URL possono essere un FQDN, ad esempio *account.office.net*, o un URL con caratteri jolly, ad esempio * *. office365.com*. Gli endpoint vengono separati in tre categorie: **Optimize**, **Allow** e **Default**, in base alla relativa criticità. Altre informazioni sulle categorie di endpoint sono riportate [qui](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Quale categoria di traffico di Office 365 è consigliata da Microsoft per l'Internet breakout diretto?
La categoria **Optimize** corrisponde agli endpoint di rete più critici ed è necessaria per il bypass di dispositivi di interruzione e ispezione SSL e di altri dispositivi di sicurezza di rete. Deve disporre di traffico in uscita da Internet diretto vicino agli utenti. Questi endpoint rappresentano gli scenari di Office 365 più sensibili alle prestazioni, alla latenza e alla disponibilità di rete. Questa categoria include un piccolo set di URL chiave (dell'ordine di ~10) e un set definito di subnet IP dedicate ai carichi di lavoro di base di Office 365 come Exchange Online, SharePoint Online, Skype for Business Online e Microsoft Teams. 

La categoria **Allow** è consigliata anche per il traffico in uscita da Internet diretto. Consentire il traffico di rete può tuttavia tollerare un'eventuale latenza di rete. Gli endpoint nelle categorie Optimize e Allow sono tutti ospitati nei data center Microsoft e gestiti come parte di Office 365. La categoria Default può essere indirizzata ad una posizione predefinita del traffico uscita da Internet e non richiede il traffico in uscita da Internet diretto o il bypass di dispositivi di interruzione e ispezione SSL.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Impostazione di criteri utente O365 tramite la rete WAN virtuale
È possibile abilitare i criteri tramite la scheda **Rete WAN virtuale** -> **Impostazioni** -> **Configurazione**. Qui è possibile specificare le categorie preferite del traffico di O365 per l'Internet breakout diretto.

![Configurare il piano di controllo di Office 365 nella rete WAN virtuale](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Come funziona?

1.  Il traffico di O365 entra nella rete Microsoft vicina all'utente, offrendo un'esperienza utente ottimale.
2.  I criteri di routing vengono usati da SDWAN. Ignora quindi i proxy di sicurezza per le categorie attendibili ed esegue il breakout locale diretto per queste categorie.
3.  Il backhauling e l'hairpinning del traffico vengono evitati, consentendo così di risparmiare i costi della WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Quali dispositivi dei partner supportano questi criteri tramite la rete WAN virtuale?
Citrix supporta attualmente questi criteri tramite la rete WAN virtuale.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Cosa succede alle categorie restanti di traffico (non attendibile) di O365?
Il traffico di O365 restante seguirà il percorso predefinito del traffico Internet dei clienti.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Cosa succede se ho già specificato i miei criteri di O365 tramite il mio provider SDWAN?
Se si specificano i criteri tramite SDWAN UX e la rete WAN virtuale di Azure, i criteri impostati nella rete WAN virtuale avranno la precedenza.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [rete WAN virtuale](virtual-wan-about.md).