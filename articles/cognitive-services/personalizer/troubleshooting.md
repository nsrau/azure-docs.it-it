---
title: Risoluzione dei problemi-personalizzatore
titleSuffix: Azure Cognitive Services
description: In questo articolo sono disponibili informazioni sulla risoluzione dei problemi relativi a Personalizzazioner.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955217"
---
# <a name="personalizer-troubleshooting"></a>Risoluzione dei problemi di personalizzazione

Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.

## <a name="learning-loop"></a>Ciclo di apprendimento

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Il ciclo di apprendimento non sembra essere appreso. Come si risolve il problema?

Il ciclo di apprendimento necessita di alcune migliaia di chiamate prima di classificare in modo efficace la priorità delle chiamate di rango. 

Se non si è certi della modalità di funzionamento del ciclo di apprendimento, eseguire una [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretti. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)