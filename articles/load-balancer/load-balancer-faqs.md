---
title: Domande frequenti-Azure Load Balancer
description: Risposte alle domande più frequenti sul Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884488"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="what-types-of-load-balancer-exist"></a>Quali sono i tipi di Load Balancer esistenti?
Bilanciamento del carico interno che bilanciano il traffico all'interno di una VNET e di bilanciamento del carico esterno che bilanciano il traffico da e verso un endpoint connesso a Internet. Per ulteriori informazioni, vedere [tipi di Load Balancer]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types)(. 

Per entrambi questi tipi, Azure offre uno SKU di base e uno SKU standard con funzionalità diverse di rilevamento funzionale, prestazioni, sicurezza e integrità. Queste differenze sono illustrate nel [confronto SKU] (https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) articolo.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Come è possibile eseguire l'aggiornamento da una base a una Load Balancer Standard?
Vedere l'articolo relativo all' [aggiornamento da Basic a standard](upgrade-basic-standard.md) per uno script automatizzato e istruzioni sull'aggiornamento di uno SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quali sono le diverse opzioni di bilanciamento del carico in Azure?
Vedere la [Guida alla tecnologia](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) del servizio di bilanciamento del carico per i servizi di bilanciamento del carico disponibili e gli usi consigliati per ognuno di essi.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Dove è possibile trovare Load Balancer modelli ARM?
Vedere l' [elenco dei modelli di avvio rapido Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) per i modelli ARM delle distribuzioni comuni.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>In che modo le regole NAT in ingresso sono diverse dalle regole di bilanciamento del carico?
Le regole NAT vengono usate per specificare una risorsa back-end a cui indirizzare il traffico. Ad esempio, configurazione di una porta di bilanciamento del carico specifica per inviare il traffico RDP a una macchina virtuale specifica. Le regole di bilanciamento del carico vengono usate per specificare un pool di risorse back-end per indirizzare il traffico a, bilanciare il carico in ogni istanza. Una regola di bilanciamento del carico, ad esempio, può instradare i pacchetti TCP sulla porta 80 del servizio di bilanciamento del carico in un pool di server Web.

## <a name="next-steps"></a>Passaggi successivi
Se la domanda non è elencata in precedenza, inviare commenti e suggerimenti su questa pagina. Verrà creato un problema di GitHub per consentire al team del prodotto di garantire la risposta a tutte le domande importanti dei clienti.
