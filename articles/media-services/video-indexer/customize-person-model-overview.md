---
title: Personalizzare un modello Person con Video Indexer - Azure
titlesuffix: Azure Media Services
description: Questo articolo fornisce informazioni generali sul modello Person in Video Indexer e come personalizzarlo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283237"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizzare un modello Person con Video Indexer


Video Indexer supporta il rilevamento viso e il riconoscimento dei personaggi famosi nei contenuti video. La funzionalità di riconoscimento dei personaggi famosi copre circa un milione di visi basandosi su origini dati di uso più comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che la funzionalità di riconoscimento dei personaggi famosi non riconosce vengono rilevati, ma lasciati senza nome. Dopo aver caricato il video di proprio interesse in Video Indexer e aver ottenuto i risultati, è possibile tornare indietro e assegnare un nome ai visi che non sono stati riconosciuti. Dopo aver etichettato un viso con un nome, il viso e il nome vengono aggiunti al modello Person del proprio account. Video Indexer riconoscerà quindi questo viso in tutti i video futuri e precedenti.

È possibile usare il sito Web o l'API Video Indexer per modificare i visi rilevati in un video, come descritto nei seguenti argomenti:

- [Customize Person model using APIs](customize-person-model-with-api.md) (Personalizzare il modello Person usando le API)
- [Customize Person model using website](customize-person-model-with-website.md) (Personalizzare il modello Person usando il sito Web)
