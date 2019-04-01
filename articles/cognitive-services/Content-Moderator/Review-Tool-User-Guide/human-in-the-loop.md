---
title: Informazioni sui concetti dello strumento di revisione - Content Moderator
titlesuffix: Azure Cognitive Services
description: Scopri lo strumento di revisione Content Moderator, un sito Web che coordina un impegno di moderazione di revisione umana e l'intelligenza artificiale combinato.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755865"
---
# <a name="content-moderator-review-tool"></a>Strumento di revisione di moderatore del contenuto

Azure Content Moderator fornisce servizi per combinare moderazione dei contenuti machine learning con revisione umana e il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) sito Web è un semplice front-end che fornisce accesso dettagliato a questi servizi.

![Il dashboard di strumento di revisione in un browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>Risultato

Il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), quando usate in combinazione con la moderazione automatica le API, è possibile eseguire le attività seguenti nel processo di moderazione dei contenuti:

- Usare un set di strumenti per moderare i contenuti in più formati (testo, immagini e video).
- Automatizzare la creazione di human [esamina](../review-api.md#reviews) quando API moderazione comporta tornare a questa pagina in.
- Assegnare o riassegnare le verifiche di contenuto a più team di revisione, organizzati in base a livello di esperienza o categoria del contenuto.
- Usare predefiniti o i filtri per la logica personalizzata ([flussi di lavoro](../review-api.md#workflows)) per ordinare e rilevare il contenuto, senza scrivere alcun codice.
- Uso [connettori](./configure.md#connectors) per elaborare il contenuto con Microsoft PhotoDNA testo Analitica e API viso oltre alle API Moderator contenuto.
- Compilare il proprio connettore per creare flussi di lavoro per le API o il processo di business.
- Ottenere le metriche di prestazioni chiave relative ai processi di moderazione del contenuto.

## <a name="review-tool-dashboard"></a>Dashboard dello strumento di revisione

Nel **Dashboard** scheda, è possibile visualizzare le metriche principali eseguite nello strumento di revisione del contenuto. Visualizzare il numero del totale, completa e in attesa di revisioni per immagini, testo e contenuto video. È anche possibile visualizzare la suddivisione di utenti e i team che sono state completate le revisioni, nonché i tag di moderazione che sono stati applicati.

![Visualizzare il dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Credenziali dello strumento di revisione

Quando effettua l'iscrizione con il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), verrà richiesto di selezionare un'area di Azure per tenere conto. Infatti, il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi di Azure Content Moderator; è necessario questa chiave per accedere a uno dei servizi da una chiamata REST o SDK client. È possibile visualizzare l'URL dell'endpoint API e chiave selezionando **le impostazioni** > **credenziali**.

![Credenziali di Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [configurare lo strumento di revisione](./configure.md) per informazioni su come accedere alle risorse dello strumento di revisione e modificare le impostazioni.