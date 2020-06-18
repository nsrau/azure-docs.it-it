---
title: Ripristino di emergenza geografico per Azure Spring Cloud | Microsoft Docs
description: Informazioni su come proteggere l'applicazione Spring Cloud da interruzioni a livello di area
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 81ca6b2c365b0dd8a249a337f51d78516cb2cc61
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657187"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Ripristino di emergenza per Azure Spring Cloud

Questo articolo illustra alcune strategie utilizzabili per proteggere le applicazioni Azure Spring Cloud da periodi di inattività.  Qualsiasi area o data center può subire interruzioni dell'attività causati da emergenze a livello di area, ma un'attenta pianificazione può attenuarne l'impatto sui clienti.

## <a name="plan-your-application-deployment"></a>Pianificare la distribuzione delle applicazioni

Le applicazioni Azure Spring Cloud vengono eseguite in un'area specifica.  Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure all'interno di un'area geografica include uno o più data center.  Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Azure serializza gli aggiornamenti della piattaforma (manutenzione pianificata) tra le coppie di aree, assicurando che solo un'area per ogni coppia venga aggiornata una sola area alla volta. Se si verifica un'interruzione che interessa più aree, almeno un'area per ogni coppia avrà priorità per il recupero.

Per garantire la disponibilità elevata e la protezione dalle emergenze, è necessario distribuire le applicazioni Spring Cloud in più aree.  Azure fornisce un elenco di [aree associate](../best-practices-availability-paired-regions.md) per consentire la pianificazione delle distribuzioni di Spring Cloud nelle coppie di aree.  Quando si progetta un'architettura di microservizi, è consigliabile prendere in considerazione tre fattori chiave: disponibilità a livello di area, aree associate di Azure e disponibilità del servizio.

*  Disponibilità a livello di area:  scegliere un'area geografica vicina agli utenti per ridurre al minimo i tempi relativi al ritardo e alla trasmissione della rete.
*  Aree associate di Azure:  scegliere aree abbinate all'interno dell'area geografica scelta per garantire il coordinamento degli aggiornamenti della piattaforma e l'assegnazione di priorità alle attività di ripristino, se necessarie.
*  Disponibilità del servizio:   decidere se l'esecuzione delle aree associate deve essere ad accesso molto frequente/molto frequente, ad accesso molto frequente/frequente, ad accesso molto frequente/sporadico.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) offre funzioni di bilanciamento del carico del traffico basato su DNS ed è in grado di distribuire il traffico di rete su più aree.  Usare gestione traffico di Azure per indirizzare i clienti all'istanza del servizio Azure Spring Cloud più vicina.  Per prestazioni e ridondanza ottimali, indirizzare tutto il traffico delle applicazioni attraverso Gestione traffico di Azure prima di inviarlo al servizio Azure Spring Cloud.

Se si hanno applicazioni Azure Spring Cloud in più aree, usare Gestione traffico di Azure per controllare il flusso del traffico verso le applicazioni in ogni area.  Definire un endpoint di Gestione traffico di Azure per ogni servizio usando l'IP del servizio. I clienti devono connettersi a un nome DNS di Gestione traffico di Azure che punta al servizio Azure Spring Cloud.  Gestione traffico di Azure esegue il bilanciamento del carico del traffico tra gli endpoint definiti.  Se un'emergenza colpisce un data center, Gestione traffico di Azure indirizza il traffico dall'area interessata all'area associata, garantendo la continuità del servizio.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Creare Gestione traffico di Azure per Azure Spring Cloud

1. Creare Azure Spring Cloud in due aree diverse.
Sono necessarie due istanze del servizio di Azure Spring Cloud distribuite in due aree diverse (Stati Uniti orientali ed Europa occidentale). Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure per creare due istanze del servizio. Ognuna verrà usata come endpoint primario e di failover per il traffico. 

**Informazioni delle due istanze del servizio:**

| Nome servizio | Location | Applicazione |
|--|--|--|
| service-sample-a | Stati Uniti orientali | gateway/auth-service/account-service |
| service-sample-b | Europa occidentale | gateway/auth-service/account-service |

2. Configurare il dominio personalizzato per il servizio. Seguire il [documento sul dominio personalizzato](spring-cloud-tutorial-custom-domain.md) per configurare il dominio personalizzato per queste due istanze del servizio esistenti. Al termine della configurazione, entrambe le istanze del servizio vengono associate a un dominio personalizzato: bcdr-test.contoso.com

3. Creare una gestione traffico e due endpoint: [Creare un profilo di Gestione traffico usando il portale di Azure](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile).

Ecco il profilo di Gestione traffico:
* Nome DNS di Gestione traffico: `http://asc-bcdr.trafficmanager.net`
* Profili endpoint: 

| Profilo | Type | Destinazione | Priorità | Impostazioni intestazione personalizzata |
|--|--|--|--|--|
| Profilo endpoint A | Endpoint esterno | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| Profilo endpoint B | Endpoint esterno | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. Creare un record CNAME nella zona DNS: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. L'ambiente è ora completamente configurato. I clienti dovrebbero essere in grado di accedere all'app tramite: bcdr-test.contoso.com