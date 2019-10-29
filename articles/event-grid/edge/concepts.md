---
title: Concetti-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Concetti di griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992548"
---
# <a name="event-grid-concepts"></a>Concetti relativi a Griglia di eventi

Questo articolo illustra i concetti principali di Griglia di eventi di Azure.

## <a name="events"></a>Events

Un evento è la quantità minima di informazioni che descrive in modo completo qualcosa che si è verificato nel sistema. Ogni evento ha informazioni comuni, come: l'origine dell'evento, l'ora in cui l'evento si è verificato e un identificatore univoco. Ogni evento ha anche informazioni specifiche rilevanti solo per il tipo di evento specifico. Il supporto per un evento di dimensioni fino a 1 MB è attualmente in fase di anteprima.

Per le proprietà incluse in un evento, vedere schema di [eventi di griglia di eventi di Azure](event-schemas.md).

## <a name="publishers"></a>Autori

Un editore è un utente o un'organizzazione che decide di inviare eventi a Griglia di eventi. È possibile pubblicare eventi dalla propria applicazione.

## <a name="event-sources"></a>Origini eventi

Un'origine evento è la posizione in cui si verifica l'evento. Ogni origine evento è correlata a uno o più tipi di evento. Archiviazione di Azure è ad esempio l'origine degli eventi di creazione di BLOB. L'applicazione è l'origine evento per gli eventi personalizzati definiti dall'utente. Le origini evento sono responsabili dell'invio degli eventi in Griglia di eventi.

## <a name="topics"></a>Argomenti

L'argomento di Griglia di eventi include un endpoint a cui l'origine invia gli eventi. L'editore crea l'argomento di Griglia di eventi e decide se un'origine evento necessita di uno o più argomenti. L'argomento viene usato per una raccolta di eventi correlati. Per rispondere a determinati tipi di eventi, i sottoscrittori scelgono gli argomenti da sottoscrivere.

Quando si progetta l'applicazione, è possibile scegliere il numero di argomenti da creare. Per soluzioni di grandi dimensioni, creare un argomento personalizzato per ogni categoria di eventi correlati. Ad esempio, si consideri un'applicazione che invia gli eventi correlati alla modifica di account utente e all'elaborazione degli ordini. È improbabile che un gestore eventi richieda entrambe le categorie di eventi. Creare due argomenti personalizzati e consentire ai gestori di eventi di sottoscrivere all'argomento di interesse. Per le soluzioni di dimensioni ridotte, è preferibile inviare tutti gli eventi a un singolo argomento. I sottoscrittori di eventi possono filtrare in base ai tipi di eventi desiderati.

Vedere la [documentazione sull'API REST](api.md) per informazioni su come gestire gli argomenti in griglia di eventi.

## <a name="event-subscriptions"></a>Sottoscrizioni di eventi

Una sottoscrizione indica a Griglia di eventi gli eventi in un argomento che l'utente desidera ricevere. Quando si crea la sottoscrizione l'utente indica un endpoint per la gestione dell'evento. È possibile filtrare gli eventi inviati all'endpoint. 

Vedere la [documentazione sull'API REST](api.md) per informazioni su come gestire le sottoscrizioni in griglia di eventi.

## <a name="event-handlers"></a>Gestori eventi

Dal punto di vista di Griglia di eventi, un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue ulteriori operazioni per elaborare l'evento. Griglia di eventi supporta diversi tipi di gestori. È possibile usare un servizio di Azure supportato o il proprio webhook come gestore. A seconda del tipo di gestore, Griglia di eventi segue meccanismi diversi per garantire il recapito dell'evento. Se il gestore dell'evento di destinazione è un webhook HTTP, l'evento viene ritentato quando il gestore restituisce un codice di stato `200 – OK`. Per l'Hub Edge, se l'evento viene recapitato senza alcuna eccezione, viene considerato riuscito.

## <a name="security"></a>Sicurezza

Griglia di eventi fornisce la sicurezza per la sottoscrizione e la pubblicazione degli argomenti. Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

## <a name="event-delivery"></a>Recapito di eventi

Se Griglia di eventi non è in grado di confermare che un evento è stato ricevuto dall'endpoint del sottoscrittore, esegue di nuovo il recapito dell'evento. Per altre informazioni, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-retry.md).

## <a name="batching"></a>Creazione di batch

Quando si usa un argomento personalizzato, gli eventi devono sempre essere pubblicati in una matrice. Per gli scenari con velocità effettiva bassa, la matrice avrà un solo valore. Per i casi di utilizzo elevato dei volumi, è consigliabile raggruppare in batch più eventi per ogni pubblicazione per ottenere una maggiore efficienza. Le dimensioni dei batch possono arrivare fino a 1 MB. Ogni evento non deve ancora essere maggiore di 1 MB (anteprima).