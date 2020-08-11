---
title: INTERFACCIA della riga di comando di Azure Speech
titleSuffix: Azure Cognitive Services
description: L'interfaccia della riga di comando vocale è uno strumento da riga di comando per l'uso del servizio di riconoscimento vocale senza scrivere codice. L'interfaccia della riga di comando vocale richiede una configurazione minima ed è facile iniziare subito a sperimentare le funzionalità chiave del servizio di riconoscimento vocale per verificare se i casi d'uso possono essere soddisfatti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067427"
---
# <a name="what-is-the-speech-cli"></a>Che cos'è l'interfaccia della riga di comando per Voce?

L'interfaccia della riga di comando vocale è uno strumento da riga di comando per l'uso del servizio di riconoscimento vocale senza scrivere codice. L'interfaccia della riga di comando vocale richiede una configurazione minima ed è facile iniziare subito a sperimentare le funzionalità chiave del servizio di riconoscimento vocale per verificare se i casi d'uso possono essere soddisfatti. In pochi minuti, è possibile eseguire semplici flussi di lavoro di test come il riconoscimento vocale in batch da una directory di file o una sintesi vocale su una raccolta di stringhe da un file. Oltre ai semplici flussi di lavoro, l'interfaccia della riga di comando vocale è pronta per l'ambiente di produzione e può essere ridimensionata per eseguire processi di dimensioni maggiori usando `.bat` script automatici o Shell.

La maggior parte delle funzionalità principali nell'SDK per i dialoghi è disponibile nell'interfaccia della riga di comando vocale, mentre alcune funzionalità e personalizzazioni avanzate sono semplificate nell'interfaccia della riga di comando vocale. Prendere in considerazione le linee guida seguenti per decidere quando usare l'interfaccia della riga di comando vocale o l'SDK di riconoscimento vocale.

Usare l'interfaccia della riga di comando vocale nei casi seguenti:
* Si desidera sperimentare le funzionalità del servizio di riconoscimento vocale con configurazione minima e senza codice
* Per un'applicazione di produzione con il servizio riconoscimento vocale sono necessari requisiti relativamente semplici

Usare l'SDK di riconoscimento vocale nei casi seguenti:
* Si desidera integrare la funzionalità del servizio di riconoscimento vocale in una lingua o una piattaforma specifica, ad esempio C#, Python, C++
* Sono presenti requisiti complessi che possono richiedere richieste di servizio avanzate o lo sviluppo di comportamenti personalizzati, incluso il flusso delle risposte

## <a name="core-features"></a>Funzionalità di base

* Riconoscimento vocale: consente di convertire il testo vocale da file audio o direttamente da un microfono oppure di trascrivere una conversazione registrata.

* Sintesi vocale: consente di convertire sintesi vocale usando input da file di testo o direttamente dalla riga di comando. Personalizzare le caratteristiche dell'output vocale usando le [configurazioni di SSML](speech-synthesis-markup.md)e le [voci standard o neurali](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Traduzione vocale: consente di convertire l'audio in una lingua di origine in testo o audio in una lingua di destinazione.

* Eseguire sulle risorse di calcolo di Azure: inviare i comandi dell'interfaccia della riga di comando per l'esecuzione in una risorsa di calcolo remota di Azure usando `spx webjob` .

## <a name="get-started"></a>Introduzione

Per iniziare a usare l'interfaccia della riga di comando vocale, vedere l' [articolo Nozioni di base](spx-basics.md). Questo articolo illustra come eseguire alcuni comandi di base e Mostra anche comandi leggermente più avanzati per l'esecuzione di operazioni batch per sintesi vocale e sintesi vocale. Dopo aver letto l'articolo Nozioni di base, è necessario avere una conoscenza della sintassi per iniziare a scrivere alcuni comandi personalizzati o automatizzare semplici operazioni del servizio di sintesi vocale.

## <a name="next-steps"></a>Passaggi successivi

- [Nozioni di base sull'interfaccia della riga di comando per Voce](spx-basics.md)
- Se il caso d'uso è più complesso, [ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
