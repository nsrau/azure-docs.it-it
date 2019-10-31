---
title: Ripristino di emergenza geografico di Azure Spring cloud | Microsoft Docs
description: Informazioni su come proteggere l'applicazione Spring cloud da interruzioni a livello di area
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jeconnoc
ms.openlocfilehash: 4752cba6081d376bd006406a154b9b182c6a3a72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164965"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Ripristino di emergenza di Azure Spring cloud

Questo articolo illustra alcune strategie che è possibile usare per proteggere le applicazioni cloud Spring di Azure dal tempo di inattività.  Qualsiasi regione o data center può comportare tempi di inattività causati da emergenze a livello di area, ma un'attenta pianificazione può attenuare l'effetto dei clienti.

## <a name="plan-your-application-deployment"></a>Pianificare la distribuzione dell'applicazione

Le applicazioni cloud Spring di Azure vengono eseguite in un'area specifica.  Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure è un'area all'interno di una geografia che contiene uno o più data center.  Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Azure serializza gli aggiornamenti della piattaforma (manutenzione pianificata) tra le coppie di aree, assicurando che solo un'area in ogni coppia venga aggiornata alla volta. Se si verifica un'interruzione che interessa più aree, almeno un'area per ogni coppia avrà priorità per il recupero.

Per garantire la disponibilità elevata e la protezione dalle emergenze, è necessario distribuire le applicazioni Spring cloud in più aree.  Azure fornisce un elenco di [aree abbinate](../best-practices-availability-paired-regions.md) in modo che sia possibile pianificare le distribuzioni di Spring cloud in coppie di aree.  Quando si progetta un'architettura di microservizi, è consigliabile prendere in considerazione tre fattori chiave, ovvero disponibilità dell'area, aree abbinate di Azure e disponibilità dei servizi.

*  Disponibilità area: scegliere un'area geografica vicina agli utenti per ridurre al minimo i tempi di ritardo e di trasmissione della rete.
*  Aree abbinate di Azure: scegliere le aree abbinate all'interno dell'area geografica scelta per garantire gli aggiornamenti della piattaforma coordinata e le attività di ripristino con priorità, se necessario.
*  Disponibilità del servizio: decidere se le aree abbinate devono essere in esecuzione a caldo, ad accesso frequente, a caldo o a caldo o a freddo

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) fornisce il bilanciamento del carico del traffico basato su DNS ed è in grado di distribuire il traffico di rete tra più aree.  Usare gestione traffico di Azure per indirizzare i clienti all'istanza del servizio cloud Spring di Azure più vicina.  Per prestazioni e ridondanza ottimali, indirizzare tutto il traffico delle applicazioni attraverso gestione traffico di Azure prima di inviarlo al servizio cloud Spring di Azure.

Se si hanno applicazioni cloud Spring di Azure in più aree, usare gestione traffico di Azure per controllare il flusso del traffico verso le applicazioni in ogni area.  Definire un endpoint di gestione traffico di Azure per ogni servizio usando l'indirizzo IP del servizio. I clienti devono connettersi a un nome DNS di gestione traffico di Azure che punta al servizio cloud Spring di Azure.  Gestione traffico di Azure bilancia il carico del traffico tra gli endpoint definiti.  Se un'emergenza colpisce un data center, gestione traffico di Azure indirizza il traffico da tale area alla propria coppia, garantendo la continuità del servizio.