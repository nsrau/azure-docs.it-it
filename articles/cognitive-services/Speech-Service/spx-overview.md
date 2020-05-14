---
title: SPX-servizio vocale
titleSuffix: Azure Cognitive Services
description: SPX è uno strumento da riga di comando per l'utilizzo del servizio di riconoscimento vocale senza scrivere codice. SPX richiede una configurazione minima ed è facile iniziare subito a sperimentare le funzionalità chiave del servizio di riconoscimento vocale per verificare se i casi d'uso possono essere soddisfatti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202282"
---
# <a name="what-is-spx"></a>Che cos'è SPX?

SPX è uno strumento da riga di comando per l'utilizzo del servizio di riconoscimento vocale senza scrivere codice. SPX richiede una configurazione minima ed è facile iniziare subito a sperimentare le funzionalità chiave del servizio di riconoscimento vocale per verificare se i casi d'uso possono essere soddisfatti. In pochi minuti, è possibile eseguire semplici flussi di lavoro di test come il riconoscimento vocale in batch da una directory di file o una sintesi vocale su una raccolta di stringhe da un file. Oltre ai semplici flussi di lavoro, SPX è pronto per l'ambiente di produzione e può essere scalato per eseguire processi di dimensioni maggiori usando `.bat` script automatici o Shell.

La maggior parte delle funzionalità principali di Speech SDK è disponibile in SPX, ma alcune funzionalità avanzate e personalizzazioni sono semplificate in SPX. Prendere in considerazione le linee guida seguenti per decidere quando usare SPX o l'SDK.

Usare SPX nei casi seguenti:
* Si desidera sperimentare le funzionalità del servizio di riconoscimento vocale con configurazione minima e senza codice
* Per un'applicazione di produzione con il servizio riconoscimento vocale sono necessari requisiti relativamente semplici

Usare l'SDK nei casi seguenti:
* Si desidera integrare la funzionalità del servizio di riconoscimento vocale in una lingua o una piattaforma specifica, ad esempio C#, Python, C++
* Sono presenti requisiti complessi che possono richiedere richieste di servizio avanzate o lo sviluppo di comportamenti personalizzati, incluso il flusso delle risposte

## <a name="core-features"></a>Funzionalità di base

* Riconoscimento vocale: consente di convertire il testo vocale da file audio o direttamente da un microfono oppure di trascrivere una conversazione registrata.

* Sintesi vocale: consente di convertire sintesi vocale usando input da file di testo o direttamente dalla riga di comando. Personalizzare le caratteristiche dell'output vocale usando le [configurazioni di SSML](speech-synthesis-markup.md)e le [voci standard o neurali](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Traduzione vocale: consente di convertire l'audio in una lingua di origine in un testo in una lingua di destinazione.

* Eseguire sulle risorse di calcolo di Azure-inviare comandi SPX da eseguire in una risorsa di calcolo remota di Azure usando `spx webjob` .

## <a name="get-started"></a>Introduzione

Per iniziare a usare SPX, vedere l' [articolo Nozioni di base](spx-basics.md). Questo articolo illustra come eseguire alcuni comandi di base in SPX e Mostra anche comandi leggermente più avanzati per l'esecuzione di operazioni batch per sintesi vocale e sintesi vocale. Dopo aver letto l'articolo Nozioni di base, è necessario avere una conoscenza della sintassi di SPX per iniziare a scrivere alcuni comandi personalizzati o automatizzare semplici operazioni di sintesi vocale.

## <a name="next-steps"></a>Passaggi successivi

- [Nozioni di base su SPX](spx-basics.md)
- Se il caso d'uso è più complesso, [ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
