---
title: Servizio Frontdoor di Azure - Architettura di routing | Microsoft Docs
description: Questo articolo aiuta a comprendere l'aspetto di visualizzazione globale dell'architettura di Frontdoor.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736284"
---
# <a name="routing-architecture-overview"></a>Panoramica dell'architettura di routing

Quando il servizio Frontdoor di Azure riceve le richieste del client, risponde (se è abilitata la memorizzazione nella cache) oppure le inoltra all’applicazione di back-end più appropriata (come un proxy inverso).

</br>Esistono opportunità di ottimizzare il traffico durante il routing a Frontdoor di Azure così come durante il routing verso i back-end.

## <a name = "anycast"></a>Selezionare l'ambiente di Frontdoor per il routing del traffico (Anycast)

Il routing al Frontdoor di Azure si basa su ambienti [Anycast](https://en.wikipedia.org/wiki/Anycast) per il traffico DNS (Domain Name System) e HTTP (Hypertext Transfer Protocol), pertanto il traffico utente passerà all'ambiente più vicino in termini di topologia di rete (minor numero hop). Questa architettura offre in genere i migliori tempi di round trip per gli utenti finali (massimizzare i vantaggi della suddivisione TCP). Frontdoor organizza gli ambienti in primari e in "anelli" di fallback.  L'anello esterno dispone di ambienti più vicini agli utenti, offrendo latenze più basse.  L'anello interno dispone di ambienti in grado di gestire il failover per l'ambiente di anello esterno nel caso di problemi. L'anello esterno è la destinazione preferita per l'intero traffico. Tuttavia, l'anello interno è necessario per gestire l'overflow del traffico dall'anello esterno. In termini di indirizzi IP virtuali, ogni host front-end, o dominio gestito da Frontdoor è assegnato a un indirizzo IP virtuale primario, annunciato dagli ambienti dell'anello interno ed esterno, nonché a un indirizzo IP virtuale di fallback, annunciato solo dagli ambienti dell'anello interno. 

</br>Questa strategia globale garantisce che le richieste da parte degli utenti finali raggiungano sempre l'ambiente Frontdoor più vicino e che, qualora l'ambiente di ingresso principale preferito non sia integro, il traffico venga spostato automaticamente all'ambiente più vicino.

## <a name = "splittcp"></a>Connessione all'ambiente Frontdoor (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) è una tecnica che serve a ridurre latenza e problemi TCP tramite la suddivisione di una connessione che comporterebbe un tempo di round trip elevato in parti più piccole.  Inserendo degli ambienti Frontdoor più vicino agli utenti finali e terminando le connessioni TCP in ambiente Frontdoor, una connessione TCP all’applicazione di back-end con un tempo di round trip (RTT) ampio è suddivisa in due connessioni TCP. La connessione breve tra l'utente finale e l'ambiente di Frontdoor significa che la connessione viene stabilita tra tre brevi round trip anziché tre lunghi round trip, risparmiando latenza.  La lunga connessione tra l’ambiente Frontdoor e il back-end può essere prestabilita e riutilizzata in più chiamate per l'utente finale, risparmiando nuovamente il tempo di connessione TCP.  L'effetto viene moltiplicato per stabilire una connessione SSL/TLS (Transport Layer Security) poiché sono presenti più round trip per la proteggere la connessione.

## <a name="processing-request-to-match-a-routing-rule"></a>Elaborazione della richiesta per la corrispondenza di una regola di gestione
Quando una richiesta viene inserita in un ambiente Frontdoor, dopo aver stabilito connessione ed esecuzione di un certificato SSL handshake, il primo passaggio è costituito dalla corrispondenza con una regola di gestione. Questa corrispondenza consiste nel determinare tra tutte le configurazioni in Frontdoor, quali regole di gestione specifiche corrispondano alla richiesta. Per altre informazioni, vedere come Frontdoor effettua la [corrispondenza delle route](front-door-route-matching.md).

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificazione di back-end disponibili nel pool di back-end per la regola di gestione
Una volta che Frontdoor acquisisce una corrispondenza per una regola di gestione basata su richiesta in ingresso e in assenza di memorizzazione nella cache, il passaggio successivo è estrarre lo stato di integrità del probe per il pool di back-end associato alla route corrispondente. Per altre informazioni, vedere come Frontdoor monitora l'integrità del back-end usando [Probe di integrità](front-door-health-probes.md).

## <a name="forwarding-the-request-to-your-application-backend"></a>Inoltrare la richiesta al back-end dell'applicazione
Infine, supponendo che non vi sia nessuna memorizzazione nella cache configurata, la richiesta dell’utente viene inoltrata al back-end "migliore" basandosi sulla configurazione del [Metodo di routing di Frontdoor](front-door-routing-methods.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
