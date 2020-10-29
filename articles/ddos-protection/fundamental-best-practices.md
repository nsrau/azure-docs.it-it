---
title: Procedure consigliate per la protezione DDoS di Azure
description: Informazioni sulle procedure consigliate per la sicurezza con protezione DDoS.
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
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905312"
---
# <a name="fundamental-best-practices"></a>Procedure consigliate fondamentali

Le sezioni seguenti forniscono indicazioni specifiche per la compilazione di servizi resilienti contro gli attacchi DDoS in Azure.

## <a name="design-for-security"></a>Progettare per la sicurezza

Garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità troppo elevata di risorse, provocando un'interruzione del servizio. 

Per proteggere un servizio in esecuzione in Microsoft Azure, i clienti devono avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](/azure/architecture/guide/pillars).
I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

## <a name="design-for-scalability"></a>Progettazione per la scalabilità

La scalabilità è la capacità di un sistema di gestire carichi elevati. progettare le applicazioni con [scalabilità orizzontale](/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.

Per [Servizio app di Azure](/azure/app-service/app-service-value-prop-what-is) selezionare un [piano di servizio app](/azure/app-service/overview-hosting-plans) che offra più istanze. Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](/azure/cloud-services/cloud-services-choose-me). Per [Macchine virtuali di Microsoft Azure](../virtual-machines/index.yml) verificare che l'architettura VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md). È consigliabile usare i [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md) per le funzionalità di scalabilità automatica.

## <a name="defense-in-depth"></a>Difesa avanzata

L'idea che sta dietro alla difesa in profondità consiste nel gestire i rischi con diverse strategie difensive. La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. È consigliabile implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.

Ad esempio, il rischio di attacco aumenta con le dimensioni o la *superficie di attacco* dell'applicazione. È possibile ridurre la superficie di attacco usando un elenco di approvazione per chiudere lo spazio di indirizzi IP esposto e le porte in ascolto che non sono necessarie nei bilanciamenti del carico ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [applicazione Azure gateway](/azure/application-gateway/application-gateway-create-probe-portal)). I [gruppi di sicurezza di rete (NSG)](/azure/virtual-network/security-overview) rappresentano un altro modo per ridurre la superficie di attacco.
È possibile usare [tag di servizio](/azure/virtual-network/security-overview#service-tags) e [gruppi di sicurezza dell'applicazione](/azure/virtual-network/security-overview#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](/azure/virtual-network/virtual-networks-overview), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita. Tramite gli [endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview), il traffico del servizio passerà all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, è consigliabile ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica. È possibile usare la scalabilità e le funzionalità di protezione DDoS avanzate di Azure distribuendo le entità pubbliche note in Azure. Poiché queste entità pubblicamente accessibili sono spesso una destinazione per gli attacchi DDoS, mettendole in Azure si riduce l'impatto sulle risorse locali.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS](manage-ddos-protection.md).