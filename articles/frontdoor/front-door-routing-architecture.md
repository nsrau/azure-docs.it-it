---
title: Architettura di routing di Azure front door | Microsoft Docs
description: Questo articolo aiuta a comprendere l'aspetto di visualizzazione globale dell'architettura di Frontdoor.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449194"
---
# <a name="routing-architecture-overview"></a>Panoramica dell'architettura di routing

Quando il front-end di Azure riceve le richieste client, effettuerà una delle due operazioni seguenti. È possibile rispondervi se si Abilita la memorizzazione nella cache o la si invia al back-end dell'applicazione appropriato come proxy inverso.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selezionare l'ambiente di Frontdoor per il routing del traffico (Anycast)

Il traffico indirizzato agli ambienti front-end di Azure usa [anycast](https://en.wikipedia.org/wiki/Anycast) per il traffico DNS (Domain Name System) e HTTP (Hypertext Transfer Protocol), che consente alle richieste degli utenti di raggiungere l'ambiente più vicino nel minor numero di hop di rete. Questa architettura offre tempi di round trip migliori per gli utenti finali, ottimizzando i vantaggi di Split TCP. Frontdoor organizza gli ambienti in primari e in "anelli" di fallback. L'anello esterno dispone di ambienti più vicini agli utenti, offrendo latenze più basse.  Il ring interno dispone di ambienti in grado di gestire il failover per l'ambiente anello esterno in caso di problemi. L'anello esterno è la destinazione preferita per tutto il traffico e l'anello interno è gestire l'overflow del traffico dall'anello esterno. A ogni host front-end o dominio servito dalla porta anteriore viene assegnato un indirizzo VIP primario (indirizzi IP virtuali), che viene annunciato dagli ambienti nell'anello interno ed esterno. Un indirizzo VIP di fallback viene annunciato solo da ambienti nell'anello interno. 

Questa architettura garantisce che le richieste degli utenti finali raggiungano sempre l'ambiente di sportello anteriore più vicino. Anche se l'ambiente front-end preferito non è integro, tutto il traffico passa automaticamente al prossimo ambiente più vicino.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Connessione all'ambiente Frontdoor (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) è una tecnica che serve a ridurre latenza e problemi TCP tramite la suddivisione di una connessione che comporterebbe un tempo di round trip elevato in parti più piccole. Con gli ambienti front-end più vicini agli utenti finali, le connessioni TCP terminano all'interno dell'ambiente di sportello anteriore. Una connessione TCP con un tempo di round trip di grandi dimensioni (RTT) al back-end dell'applicazione viene suddivisa in due connessioni separate. La "connessione breve" tra l'utente finale e l'ambiente di sportello anteriore indica che la connessione viene stabilita in tre round trip brevi invece che in tre round trip lunghi, il che comporta il salvataggio della latenza. La "connessione lunga" tra l'ambiente della porta anteriore e il back-end può essere prestabilita e quindi riutilizzata in altre richieste degli utenti finali per risparmiare tempo di connettività. L'effetto di Split TCP viene moltiplicato quando si stabilisce una connessione SSL/TLS (Transport Layer Security) perché sono presenti più round trip per proteggere una connessione.

## <a name="processing-request-to-match-a-routing-rule"></a>Elaborazione della richiesta per la corrispondenza di una regola di gestione
Dopo aver stabilito una connessione e completato un handshake TLS, il primo passaggio dopo una richiesta viene rilasciata in un ambiente front-end in modo che corrisponda alla regola di routing. La corrispondenza è determinata dalle configurazioni sulla porta anteriore a cui è determinata la regola di routing a cui associare la richiesta. Per altre informazioni, vedere come Frontdoor effettua la [corrispondenza delle route](front-door-route-matching.md).

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificazione di back-end disponibili nel pool di back-end per la regola di gestione
Quando la porta anteriore corrisponde a una regola di routing per una richiesta in ingresso, il passaggio successivo consiste nell'ottenere lo stato del probe di integrità per il pool back-end associato alla regola di routing se non è presente alcuna memorizzazione nella cache. Per altre informazioni, vedere come Frontdoor monitora l'integrità del back-end usando [Probe di integrità](front-door-health-probes.md).

## <a name="forwarding-the-request-to-your-application-backend"></a>Inoltrare la richiesta al back-end dell'applicazione
Infine, supponendo che la memorizzazione nella cache non sia configurata, la richiesta dell'utente viene trasmessa al back-end "migliore" in base alla configurazione del [metodo di routing](front-door-routing-methods.md) .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
