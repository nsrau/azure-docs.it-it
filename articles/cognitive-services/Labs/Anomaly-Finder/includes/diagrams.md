---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228968"
---
I dati restituiti con il valore atteso e i margini superiore e inferiore predefiniti. In pratica, è possibile definire un parametro [sensitivity], quindi usare (ExpectedValue + sensitivity * UpperMargin) come limite superiore e (ExpectedValue - sensitivity * LowerMargin) come limite inferiore per regolare autonomamente il punto di anomalia. Il valore del parametro [sensitivity] deve essere maggiore di 1. Di seguito sono riportati alcuni diagrammi per la regolazione del punto di anomalia.

> [!NOTE]
> I diagrammi non sono generati dall'applicazione di esempio, ma vengono creati da un apposito strumento con l'applicazione di esempio.

![Regolazione: sensitivity = 1,0](../media/sensitivity_1.png)
![Regolazione: sensitivity = 1,5](../media/sensitivity_1.5.png)
![Regolazione: sensitivity = 2](../media/sensitivity_2.png)
![Regolazione: sensitivity = 3,5](../media/sensitivity_3.5.png)
