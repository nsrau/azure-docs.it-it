---
title: Esportare o eliminare i dati - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Mantenete il pieno controllo sui dati. Questo articolo spiega come visualizzare, esportare o eliminare i dati nel Servizio visione artificiale personalizzato.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718962"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Esportare o eliminare i dati in Visione personalizzata

Custom Vision raccoglie i dati degli utenti per gestire il servizio, ma i clienti hanno il pieno controllo sulla visualizzazione, l'esportazione e l'eliminazione dei dati utilizzando le API di [formazione](https://go.microsoft.com/fwlink/?linkid=865446)con visione personalizzata .

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Per informazioni su come esportare ed eliminare i dati utente in Visione personalizzata, vedere la tabella seguente.

| Dati | Operazione di esportazione | Operazioni di eliminazione |
| ---- | ---------------- | ---------------- |
| Informazioni sull'account (chiavi di sottoscrizione) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminare tramite il portale di Azure (sottoscrizioni di Azure). In alternativa, usare il pulsante "Elimina l'account" nella pagina delle impostazioni di CustomVision.ai (sottoscrizioni di account Microsoft) | 
| Dettagli sull'iterazione | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Dettagli sulle prestazioni dell'iterazione | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Elenco delle iterazioni | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Progetti e dettagli dei progetti | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Tag di immagine | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Immagini | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelli esportati | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminati con l'eliminazione dell'account |
