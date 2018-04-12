---
title: Panoramica delle porte a disponibilità elevata in Azure | Microsoft Docs
description: Informazioni sul bilanciamento del carico con le porte a disponibilità elevata in un servizio di bilanciamento del carico interno.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: f6e9dd09558a3485629d5b70dd8b68b292427b18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="high-availability-ports-overview"></a>Panoramica delle porta a disponibilità elevata

Azure Load Balancer Standard semplifica il bilanciamento del carico di flussi TCP e UDP su tutte le porte simultaneamente, quando si usa un servizio Azure Load Balancer interno. 

Una regola delle porte a disponibilità elevata è una variante di una regola di bilanciamento del carico, configurata in un servizio Azure Load Balancer Standard interno. Per semplificare l'uso di questo servizio, è possibile specificare un'unica regola per il bilanciamento del carico di tutti i flussi TCP e UDP in arrivo su tutte le porte del servizio Azure Load Balancer Standard interno. La decisione di bilanciamento del carico viene presa per ogni flusso in base a queste cinque tuple: indirizzo IP di origine, porta di origine, indirizzo IP di destinazione, porta di destinazione e protocollo.

La funzionalità Porte a disponibilità elevata consente di gestire scenari critici, ad esempio la disponibilità elevata e la scalabilità per appliance di rete virtuale all'interno di reti virtuali. Può essere utile anche quando è necessario eseguire il bilanciamento del carico di un numero elevato di porte. 

La funzionalità Porte a disponibilità elevata viene configurata quando si imposta le porte front-end e back-end su **0**e il protocollo su **Tutti**. La risorsa Load Balancer interna esegue quindi il bilanciamento di tutti i flussi TCP e UDP, indipendentemente dal numero di porta.

## <a name="why-use-ha-ports"></a>Vantaggi dell'uso delle porte a disponibilità elevata

### <a name="nva"></a>Appliance di rete virtuale

È possibile usare appliance di rete virtuale per proteggere il carico di lavoro di Azure da diversi tipi di minacce alla sicurezza. Le appliance di rete virtuale usate in questi scenari devono essere affidabili e a disponibilità elevata, ed essere scalabili orizzontalmente in base alle esigenze.

Per raggiungere questi obiettivi è sufficiente aggiungere istanze di appliance di rete virtuale al pool back-end di Azure Load Balancer interno e configurare una regola di bilanciamento del carico delle porte a disponibilità elevata.

Nell'ambito di scenari a disponibilità elevata di appliance di rete virtuale le porte a disponibilità elevata offrono diversi vantaggi:
- rapidità di failover verso istanze integre con probe di integrità per singola istanza
- Prestazioni più elevate con scalabilità orizzontale fino a *n* istanze attive
- scenari di *n* istanze attive e di istanze attive-passive
- eliminazione della necessità di soluzioni complesse, ad esempio nodi Apache ZooKeeper, per il monitoraggio delle appliance

Il diagramma seguente illustra una distribuzione di rete virtuale hub e spoke. Gli spoke effettuano il tunneling forzato del relativo traffico verso la rete virtuale hub e tramite l'appliance di rete virtuale prima di abbandonare lo spazio attendibile. Le appliance di rete virtuale si trovano dietro a un servizio Load Balancer Standard con una configurazione con porte a disponibilità elevata. Tutto il traffico può essere elaborato e inoltrato di conseguenza.

