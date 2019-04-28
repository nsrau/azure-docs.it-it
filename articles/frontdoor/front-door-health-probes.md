---
title: Servizio Frontdoor di Azure - Monitoraggio dell'integrità dei back-end | Microsoft Docs
description: Questo articolo aiuta a comprendere come il servizio Frontdoor di Azure monitora l'integrità dei back-end
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736582"
---
# <a name="health-probes"></a>Probe di integrità

Per determinare l'integrità di ogni back-end, ogni ambiente Frontdoor invia periodicamente una richiesta HTTP/HTTPS sintetica a ciascuno dei back-end configurati. Frontdoor usa quindi le risposte ricevute da questi probe per determinare i "migliori" back-end a cui indirizzare le richieste client reali.


## <a name="supported-protocols"></a>Protocolli supportati

Frontdoor supporta l'invio di probe tramite i protocolli HTTP o HTTPS. Questi probe vengono inviati tramite le stesse porte TCP configurate per il routing delle richieste client e non è possibile eseguirne l'override.

## <a name="health-probe-responses"></a>Risposte del probe di integrità

| Risposte  | DESCRIZIONE | 
| ------------- | ------------- |
| Determinazione dell'integrità  |  Il codice di stato 200 OK indica che il back-end è integro. Tutti gli altri codici vengono considerati errori. Se per qualsiasi motivo, compreso un errore di rete, non viene ricevuta una risposta HTTP valida per un probe, il probe viene conteggiato come errore.|
| Misurazione della latenza  | La latenza è il tempo misurato dal momento immediatamente prima dell'invio della richiesta del probe al momento in cui viene ricevuto l'ultimo byte della risposta. Viene usata una nuova connessione TCP per ogni richiesta, in modo tale che questa misurazione non venga influenzata dai back-end con connessioni esistenti a caldo.  |

## <a name="how-front-door-determines-backend-health"></a>Come Frontdoor determina l'integrità dei back-end

Per determinare l'integrità, il servizio Frontdoor di Azure usa lo stesso processo in tre passaggi riportato di seguito per tutti gli algoritmi.

1. Escludere i back-end disabilitati.

2. Escludere i back-end che presentano errori nei probe di integrità:
    * Questa selezione viene eseguita osservando le ultime _n_ risposte del probe di integrità. Se almeno _x_ sono integre, il back-end viene considerato integro.

    * _n_ viene configurato modificando la proprietà SampleSize nelle impostazioni di bilanciamento del carico.

    * _x_ viene configurato modificando la proprietà SuccessfulSamplesRequired nelle impostazioni di bilanciamento del carico.

3. Oltre al set di back-end integri nel pool di back-end, Frontdoor misura inoltre e mantiene la latenza (tempo di round trip) per ogni back-end.


## <a name="complete-health-probe-failure"></a>Errore di completamento del probe di integrità

Se i probe di integrità hanno esito negativo per ogni back-end in un pool di back-end, Frontdoor considererà tutti i back-end non integri e instraderà il traffico in una distribuzione round robin per tutti i back-end.

Una volta che tutti i back-end tornano a uno stato integro, Frontdoor riprenderà il normale algoritmo di bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
