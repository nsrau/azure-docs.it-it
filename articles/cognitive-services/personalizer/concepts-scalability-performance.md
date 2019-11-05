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
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490790"
---
# <a name="scalability-and-performance"></a>Scalabilità e prestazioni

Nei siti Web e nelle applicazioni ad alte prestazioni e con traffico elevato è necessario tenere in considerazione due fattori principali in merito alla scalabilità e alle prestazioni di Personalizza esperienze:

* Mantenere bassa la latenza quando si effettuano chiamate alle API di classificazione
* Assicurarsi che la velocità effettiva di training stia al passo con l'input di eventi

La personalizzazione può restituire rapidamente un rango, con la maggior parte della durata della chiamata dedicata alla comunicazione tramite l'API REST. La scalabilità automatica di Azure consentirà di rispondere rapidamente alle richieste.

##  <a name="low-latency-scenarios"></a>Scenari a bassa latenza

Alcune applicazioni richiedono bassi livelli di latenza quando restituiscono una classificazione. Sono necessarie latenze basse:

* Evitare che l'utente resti in attesa per molto tempo prima che venga visualizzato il contenuto classificato.
* Evitare che un server con molto traffico occupi tempo di elaborazione o connessioni di rete disponibili in quantità limitata.


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