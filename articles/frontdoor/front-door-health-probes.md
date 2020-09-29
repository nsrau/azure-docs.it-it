---
title: Sportello anteriore di Azure-monitoraggio dello stato back-end | Microsoft Docs
description: Questo articolo consente di comprendere come il front-end di Azure monitora l'integrità dei backend
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449239"
---
# <a name="health-probes"></a>Probe di integrità

Per determinare l'integrità e la vicinanza di ogni back-end per un ambiente front-end specifico, ogni ambiente di sportello anteriore invia periodicamente una richiesta HTTP/HTTPS sintetica a ognuno dei back-end configurati. La porta anteriore usa quindi queste risposte dal probe per determinare le risorse di back-end "migliori" per instradare le richieste del client. 

> [!WARNING]
> Poiché la porta anteriore ha molti ambienti perimetrali a livello globale, il volume del probe di integrità per i backend può essere molto elevato da 25 richieste al minuto fino a 1200 richieste al minuto, a seconda della frequenza di probe di integrità configurata. Con la frequenza Probe predefinita di 30 secondi, il volume probe sul back-end deve essere di circa 200 richieste al minuto.

## <a name="supported-protocols"></a>Protocolli supportati

Frontdoor supporta l'invio di probe tramite i protocolli HTTP o HTTPS. Questi probe vengono inviati tramite le stesse porte TCP configurate per il routing delle richieste client e non è possibile eseguirne l'override.

## <a name="supported-http-methods-for-health-probes"></a>Metodi HTTP supportati per i probe di integrità

Front door supporta i seguenti metodi HTTP per l'invio dei probe di integrità:

1. **Ottenere:** Il metodo GET indica di recuperare qualsiasi informazione (sotto forma di entità) identificata dall'URI della richiesta.
2. **Intestazione:** Il metodo HEAD è identico a GET ad eccezione del fatto che il server non deve restituire un corpo del messaggio nella risposta. Per i nuovi profili di porte anteriori, per impostazione predefinita, il metodo Probe è impostato come HEAD.

> [!NOTE]
> Per un carico inferiore e un costo sui backend, la porta anteriore consiglia di usare le richieste HEAD per i probe di integrità.

## <a name="health-probe-responses"></a>Risposte del probe di integrità

| Risposte  | Descrizione | 
| ------------- | ------------- |
| Determinazione dell'integrità  |  Il codice di stato 200 OK indica che il back-end è integro. Tutti gli altri codici vengono considerati errori. Se per qualsiasi motivo (incluso l'errore di rete) non viene ricevuta una risposta HTTP valida per un probe, il probe viene considerato come un errore.|
| Misurazione della latenza  | La latenza è il tempo misurato dal momento immediatamente prima dell'invio della richiesta del probe al momento in cui viene ricevuto l'ultimo byte della risposta. Viene usata una nuova connessione TCP per ogni richiesta, quindi questa misurazione non è distorta per i backend con le connessioni calde esistenti.  |

## <a name="how-front-door-determines-backend-health"></a>Come Frontdoor determina l'integrità dei back-end

Il front-end di Azure usa lo stesso processo in tre passaggi riportato di seguito in tutti gli algoritmi per determinare l'integrità.

1. Escludere i back-end disabilitati.

2. Escludere i back-end che presentano errori nei probe di integrità:
    * Questa selezione viene eseguita osservando le ultime _n_ risposte del probe di integrità. Se almeno _x_ sono integre, il back-end viene considerato integro.

    * _n_ viene configurato modificando la proprietà SampleSize nelle impostazioni di bilanciamento del carico.

    * _x_ viene configurato modificando la proprietà SuccessfulSamplesRequired nelle impostazioni di bilanciamento del carico.

3. Per i set di back-end integri nel pool back-end, la porta anteriore misura e mantiene la latenza (tempo di round trip) per ogni back-end.


## <a name="complete-health-probe-failure"></a>Errore di completamento del probe di integrità

Se i probe di integrità hanno esito negativo per ogni back-end in un pool di back-end, Frontdoor considererà tutti i back-end non integri e instraderà il traffico in una distribuzione round robin per tutti i back-end.

Quando un back-end torna in uno stato integro, la porta anteriore riprenderà il normale algoritmo di bilanciamento del carico.

## <a name="disabling-health-probes"></a>Disabilitazione di probe di integrità

Se si dispone di un unico back-end nel pool back-end, è possibile scegliere di disabilitare i probe di integrità riducendo il carico sul back-end dell'applicazione. Anche se sono presenti più back-end nel pool back-end, ma solo uno di essi è abilitato, è possibile disabilitare i probe di integrità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