![Diagramma della rete virtuale hub e spoke con appliance di rete virtuale distribuite in modalità a disponibilità elevata](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se si usano appliance di rete virtuale, verificare con il provider come usare al meglio le porte a disponibilità elevata e quali scenari sono supportati.

### <a name="load-balancing-large-numbers-of-ports"></a>Bilanciamento del carico di un numero elevato di porte

È anche possibile usare porte a disponibilità elevata per applicazioni che richiedono il bilanciamento del carico di un numero elevato di porte. È possibile semplificare questi scenari con un servizio [Azure Load Balancer Standard](load-balancer-standard-overview.md) interno con porte a disponibilità elevata. Un'unica regola di bilanciamento del carico sostituisce più regole di bilanciamento del carico associate alle singole porte.

## <a name="region-availability"></a>Aree di disponibilità

La funzionalità Porte a disponibilità elevata è disponibile in tutte le aree globali di Azure.

## <a name="supported-configurations"></a>Configurazioni supportate

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Singola configurazione con porte a disponibilità elevata con indirizzo IP non mobile (Non-Direct Server Return) nel servizio Azure Load Balancer Standard interno

Questa è una configurazione con porte a disponibilità elevata di base. La configurazione seguente permette di configurare il bilanciamento del carico con porte a disponibilità elevata in un singolo indirizzo IP front-end.
- Durante la configurazione di Azure Load Balancer Standard, selezionare la casella di controllo **Porte a disponibilità elevata** nella configurazione della regola di bilanciamento del carico, 
- assicurandosi che l'opzione **Indirizzo IP mobile** sia impostata su **Disabilitato**.

Questa configurazione non permette altre configurazione di regole di bilanciamento del carico nella risorsa Azure Load Balancer corrente, né altre configurazioni di risorse di bilanciamento del carico interno per il set specifico di istanze back-end.

Tuttavia, è possibile configurare un servizio Azure Load Balancer Standard pubblico per le istanze back-end oltre a questa regola con porte a disponibilità elevata.

### <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Singola configurazione con porte a disponibilità elevata con indirizzo IP mobile (Non-Direct Server Return) nel servizio Azure Load Balancer Standard interno

Analogamente, è possibile configurare il servizio di bilanciamento del carico in modo da usare una regola di bilanciamento del carico con **porte a disponibilità elevata** con un singolo front-end e l'opzione **Indirizzo IP mobile** impostato su **Abilitato**. 

Questa configurazione permette di aggiungere altre regole di bilanciamento del carico con indirizzo IP mobile e/o un servizio Azure Load Balancer pubblico. Tuttavia, non è possibile usare una configurazione di bilanciamento del carico con porte a disponibilità elevata con indirizzo IP non mobile insieme a questa configurazione.

### <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Più configurazioni con porte a disponibilità elevata nel servizio Azure Load Balancer Standard interno

Se lo scenario richiede la configurazione di più front-end con porte a disponibilità elevata per lo stesso pool back-end, a questo scopo è possibile: 
- Configurare più di un indirizzo IP privato front-end per una singola risorsa Azure Load Balancer Standard interna.
- Configurare più regole di bilanciamento del carico, in cui ogni regola ha un unico indirizzo IP front-end selezionato.
- Selezionare l'opzione **Porte a disponibilità elevata** e impostare **Indirizzo IP mobile** su **Abilitato** per tutte le regole di bilanciamento del carico.

### <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Servizio Azure Load Balancer interno con porte a disponibilità elevata e servizio Azure Load Balancer pubblico nelle stesse istanze back-end

È possibile configurare **una** risorsa Azure Load Balancer Standard pubblica per le risorse back-end insieme a un singolo servizio Azure Load Balancer Standard interno con porte a disponibilità elevata.

>[!NOTE]
>Questa funzionalità oggi è disponibile tramite i modelli di Azure Resource Manager, ma non tramite il portale di Azure.

## <a name="limitations"></a>Limitazioni

- La configurazione con porte a disponibilità elevata è disponibile solo per il servizio di bilanciamento del carico interno e non per un servizio Azure Load Balancer pubblico.

- Una combinazione di regole di bilanciamento del carico con porte a disponibilità elevata e porte non a disponibilità elevata non è supportata.

- La funzionalità Porte a disponibilità elevata non è disponibile per IPv6.

- La simmetria di flusso per gli scenari di appliance di rete virtuale è supportata soltanto con una sola scheda di interfaccia di rete. Vedere la descrizione e il diagramma per [gli appliance di rete virtuale](#nva). Tuttavia, se una regola Destination NAT può funzionare per lo scenario, è possibile usarla per assicurarsi che il servizio Azure Load Balancer interno invii il traffico restituito nella stessa appliance virtuale di rete.


## <a name="next-steps"></a>Passaggi successivi

- [Configurare le porte a disponibilità elevata per un servizio Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- Altre informazioni su [Azure Load Balancer Standard](load-balancer-standard-overview.md)
