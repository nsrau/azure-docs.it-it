---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130092"
---
Come minimo, è consigliabile usare almeno 30 immagini per ogni tag nel set di training iniziale. È anche opportuno raccogliere alcune immagini aggiuntive per testare il modello dopo il training.

Per eseguire il training del modello in modo efficace, usare le immagini con diversi oggetti visivi. Selezionare immagini diverse per:
* angolazione
* illuminazione
* background
* stile visivo
* soggetti singoli/raggruppati
* size
* type

Assicurarsi anche che tutte le immagini di training soddisfino i criteri seguenti:
* formato JPG, PNG, BMP o GIF
* dimensioni massime pari a 6 MB (4 MB per le immagini per la previsione)
* almeno 256 pixel sul bordo più corto. Le immagini più piccole verranno automaticamente ingrandite dal Servizio visione artificiale personalizzato
