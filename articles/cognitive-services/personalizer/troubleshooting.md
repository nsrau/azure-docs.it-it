---
title: Risoluzione dei problemi-personalizzatore
titleSuffix: Azure Cognitive Services
description: In questo articolo sono disponibili informazioni sulla risoluzione dei problemi relativi a Personalizzazioner.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663674"
---
# <a name="personalizer-troubleshooting"></a>Risoluzione dei problemi di personalizzazione

Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.

## <a name="learning-loop"></a>Ciclo di apprendimento

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Il ciclo di apprendimento non sembra essere appreso. Come si risolve il problema?

Il ciclo di apprendimento necessita di alcune migliaia di chiamate prima di classificare in modo efficace la priorità delle chiamate di rango. 

Se non si è certi della modalità di funzionamento del ciclo di apprendimento, eseguire una [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretti. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)