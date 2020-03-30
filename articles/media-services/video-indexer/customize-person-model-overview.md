---
title: Personalizzare un modello Person con Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo fornisce informazioni generali sul modello Person in Video Indexer e come personalizzarlo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838304"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizzare un modello Person con Video Indexer

L'indicizzatore video supporta il riconoscimento delle celebrità nei tuoi video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. Le facce che non sono riconosciute dall'indicizzatore video vengono ancora rilevate ma vengono lasciate senza nome. I clienti possono creare modelli Person personalizzati e abilitare l'indicizzatore video per riconoscere i volti non riconosciuti per impostazione predefinita. I clienti possono creare questi modelli di persona associando il nome di una persona ai file di immagine del volto della persona.  

Se il tuo account si rivolge a diversi casi d'uso, puoi trarre vantaggio dalla possibilità di creare più modelli di persona per account. Ad esempio, se il contenuto del tuo account deve essere ordinato in canali diversi, potresti voler creare un modello Persona separato per ogni canale. 

> [!NOTE]
> Ogni modello di persona supporta fino a 1 milione di persone e ogni account ha un limite di 50 modelli di persona. 

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. Il training di un nuovo volto per un video aggiorna il modello personalizzato specifico a cui è stato associato il video. 

Se non è necessario il supporto di più modelli Persona, non assegnare un ID di modello Persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer utilizzerà il modello Persona predefinito nel tuo account. 

Puoi utilizzare il sito Web Indicizzatore video per modificare i volti rilevati in un video e per gestire più modelli di persona personalizzati nel tuo account, come descritto nell'argomento [Personalizzare una persona utilizzando un sito Web.](customize-person-model-with-website.md) È anche possibile usare l'API, come descritto in [Personalizzare il modello Person usando le API](customize-person-model-with-api.md).
