---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375297"
---
I dati restituiti con il valore atteso e i margini superiore e inferiore predefiniti. In pratica, è possibile definire un parametro [sensitivity], quindi usare (ExpectedValue + sensitivity * UpperMargin) come limite superiore e (ExpectedValue - sensitivity * LowerMargin) come limite inferiore per regolare autonomamente il punto di anomalia. Il valore del parametro [sensitivity] deve essere maggiore di 1. Di seguito sono riportati alcuni diagrammi per la regolazione del punto di anomalia.

> [!NOTE]
> I diagrammi non sono generati dall'applicazione di esempio, ma sono creati da uno strumento separato con l'applicazione di esempio.

![Regolazione: sensitivity = 1,0](../media/sensitivity_1.png)
![Regolazione: sensitivity = 1,5](../media/sensitivity_1.5.png)
![Regolazione: sensitivity = 2](../media/sensitivity_2.png)
![Regolazione: sensitivity = 3,5](../media/sensitivity_3.5.png)