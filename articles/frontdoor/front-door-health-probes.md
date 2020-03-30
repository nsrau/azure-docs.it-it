---
title: Porta frontale di Azure - monitoraggio dell'integrità del back-end Documenti Microsoft
description: Questo articolo illustra in che modo Azure Front Door monitora l'integrità dei back-end
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471575"
---
# <a name="health-probes"></a>Probe di integrità

Per determinare l'integrità e la prossimità di ogni back-end da un determinato ambiente Front Door, ogni ambiente Front Door invia periodicamente una richiesta HTTP/HTTPS sintetica a ciascuno dei back-end configurati. Frontdoor usa quindi le risposte ricevute da questi probe per determinare i "migliori" back-end a cui indirizzare le richieste client reali. 

> [!WARNING]
> Poiché Front Door dispone di molti ambienti perimetrali a livello globale, il volume delle richieste di probe di integrità per i back-end può essere piuttosto elevato, da 25 richieste al minuto fino a 1200 richieste al minuto, a seconda della frequenza del probe di integrità configurata. Con una frequenza probe predefinita di 30 secondi, il volume del probe sul back-end deve essere di circa 200 richieste al minuto.

## <a name="supported-protocols"></a>Protocolli supportati

Frontdoor supporta l'invio di probe tramite i protocolli HTTP o HTTPS. Questi probe vengono inviati tramite le stesse porte TCP configurate per il routing delle richieste client e non è possibile eseguirne l'override.

## <a name="supported-http-methods-for-health-probes"></a>Metodi HTTP supportati per i probe di integritàSupported HTTP methods for health probes

Front Door supporta i seguenti metodi HTTP per l'invio dei probe di integrità:

1. **OTTENERE:** Il metodo GET significa recuperare tutte le informazioni (sotto forma di entità) identificate dall'URI della richiesta.
2. **TESTA DI CAPO:** Il metodo HEAD è identico a GET, ad eccezione del fatto che il server NON DEVE restituire un corpo del messaggio nella risposta. Per i nuovi profili Front Door, per impostazione predefinita, il metodo di sonda è impostato su HEAD.

> [!NOTE]
> Per ridurre il carico e i costi sui back-end, Front Door consiglia di utilizzare le richieste HEAD per le sonde di integrità.

## <a name="health-probe-responses"></a>Risposte del probe di integrità

| Risposte  | Descrizione | 
| ------------- | ------------- |
| Determinazione dell'integrità  |  Il codice di stato 200 OK indica che il back-end è integro. Tutti gli altri codici vengono considerati errori. Se per qualsiasi motivo, compreso un errore di rete, non viene ricevuta una risposta HTTP valida per un probe, il probe viene conteggiato come errore.|
| Misurazione della latenza  | La latenza è il tempo misurato dal momento immediatamente prima dell'invio della richiesta del probe al momento in cui viene ricevuto l'ultimo byte della risposta. Viene usata una nuova connessione TCP per ogni richiesta, in modo tale che questa misurazione non venga influenzata dai back-end con connessioni esistenti a caldo.  |

## <a name="how-front-door-determines-backend-health"></a>Come Frontdoor determina l'integrità dei back-end

La porta frontale di Azure usa lo stesso processo in tre passaggi riportato di seguito in tutti gli algoritmi per determinare l'integrità.

1. Escludere i back-end disabilitati.

2. Escludere i back-end che presentano errori nei probe di integrità:
    * Questa selezione viene eseguita osservando le ultime _n_ risposte del probe di integrità. Se almeno _x_ sono integre, il back-end viene considerato integro.

    * _n_ viene configurato modificando la proprietà SampleSize nelle impostazioni di bilanciamento del carico.

    * _x_ viene configurato modificando la proprietà SuccessfulSamplesRequired nelle impostazioni di bilanciamento del carico.

3. Oltre al set di back-end integri nel pool di back-end, Frontdoor misura inoltre e mantiene la latenza (tempo di round trip) per ogni back-end.


## <a name="complete-health-probe-failure"></a>Errore di completamento del probe di integrità

Se i probe di integrità hanno esito negativo per ogni back-end in un pool di back-end, Frontdoor considererà tutti i back-end non integri e instraderà il traffico in una distribuzione round robin per tutti i back-end.

Una volta che un back-end torna a uno stato integro, Front Door riprenderà il normale algoritmo di bilanciamento del carico.

## <a name="disabling-health-probes"></a>Disabilitazione dei probe di integritàDisabling health probes

Se nel pool back-end è presente un singolo back-end, è possibile scegliere di disabilitare i probe di integrità riducendo il carico sul back-end dell'applicazione. Anche se nel pool back-end sono presenti più back-end, ma solo uno di essi è abilitato, è possibile disabilitare i probe di integrità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
