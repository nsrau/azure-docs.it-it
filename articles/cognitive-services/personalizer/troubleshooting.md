---
title: risoluzione dei problemi
titleSuffix: Azure Cognitive Services
description: Risoluzione dei problemi di domande su Personalizer è reperibile in questo articolo.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: 5136bd295c12c4439a894b4dcf0b868d32ce43ca
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313147"
---
# <a name="personalizer-troubleshooting"></a>Personalizer risoluzione dei problemi

Questo articolo contiene risposte alle domande sulla risoluzione dei problemi più frequenti sui Personalizer.

## <a name="learning-loop"></a>Ciclo di apprendimento

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Il ciclo di apprendimento non sembrano essere per altre. Come risolvere il problema?

Il ciclo di apprendimento deve alcune chiamate Reward migliaia prima di stabilire le priorità classifica le chiamate in modo efficace. 

Se si è certi che il ciclo di learning è attualmente comportamento, eseguire un' [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretto. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)