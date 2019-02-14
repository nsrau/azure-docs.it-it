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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 64db05e5e40b76d219ea0e3214c20297f32da4b5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861273"
---
# <a name="detecting-adult-and-racy-content"></a>Rilevamento di contenuti audaci e per adulti

Le diverse categorie visive includono anche il gruppo di contenuto spinti e per adulti, che consente d rilevare materiali per adulti e di limitare la visualizzazione delle immagini con contenuto sessuale. Il filtro per il rilevamento del contenuto spinto e per adulti può essere impostato tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="defining-adult-and-racy-content"></a>Definizione di contenuti audaci e per adulti

Tra le varie caratteristiche visive gestite dal [metodo Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), Adult consente di rilevare immagini con contenuti audaci o per adulti. Le immagini per adulti sono immagini di carattere pornografico in cui sono in genere rappresentati atti sessuali o scene di nudo. Le immagini audaci sono immagini sessualmente allusive in cui sono presenti contenuti a sfondo sessuale meno espliciti rispetto alle immagini per adulti. Il tipo di caratteristica visiva Adult viene usato in genere per limitare la visualizzazione di immagini con contenuti sessualmente allusivi ed esplicitamente pornografici.

## <a name="identifying-adult-and-racy-content"></a>Identificazione di contenuti audaci e per adulti

Nella risposta JSON del metodo Analyze Image vengono restituite due proprietà, `isAdultContent` e `isRacyContent`, per indicare rispettivamente contenuti per adulti e audaci. Entrambe le proprietà restituiscono un valore booleano, true o false. Il metodo restituisce inoltre due proprietà, `adultScore` e `racyScore`, che rappresentano rispettivamente il grado di attendibilità dei risultati dell'identificazione di contenuti per adulti e audaci. È possibile impostare un filtro di attendibilità per il rilevamento di contenuti audaci e per adulti basato su una scala progressiva, in modo da soddisfare i requisiti dello specifico scenario.

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi al [rilevamento di contenuti specifici di dominio](concept-detecting-domain-content.md) e al [rilevamento dei visi](concept-detecting-faces.md).
