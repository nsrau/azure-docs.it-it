---
title: Scalabilità e prestazioni-personalizzatore
titleSuffix: Azure Cognitive Services
description: 'Nei siti Web e nelle applicazioni ad alte prestazioni e con traffico elevato è necessario tenere in considerazione due fattori principali in merito alla scalabilità e alle prestazioni di Personalizza esperienze: latenza e velocità effettiva di training.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662813"
---
# <a name="scalability-and-performance"></a>Scalabilità e prestazioni

Nei siti Web e nelle applicazioni ad alte prestazioni e con traffico elevato è necessario tenere in considerazione due fattori principali in merito alla scalabilità e alle prestazioni di Personalizza esperienze:

* Mantenere bassa la latenza quando si effettuano chiamate alle API di classificazione
* Assicurarsi che la velocità effettiva di training stia al passo con l'input di eventi

La personalizzazione può restituire una classificazione molto rapidamente, con la durata della chiamata dedicata in gran parte alla comunicazione tramite l'API REST. La scalabilità automatica di Azure consentirà di rispondere rapidamente alle richieste.

##  <a name="low-latency-scenarios"></a>Scenari a bassa latenza

Alcune applicazioni richiedono bassi livelli di latenza quando restituiscono una classificazione. Questo aspetto è necessario per:

* Evitare che l'utente resti in attesa per molto tempo prima che venga visualizzato il contenuto classificato.
* Evitare che un server con molto traffico occupi tempo di elaborazione o connessioni di rete disponibili in quantità limitata.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Scalabilità e velocità effettiva di training

Il funzionamento di Personalizza esperienze si basa sull'aggiornamento di un modello di cui è stato nuovamente eseguito il training in base ai messaggi inviati in modo asincrono da Personalizza esperienze in seguito all'applicazione delle API di classificazione e ricompensa. Questi messaggi vengono inviati tramite un hub eventi di Azure per l'applicazione.

 È improbabile che la maggior parte delle applicazioni raggiunga la velocità effettiva massima di join e training consentita da Personalizza esperienze. Sebbene il raggiungimento di questo limite massimo non rallenti l'applicazione, la velocità di esaurimento delle code dell'hub eventi sarebbe superiore alla velocità con cui verrebbero pulite.

## <a name="how-to-estimate-your-throughput-requirements"></a>Come stimare i requisiti di velocità effettiva

* Per stimare il numero medio di byte per ogni evento di classificazione, aggiungere le lunghezze dei documenti JSON di contesto e azione.
* Dividere 20 MB/sec per il numero medio di byte stimato.

Se, ad esempio, il payload medio include 500 funzionalità e si stima che ognuna di esse sia composta da 20 caratteri, ogni evento occuperebbe circa 10 kb. Con queste stime, 20.000.000 / 10.000 = 2.000 eventi al secondo, ovvero circa 173 milioni di eventi al giorno. 

Se si raggiungono questi limiti, contattare il team di supporto per ricevere consulenza sull'architettura.

## <a name="next-steps"></a>Passaggi successivi

[Creare e configurare Personalizza esperienze](how-to-settings.md).