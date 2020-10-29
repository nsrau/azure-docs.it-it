---
title: Tipi di attacchi mitiga la protezione DDoS standard di Azure
description: Informazioni sui tipi di attacchi protetti da protezione DDoS standard di Azure.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 50dd5cf9a51ebcc6b2df188838ef2910afcd224d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905505"
---
# <a name="types-of-ddos-attacks-overview"></a>Panoramica dei tipi di attacchi DDoS

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

- **Attacchi volumetrici** : questi attacchi inondano il livello di rete con una notevole quantità di traffico apparentemente legittimo. Sono inclusi i flood UDP, le inondazioni di amplificazione e altri Flood con pacchetti falsificati. Protezione DDoS standard attenua questi potenziali attacchi a più gigabyte grazie all'assorbimento e alla pulitura, con la scalabilità di rete globale di Azure, in modo automatico.
- **Attacchi ai protocolli** : questi attacchi rendono inaccessibile una destinazione sfruttando una vulnerabilità nello stack di protocolli di livello 3 e di livello 4. Sono inclusi gli attacchi SYN flood, gli attacchi di reflection e altri attacchi del protocollo. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso. 
- **Attacchi a livello di risorsa (applicazione)** : questi attacchi colpiscono i pacchetti di applicazioni Web per interrompere la trasmissione dei dati tra host. Includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare un Web Application Firewall, ad esempio il [gateway applicazione](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure Web Application Firewall, oltre a protezione DDoS standard per offrire una difesa contro tali attacchi. Sono disponibili anche offerte di terze parti di web application firewall nel [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Protezione DDoS di Azure Standard

Protezione DDoS Standard protegge le risorse in una rete virtuale, inclusi gli indirizzi IP pubblici associati alle macchine virtuali, i bilanciamenti del carico e i gateway applicazione. Una volta abbinato il gateway applicazione web application firewall o una web application firewall di terze parti distribuita in una rete virtuale con un indirizzo IP pubblico, protezione DDoS standard può fornire funzionalità di mitigazione complete di livello 3 a livello 7.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS](manage-ddos-protection.md).