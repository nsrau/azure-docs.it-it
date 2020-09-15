---
title: Visualizzare o eliminare i dati Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Si mantiene il controllo completo sui dati. Questo articolo illustra come è possibile visualizzare, esportare o eliminare i dati nel Servizio visione artificiale personalizzato.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527395"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Visualizzare o eliminare dati utente in Visione personalizzata

Visione personalizzata raccoglie i dati utente per il funzionamento del servizio, ma i clienti hanno il controllo completo sulla visualizzazione e sull'eliminazione dei dati usando le [API di training](https://go.microsoft.com/fwlink/?linkid=865446)di visione personalizzata.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Per informazioni su come visualizzare ed eliminare i dati degli utenti in Visione personalizzata, vedere la tabella seguente.

| Data | Visualizza operazione | Operazioni di eliminazione |
| ---- | ---------------- | ---------------- |
| Informazioni account (chiavi di sottoscrizione) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminare tramite il portale di Azure (sottoscrizioni di Azure). In alternativa, usare il pulsante "Elimina account" nella pagina delle impostazioni di CustomVision.ai (sottoscrizioni dell'account Microsoft) | 
| Dettagli sull'iterazione | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Dettagli prestazioni iterazione | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Elenco delle iterazioni | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Progetti e dettagli dei progetti | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Tag di immagine | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Immagini | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Iterazioni esportate | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminati con l'eliminazione dell'account |
