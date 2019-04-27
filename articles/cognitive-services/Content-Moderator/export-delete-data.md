---
title: Esportare o eliminare i dati utente - Content Moderator
titlesuffix: Azure Cognitive Services
description: Informazioni su come esportare o eliminare i dati in Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 1cd70bee22e56e2580b322b93e0f121261d97a94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699176"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Esportare o eliminare i dati utente in Content Moderator

Content Moderator raccoglie i dati dell'utente per il funzionamento del servizio, ma i clienti hanno il controllo completo sulla visualizzazione, l'esportazione e l'eliminazione dei propri dati mediante lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) e le [API Moderazione e Revisione](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Per altre informazioni su come esportare ed eliminare i dati utente in Content Moderator, vedere la tabella seguente.

| Dati | Operazione di esportazione | Operazione di eliminazione |
| ---- | ---------------- | ---------------- |
| Informazioni sull'account (chiavi di sottoscrizione) | N/D | Eliminare tramite il portale di Azure (sottoscrizioni di Azure). In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](https://contentmoderator.cognitive.microsoft.com/). |
| Immagini per la corrispondenza personalizzata | Chiamare l'API [Get image IDs](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) (Ottieni gli ID immagine). Le immagini vengono archiviate in un formato hash unidirezionale proprietario e non è possibile estrarre le immagini effettive. | Chiamare l'API [Delete all Images](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686) (Elimina tutte le immagini). In alternativa, eliminare la risorsa Content Moderator tramite il portale di Azure. |
| Termini per la corrispondenza personalizzata | Chiamare l'API [Get all terms](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) (Ottieni tutti i termini) | Chiamare l'API [Delete all terms](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) (Elimina tutti i termini). In alternativa, eliminare la risorsa Content Moderator tramite il portale di Azure. |
| Tag | N/D | Usare l'icona **Elimina** disponibile per ogni tag nella pagina delle impostazioni tag dell'interfaccia utente di revisione. In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](https://contentmoderator.cognitive.microsoft.com/). |
| Recensioni | Chiamare l'API [Get Review](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) (Ottieni revisione) | Usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](https://contentmoderator.cognitive.microsoft.com/).
| Utenti | N/D | Usare l'icona **Elimina** disponibile per ogni utente nella pagina delle impostazioni team dell'[interfaccia utente di revisione](https://contentmoderator.cognitive.microsoft.com/). In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](https://contentmoderator.cognitive.microsoft.com/). |

