---
title: Rilevare contenuti audaci e per adulti - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di contenuti audaci e per adulti tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368342"
---
# <a name="detect-adult-and-racy-content"></a>Rilevare contenuti audaci e per adulti

Visione artificiale può rilevare materiale per soli adulti nelle immagini per consentire agli sviluppatori di limitare la visualizzazione di tali immagini nel software. Vengono applicati flag di contenuto con un punteggio compreso tra 0 e 1, in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze. 

> [!NOTE]
> Questa funzionalità è disponibile anche nel servizio [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview). Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

Le immagini per **adulti** sono immagini di natura pornografica in cui sono in genere rappresentati atti sessuali o scene di nudo. 

Le immagini **audaci** sono immagini di natura sessualmente allusiva in cui sono presenti contenuti a sfondo sessuale meno espliciti rispetto alle immagini contrassegnate come per **adulti**. 

## <a name="identify-adult-and-racy-content"></a>Identificare contenuti audaci e per adulti

API [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

Il metodo Analyze Image restituisce nella risposta JSON del metodo due proprietà booleane, `isAdultContent` e `isRacyContent`, per indicare rispettivamente i contenuti per adulti e quelli audaci. Il metodo restituisce anche due proprietà, `adultScore` e `racyScore`, che rappresentano i punteggi di attendibilità per identificare rispettivamente i contenuti per adulti e quelli audaci.

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi al [rilevamento di contenuti specifici di dominio](concept-detecting-domain-content.md) e al [rilevamento dei visi](concept-detecting-faces.md).
