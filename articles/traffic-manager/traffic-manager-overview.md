---
title: "Che cos&quot;è Gestione traffico | Documentazione Microsoft"
description: Questo articolo fornisce informazioni su Gestione traffico e permette di determinare se sia la scelta giusta per il routing del traffico dell&quot;applicazione
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Panoramica di Gestione traffico

Gestione traffico di Microsoft Azure consente di controllare la distribuzione del traffico utente per gli endpoint di servizio in diversi data center. Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e macchine virtuali di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure.

Gestione traffico usa il sistema DNS (Domain Name System) per indirizzare le richieste del client all'endpoint più appropriato in base a un [metodo di routing del traffico](traffic-manager-routing-methods.md) e all'integrità degli endpoint. Gestione traffico offre diversi metodi di routing del traffico per soddisfare le diverse esigenze delle applicazioni. Offre inoltre [monitoraggio](traffic-manager-monitoring.md) dell'integrità degli endpoint e failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

## <a name="traffic-manager-benefits"></a>Vantaggi di Gestione traffico

Con Gestione traffico è possibile:

* **Migliorare la disponibilità delle applicazioni critiche**

    Gestione traffico assicura la disponibilità elevata delle applicazioni attraverso il monitoraggio degli endpoint e il failover automatico in caso di inattività di un endpoint.

* **Migliorare la velocità di risposta delle applicazioni a prestazioni elevate**

    Azure consente di eseguire servizi cloud o siti Web in datacenter di tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni indirizzando il traffico all'endpoint con la minore latenza di rete per il client.

* **Eseguire la manutenzione dei servizi senza tempi di inattività**

    È possibile eseguire le operazioni pianificate di manutenzione delle applicazioni senza tempi di inattività. Durante l'esecuzione delle operazioni di manutenzione, Gestione traffico indirizza il traffico verso endpoint alternativi.

* **Combinare applicazioni locali e applicazioni basate sul cloud**

    Gestione traffico supporta anche endpoint esterni, non di Azure, e può quindi essere usato con distribuzioni cloud ibride e locali, inclusi gli scenari di tipo "burst nel cloud", "migrazione nel cloud" o "failover nel cloud".

* **Distribuire il traffico in distribuzioni complesse e di grandi dimensioni**

    Usando i [profili di Gestione traffico annidati](traffic-manager-nested-profiles.md) è possibile combinare metodi di routing del traffico per creare regole sofisticare e flessibili in grado di supportare le esigenze di distribuzioni più complesse e di maggiori dimensioni.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).
* Informazioni su come sviluppare applicazioni a disponibilità elevata usando il [monitoraggio degli endpoint di Gestione traffico](traffic-manager-monitoring.md).
* Altre informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico.
* [Creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md).



<!--HONumber=Nov16_HO3-->


