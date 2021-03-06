---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018367"
---
Si noti il dispositivo di scorrimento **Probability Threshold** (Soglia probabilità) nel riquadro sinistro della scheda **Prestazioni**. Questo è il livello di confidenza che una stima deve avere per essere considerata corretta (ai fini del calcolo della precisione e del richiamo). 

Quando si interpretano le chiamate di stima con una soglia di probabilità alta, i risultati restituiti tendono ad avere una precisione elevata a scapito del richiamo, ossia le classificazioni rilevate sono corrette, ma molte rimangono non rilevate. Con una soglia di probabilità bassa avviene l'opposto, ossia viene rilevata la maggior parte delle classificazioni, ma all'interno del set sono presenti più falsi positivi. Tenendo presente questo aspetto, è consigliabile impostare la soglia di probabilità in base alle esigenze specifiche del progetto. In seguito, quando si ricevono i risultati della stima sul lato client, è consigliabile usare lo stesso valore di soglia di probabilità usato qui.