---
title: Esportare o eliminare i dati in Visione personalizzata - Servizi cognitivi di Azure | Microsoft Docs
description: Informazioni su come esportare o eliminare i dati in Visione personalizzata.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378068"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Esportare o eliminare i dati in Visione personalizzata

Content Moderator raccoglie i dati dell'utente per il funzionamento del servizio, ma i clienti hanno il controllo completo sulla visualizzazione, l'esportazione e l'eliminazione dei propri dati mediante l'[API Training](https://go.microsoft.com/fwlink/?linkid=865446) del Servizio visione artificiale personalizzato.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Per altre informazioni su come esportare ed eliminare i dati utente in Visione personalizzata, vedere la tabella seguente.

| Dati | Operazione di esportazione | Operazione di eliminazione |
| ---- | ---------------- | ---------------- |
| Informazioni sull'account (chiavi di sottoscrizione) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminare tramite il portale di Azure (sottoscrizioni di Azure). In alternativa, usare il pulsante "Elimina l'account" nella pagina delle impostazioni di CustomVision.ai (sottoscrizioni di account Microsoft) |
| Dettagli sull'iterazione | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Dettagli sulle prestazioni dell'iterazione | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Elenco delle iterazioni | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Progetti e dettagli dei progetti | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Tag immagine | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Immagini | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornisce l'URI per il download di immagini) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Modelli esportati | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminati con l'eliminazione dell'account |
