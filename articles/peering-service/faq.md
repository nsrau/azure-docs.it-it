---
title: Domande frequenti sul servizio peering di Azure
description: Informazioni sulle domande frequenti sul servizio peering di Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871361"
---
# <a name="peering-service-faq"></a>Domande frequenti sul servizio peering

Questo articolo illustra le domande più frequenti sulle connessioni al servizio peering di Azure.


**D. Chi sono i clienti di destinazione?**

R. I clienti di destinazione sono imprese che si connettono a Microsoft Cloud tramite Internet come trasporto.

**D. I clienti possono iscriversi a un servizio di peering con più provider?** 

R. Sì, i clienti possono iscriversi al servizio di peering con più provider nella stessa area o in aree diverse, ma non per lo stesso prefisso.

**D. I clienti possono selezionare un ISP univoco per i propri siti per area geografica?**

R. Sì, i clienti possono farlo. Selezionare l'ISP partner per area che soddisfi le esigenze aziendali e operative.

**D. Che cos'è un PoP Microsoft Edge?**

R. Si tratta di una posizione fisica in cui Microsoft si interconnette con altre reti. Nella località PoP Microsoft Edge sono ospitati servizi come la porta di ingresso di Azure e la rete CDN di Azure. Per altre informazioni, consultare [Rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-features).

## <a name="peering-service-unique-characteristics"></a>Servizio di peering: caratteristiche univoche

**D. Come è diverso il servizio di peering rispetto al normale accesso a Internet?**

R. I partner che hanno effettuato la registrazione con il servizio di peering Microsoft collaborano con Microsoft per offrire routing ottimizzato e connettività affidabile ai servizi Microsoft.  

**D. In che modo il servizio di peering è diverso da ExpressRoute?**

R. Azure ExpressRoute è una connessione privata e dedicata da una o più sedi dei clienti. Sebbene il servizio di peering offra una connettività pubblica ottimizzata e non supporta la connettività privata, offre anche una connettività ottimizzata per gli breakout Internet locali.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul servizio di peering, vedere [Panoramica del servizio di peering](about.md).
- Per trovare un provider di servizi, vedere [peering Service Partners and locations](location-partners.md).
- Per caricare una connessione al servizio di peering, vedere [onboarding peering Service](onboarding-model.md).
- Per registrare una connessione al servizio di peering, vedere [registrare una connessione al servizio di peering-portale di Azure](azure-portal.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).