---
title: Informazioni su Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Panoramica di funzionalità, architettura e implementazione di Azure Load Balancer. Informazioni sul funzionamento di Load Balancer e su come usarlo nel cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 1bc18788019c3ec97e06e3b01e823a0ba53541b8
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734757"
---
# <a name="what-is-azure-load-balancer"></a>Informazioni su Azure Load Balancer

Il *bilanciamento del carico* si riferisce alla distribuzione uniforme del carico o del traffico di rete in ingresso tra un gruppo di risorse o server back-end. 

Azure Load Balancer funziona a livello 4 del modello OSI (Open Systems Interconnection). Rappresenta l'unico punto di contatto per i client. Load Balancer distribuisce i flussi in ingresso che arrivano nel relativo front-end alle istanze del pool back-end. Questi flussi sono conformi alle regole di bilanciamento del carico e ai probe di integrità configurati. Le istanze del pool back-end possono essere macchine virtuali o istanze di Azure in un set di scalabilità di macchine virtuali.

Un **[servizio Load Balancer pubblico](./components.md#frontend-ip-configurations)** può offrire connettività in uscita per le macchine virtuali all'interno della rete virtuale. Queste connessioni vengono stabilite convertendo i relativi indirizzi IP privati in indirizzi IP pubblici. I servizi Load Balancer pubblici vengono usati per bilanciare il carico del traffico Internet inviato alle VM.

Un **[servizio Load Balancer interno (o privato)](./components.md#frontend-ip-configurations)** viene usato se gli indirizzi IP privati sono necessari solo sul front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È possibile accedere al front-end di Load Balancer da una rete locale in uno scenario ibrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Figura: bilanciamento del carico di applicazioni multilivello tramite il servizio Load Balancer sia pubblico che interno*

Per altre informazioni sui singoli componenti del servizio Load Balancer, vedere [Componenti di Azure Load Balancer](./components.md).

## <a name="why-use-azure-load-balancer"></a>Perché usare Azure Load Balancer?
Load Balancer Standard consente di ridimensionare le applicazioni e di creare servizi a disponibilità elevata. Load Balancer supporta scenari in ingresso e in uscita. Load Balancer offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

I principali scenari in cui è possibile usare Load Balancer Standard includono:

- Bilanciare il carico del traffico **[interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** ed **[esterno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** inviato alle macchine virtuali di Azure.

- Aumentare la disponibilità distribuendo le risorse **[all'interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** e **[tra](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zone.

- Configurare la **[connettività in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** per le macchine virtuali di Azure.

- Usare **[probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** per monitorare le risorse con carico bilanciato.

- Adottare il **[port forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** per accedere alle macchine virtuali in una rete virtuale tramite indirizzo IP pubblico e porta.

- Abilitare il supporto per il **[bilanciamento del carico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** di **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Load Balancer Standard fornisce le metriche multidimensionali tramite [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).  Queste metriche possono essere filtrate, raggruppate e suddivise in una determinata dimensione.  Forniscono informazioni dettagliate sulle prestazioni presenti e passate e sullo stato di integrità del servizio.  Integrità risorse di Azure è supportato. Per altre informazioni, vedere **[Diagnostica di Load Balancer Standard con metriche, avvisi e integrità delle risorse](load-balancer-standard-diagnostics.md)** .

- Bilanciare il carico dei servizi in **[più porte, più indirizzi IP o entrambi](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Spostare le risorse di Load Balancer **[interne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** ed **[esterne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** tra le aree di Azure.

- Bilanciare il carico del flusso TCP e UDP in tutte le porte contemporaneamente usando **[porte a disponibilità elevata](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Protezione per impostazione predefinita

Alla base di Load Balancer Standard c'è il modello di sicurezza di rete Zero Trust. Load Balancer Standard è sicuro per impostazione predefinita ed è parte della rete virtuale. La rete virtuale è una rete privata e isolata.  Questo significa che le istanze di Load Balancer Standard e gli indirizzi IP pubblici standard sono chiusi ai flussi in ingresso, a meno che non vengano aperti da gruppi di sicurezza di rete. I gruppi di sicurezza di rete vengono usati per permettere esplicitamente il traffico consentito.  Se non si ha un gruppo di sicurezza di rete su una subnet o sulla scheda di rete della risorsa macchina virtuale, non è consentito al traffico di raggiungere questa risorsa. Per altre informazioni sui gruppi di sicurezza di rete e su come applicarli allo scenario, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
Load Balancer Basic è aperto a Internet per impostazione predefinita.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

Per informazioni sui prezzi di Load Balancer Standard, vedere [Prezzi di Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
Load Balancer Basic è disponibile senza alcun addebito.
Vedere [Contratto di servizio per Load Balancer](https://aka.ms/lbsla). Load Balancer Basic non ha un contratto di servizio.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Aggiornare Load Balancer Basic](upgrade-basic-standard.md) per aggiornare Load Balancer Basic a Load Balancer Standard.

Per iniziare a usare il servizio Load Balancer, vedere [Creare un servizio Load Balancer Standard pubblico](quickstart-load-balancer-standard-public-portal.md).

Per altre informazioni su componenti e limiti di Azure Load Balancer, vedere [Componenti di Azure Load Balancer](./components.md) e [Concetti di Azure Load Balancer](./concepts.md).

Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).
