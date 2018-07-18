---
title: Esportare o eliminare i dati in Content Moderator - Servizi cognitivi di Azure | Microsoft Docs
description: Informazioni su come esportare o eliminare i dati in Content Moderator.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378060"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Esportare o eliminare i dati utente in Content Moderator

Content Moderator raccoglie i dati dell'utente per il funzionamento del servizio, ma i clienti hanno il controllo completo sulla visualizzazione, l'esportazione e l'eliminazione dei propri dati mediante l'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/) e le [API](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Per altre informazioni su come esportare ed eliminare i dati utente in Content Moderator, vedere la tabella seguente.

| Dati | Operazione di esportazione | Operazione di eliminazione |
| ---- | ---------------- | ---------------- |
| Informazioni sull'account (chiavi di sottoscrizione) | N/D | Eliminare tramite il portale di Azure (sottoscrizioni di Azure). In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/). |
| Immagini per la corrispondenza personalizzata | [Ottenere gli ID delle immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Le immagini vengono archiviate in un formato hash unidirezionale proprietario e non è possibile estrarre le immagini effettive. | [Eliminare tutte le immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). In alternativa, eliminare la risorsa Content Moderator tramite il portale di Azure. |
| Termini per la corrispondenza personalizzata | [Ottenere tutti i termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Eliminare tutti i termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). In alternativa, eliminare la risorsa Content Moderator tramite il portale di Azure. |
| Tag | N/D | Usare l'icona **Elimina** disponibile per ogni tag nella pagina delle impostazioni tag dell'interfaccia utente di revisione. In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/). |
| Revisioni | [Ottenere la revisione](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/).
| Utenti | N/D | Usare l'icona **Elimina** disponibile per ogni utente nella pagina delle impostazioni team dell'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/). In alternativa, usare il pulsante **Elimina team** nella pagina delle impostazioni team dell'[interfaccia utente di revisione](http://contentmoderator.cognitive.microsoft.com/). |

