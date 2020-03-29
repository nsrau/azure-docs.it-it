---
title: Concetti dello strumento Learn Review - Content Moderator
titleSuffix: Azure Cognitive Services
description: Scopri di più sullo strumento Content Moderator Review, un sito web che coordina un'IA combinata e uno sforzo di moderazione della revisione umana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169511"
---
# <a name="content-moderator-review-tool"></a>Strumento di revisione del moderatore del contenuto

Azure Content Moderator offre servizi per combinare la moderazione del contenuto di Apprendimento automatico con le recensioni umane e il sito Web [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com) è un front-end di facile utilizzo che consente l'accesso dettagliato a questi servizi.

![Dashboard dello strumento di revisione in un browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>Funzione

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), se utilizzato insieme alle API di moderazione assistite dal computer, consente di eseguire le attività seguenti nel processo di moderazione del contenuto:

- Usa un solo set di strumenti per moderare i contenuti in più formati (testo, immagine e video).
- Automatizza la creazione di [recensioni](../review-api.md#reviews) umane quando arrivano i risultati dell'API di moderazione.
- Assegnare o riassegnare le revisioni del contenuto a più team di revisione, organizzati per categoria di contenuto o livello di esperienza.
- Utilizzare filtri logici predefiniti o personalizzati ([flussi di lavoro)](../review-api.md#workflows)per ordinare e tenere traccia del contenuto, senza scrivere codice.
- Utilizza [i connettori](./configure.md#connectors) per elaborare il contenuto con i servizi Microsoft PhotoDNA, Text Analytics e Face oltre alle API di Content Moderator.
- Crea il tuo connettore per creare flussi di lavoro per qualsiasi API o processo aziendale.
- Ottenere le metriche di prestazioni chiave relative ai processi di moderazione del contenuto.

## <a name="review-tool-dashboard"></a>Dashboard dello strumento di revisione

Nella scheda **Dashboard,** puoi visualizzare le metriche chiave per le recensioni dei contenuti fatte all'interno dello strumento. Visualizza il numero di recensioni totali, complete e in sospeso per immagini, testo e contenuti video. Puoi anche visualizzare la suddivisione degli utenti e dei team che hanno completato le recensioni, nonché i tag di moderazione applicati.

![Visualizzare il dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Esaminare le credenziali dello strumentoReview tool credentials

Quando ti iscrivi con [lo strumento di revisione,](https://contentmoderator.cognitive.microsoft.com)ti verrà richiesto di selezionare un'area di Azure per l'account. Ciò è dovuto al fatto che [lo strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi di Azure Content Moderator. questa chiave è necessaria per accedere a qualsiasi servizio da una chiamata REST o da un SDK client. È possibile visualizzare l'URL della chiave e dell'endpoint API selezionando**Credenziali** **impostazioni** > .

![Credenziali di Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Configurare lo strumento Revisione](./configure.md) per informazioni su come accedere alle risorse dello strumento di revisione e modificare le impostazioni.