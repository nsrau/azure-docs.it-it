---
title: Ottenere le chiavi di sottoscrizione - Visione artificiale
titlesuffix: Azure Cognitive Services
description: Informazioni su come ottenere chiavi di sottoscrizione per le chiamate all'API Visione artificiale in Servizi cognitivi di Azure.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 820531cc2254d9cbc7aaf7e758dd0457b282d892
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580808"
---
# <a name="how-to-obtain-subscription-keys"></a>Come ottenere chiavi di sottoscrizione

I servizi di Visione artificiale richiedono chiavi di sottoscrizione speciali. Per ogni chiamata all'API Visione artificiale è richiesta una chiave di sottoscrizione. Questa chiave deve essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta.

Per iscriversi e ottenere chiavi di sottoscrizione, vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). L'iscrizione è gratuita. I prezzi per questi servizi sono soggetti a modifiche.

>[!NOTE]
Le chiavi di sottoscrizione sono valide per una sola di queste [aree di Microsoft Azure](https://azure.microsoft.com/regions/). 

| Region | Indirizzo |
|---|---|
| Stati Uniti occidentali | westus.api.cognitive.microsoft.com |
| Stati Uniti orientali 2 | eastus2.api.cognitive.microsoft.com |
| Stati Uniti centro-occidentali | westcentralus.api.cognitive.microsoft.com |
| Europa occidentale | westeurope.api.cognitive.microsoft.com |
| Asia sud-orientale | southeastasia.api.cognitive.microsoft.com |

Se si effettua l'iscrizione usando la versione di valutazione gratuita di Visione artificiale, le chiavi di sottoscrizione sono valide per l'area **westcentral** (`https://westcentralus.api.cognitive.microsoft.com/`). Questo è il caso più comune. Se invece si effettua l'iscrizione a Visione artificiale con il proprio account Microsoft Azure tramite il sito Web [https://azure.microsoft.com/](https://azure.microsoft.com/), si può specificare l'area per la versione di valutazione dall'elenco di aree precedente.

Se ad esempio si effettua l'iscrizione a Visione artificiale con il proprio account Microsoft Azure e come area si specifica `westus`, è necessario usare l'area `westus` per le chiamate all'API REST (`https://westus.api.cognitive.microsoft.com/`).

Se si dimentica l'area per la chiave di sottoscrizione dopo aver ottenuto la chiave di prova, è possibile trovare l'area all'indirizzo [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Collegamenti correlati:

* [Opzioni di prezzo per le API Servizi cognitivi di Azure](https://azure.microsoft.com/pricing/details/cognitive-services/)
