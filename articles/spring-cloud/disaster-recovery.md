---
title: Ripristino di emergenza del cloud di Azure Spring Documenti Microsoft
description: Scopri come proteggere la tua applicazione Spring Cloud da interruzioni regionali
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279147"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Ripristino di emergenza di Azure Spring CloudAzure Spring Cloud disaster recovery

Questo articolo illustra alcune strategie che è possibile usare per proteggere le applicazioni di Azure Spring Cloud dai tempi di inattività.  Qualsiasi area geografica o data center potrebbe subire tempi di inattività causati da disastri regionali, ma un'attenta pianificazione può ridurre l'impatto sui clienti.

## <a name="plan-your-application-deployment"></a>Pianificare la distribuzione dell'applicazione

Le applicazioni Spring Cloud di Azure vengono eseguite in un'area specifica.  Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure è un'area all'interno di un'area geografica contenente uno o più data center.  Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Azure serializza gli aggiornamenti della piattaforma (manutenzione pianificata) tra coppie regionali, assicurando che venga aggiornata una sola area in ogni coppia alla volta. Se si verifica un'interruzione che interessa più aree, almeno un'area per ogni coppia avrà priorità per il recupero.

Per garantire un'elevata disponibilità e protezione dalle emergenze, è necessario distribuire le applicazioni Spring Cloud in più aree.  Azure fornisce un elenco di [aree associate](../best-practices-availability-paired-regions.md) in modo da poter pianificare le distribuzioni di Spring Cloud in coppie regionali.  È consigliabile considerare tre fattori chiave durante la progettazione dell'architettura del microservizio: disponibilità dell'area, aree associate di Azure e disponibilità del servizio.

*  Disponibilità dell'area geografica: scegliere un'area geografica vicina agli utenti per ridurre al minimo il ritardo di rete e il tempo di trasmissione.
*  Aree abbinate di Azure: scegli le aree abbinate all'interno dell'area geografica scelta per garantire aggiornamenti coordinati della piattaforma e, se necessario, le attività di ripristino con priorità.
*  Disponibilità del servizio: decidere se le aree abbinate devono essere calde/calde, calde/calde o calde/fredde.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

[Gestione traffico](../traffic-manager/traffic-manager-overview.md) di Azure offre il bilanciamento del carico del traffico basato su DNS e può distribuire il traffico di rete tra più aree.  Usare Gestione traffico di Azure per indirizzare i clienti all'istanza del servizio Cloud di Azure più vicina.  Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico delle applicazioni tramite Gestione traffico di Azure prima di inviarlo al servizio Cloud di Azure Spring.For best performance and redundancy, direct all application traffic through Azure Traffic Manager before sending it to your Azure Spring Cloud service.

Se si dispone di applicazioni Cloud di Azure in più aree, usare Gestione traffico di Azure per controllare il flusso di traffico verso le applicazioni in ogni area.  Definire un endpoint di Gestione traffico di Azure per ogni servizio usando l'indirizzo IP del servizio. I clienti devono connettersi a un nome DNS di Azure Traffic Manager che punta al servizio Cloud di Azure Spring.Customers should connect to an Azure Traffic Manager DNS name che punta al servizio Azure Spring Cloud.  Azure Traffic Manager bilancia il traffico tra gli endpoint definiti.  Se un'emergenza colpisce un data center, Gestione traffico di Azure indirizzerà il traffico da tale area alla relativa coppia, garantendo la continuità del servizio.