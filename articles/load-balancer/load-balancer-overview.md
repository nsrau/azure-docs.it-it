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
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045385"
---
# <a name="what-is-azure-load-balancer"></a>Informazioni su Azure Load Balancer

Il *bilanciamento del carico* si riferisce alla distribuzione uniforme del carico o del traffico di rete in ingresso tra un gruppo di risorse o server back-end. Azure offre un'[ampia gamma di opzioni di bilanciamento del carico](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) che è possibile scegliere in base alle esigenze. Questo documento illustra Azure Load Balancer.

Azure Load Balancer funziona a livello 4 del modello OSI (Open Systems Interconnection). Rappresenta l'unico punto di contatto per i client. Load Balancer distribuisce i nuovi flussi in ingresso che arrivano nel relativo front-end alle istanze del pool back-end. Questi flussi sono conformi alle regole di bilanciamento del carico e ai probe di integrità configurati. Le istanze del pool back-end possono essere macchine virtuali o istanze di Azure in un set di scalabilità di macchine virtuali.


Un **[servizio Load Balancer pubblico](./concepts-limitations.md#publicloadbalancer)** può offrire connettività in uscita per le macchine virtuali all'interno della rete virtuale. Queste connessioni vengono stabilite convertendo i relativi indirizzi IP privati in indirizzi IP pubblici. I servizi Load Balancer pubblici vengono usati per bilanciare il carico del traffico Internet inviato alle VM.

Un **[servizio Load Balancer interno (o privato)](./concepts-limitations.md#internalloadbalancer)** viene usato se gli indirizzi IP privati sono necessari solo sul front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È possibile accedere al front-end di Load Balancer da una rete locale in uno scenario ibrido.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Figura: bilanciamento del carico di applicazioni multilivello tramite il servizio Load Balancer sia pubblico che interno*

Per altre informazioni sui singoli componenti del servizio Load Balancer, vedere [Componenti e limiti di Azure Load Balancer](./concepts-limitations.md)

>[!NOTE]
> Microsoft consiglia di usare [Load Balancer Standard](./load-balancer-standard-overview.md).
Le macchine virtuali autonome, i set di disponibilità e i set di scalabilità di macchine virtuali possono essere collegati solo a uno SKU, non a entrambi. Se usati con gli indirizzi IP pubblici, lo SKU di Load Balancer e quello dell'indirizzo IP pubblico devono corrispondere. Gli SKU di Load Balancer e quelli degli indirizzi IP pubblici non sono modificabili.

## <a name="why-use-azure-load-balancer"></a>Perché usare Azure Load Balancer?
Azure Load Balancer consente di ridimensionare le applicazioni e di creare servizi a disponibilità elevata. Load Balancer supporta scenari in ingresso e in uscita. Load Balancer offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

I principali scenari in cui è possibile usare Azure Load Balancer includono:

- Bilanciare il carico del traffico **[interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** ed **[esterno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** inviato alle macchine virtuali di Azure.

- Aumentare la disponibilità distribuendo le risorse **[all'interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** e **[tra](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zone.

- Configurare la **[connettività in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** per le macchine virtuali di Azure.

- Usare **[probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** per monitorare le risorse con carico bilanciato.

- Adottare il **[port forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** per accedere alle macchine virtuali in una rete virtuale tramite indirizzo IP pubblico e porta.

- Abilitare il supporto per il **[bilanciamento del carico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** di **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Sfruttare i vantaggi di **[metriche e diagnostica ](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** per Azure Load Balancer con **[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Bilanciare il carico dei servizi in **[più porte, più indirizzi IP o entrambi](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Spostare le risorse di Load Balancer **[interne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** ed **[esterne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** tra le aree di Azure.

- Bilanciare il carico del flusso TCP e UDP in tutte le porte contemporaneamente usando **[porte a disponibilità elevata](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Prezzi

Il costo dell'utilizzo del servizio Load Balancer Standard viene addebitato in base ai criteri seguenti.

* Numero di regole in uscita e di bilanciamento del carico configurate. Le regole NAT in ingresso non sono conteggiate ai fini del numero totale di regole.
* Quantità di dati elaborati in ingresso e in uscita indipendenti dalle regole.

Per informazioni sui prezzi di Load Balancer Standard, vedere [Prezzi di Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer Basic è disponibile senza alcun addebito.

## <a name="sla"></a>Contratto di servizio

Per informazioni sul contratto di servizio di Load Balancer Standard, vedere [Contratto di servizio per Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare il servizio Load Balancer, vedere [Creare un servizio Load Balancer Standard pubblico](quickstart-load-balancer-standard-public-portal.md).

Per altre informazioni su componenti e limiti di Azure Load Balancer, vedere [Componenti e limiti di Azure Load Balancer](./concepts-limitations.md)
