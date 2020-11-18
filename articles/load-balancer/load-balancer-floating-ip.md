---
title: Azure Load Balancer configurazione IP mobile
description: Panoramica di Azure Load Balancer IP mobile
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699407"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer configurazione IP mobile

Load Balancer offre numerose funzionalità per le applicazioni UDP e TCP.

## <a name="floating-ip"></a>IP mobile

Alcuni scenari di applicazione preferiscono o richiedono l'uso della stessa porta da parte di più istanze dell'applicazione in una singola macchina virtuale nel pool back-end. Esempi comuni di riutilizzo delle porte includono il clustering per la disponibilità elevata, le appliance virtuali di rete e l'esposizione di più endpoint TLS senza rieseguire la crittografia di rete. Per riutilizzare la porta di back-end tra più regole, è necessario abilitare l'indirizzo IP mobile nella definizione della regola.

Il termine **IP mobile** appartiene alla terminologia di Azure e fa riferimento a una parte della cosiddetta configurazione Direct Server Return (DSR). La configurazione DSR è costituita da due parti:

- Topologia di flussi
- Schema di mapping degli indirizzi IP

A livello di piattaforma, Azure Load Balancer viene sempre eseguito in una topologia di flussi DSR indipendentemente dal fatto che l'indirizzo IP mobile sia abilitato o meno. Ciò significa che la parte in uscita di un flusso viene sempre correttamente riscritta in un flusso direttamente nell'origine.
Senza IP mobile, per semplicità Azure espone uno schema di mappatura degli indirizzi IP tradizionale per il servizio di bilanciamento del carico (IP delle istanze di macchine virtuali). L'abilitazione dell'IP mobile modifica il mapping degli indirizzi IP con l'IP front-end del servizio di bilanciamento del carico per offrire una maggiore flessibilità. Fare clic [qui](load-balancer-multivip-overview.md) per altre informazioni.

## <a name="limitations"></a><a name = "limitations"></a>Limitazioni

- L'IP mobile non è attualmente supportato nelle configurazioni IP secondarie per gli scenari di bilanciamento del carico

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare il servizio Load Balancer, vedere [Creare un servizio Load Balancer Standard pubblico](quickstart-load-balancer-standard-public-portal.md).
- Informazioni sulle [Connessioni in uscita di Azure Load Balancer](load-balancer-outbound-connections.md).
- Altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
- Informazioni sui [probe di integrità](load-balancer-custom-probe-overview.md).
- Altre informazioni sulla diagnostica per [Azure Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).