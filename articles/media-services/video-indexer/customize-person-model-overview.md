---
title: Personalizzare un modello Person con Video Indexer - Azure
titlesuffix: Azure Media Services
description: Questo articolo fornisce informazioni generali sul modello Person in Video Indexer e come personalizzarlo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c74b913fc3ac35039d914fc97c9c438d2e4a3069
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799436"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizzare un modello Person con Video Indexer

Video Indexer supporta il riconoscimento di celebrità nei video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. Visi non riconosciute da indicizzatore Video vengono comunque rilevati ma rimangono senza nome. I clienti possono creare modelli di persona personalizzati e consentire all'indicizzatore Video di riconoscere i visi non riconosciuti per impostazione predefinita. I clienti possono creare questi modelli persona mediante l'associazione di un nome di persona con i file di immagine del viso della persona.  

Se l'account si occupa dei diversi casi d'uso, è possibile trarre vantaggio dalla possibilità di creare più modelli di persona per ogni account. Ad esempio, se il contenuto nell'account deve essere ordinato in canali diversi, è possibile creare un modello di persona separato per ogni canale. 

> [!NOTE]
> Ogni modello di persona supporta fino a 1 milione di persone e ogni account prevede un limite di 50 modelli persona. 

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. Training di un nuovo viso per un video, aggiorna il modello personalizzato specifico che il video è stato associato. 

Se non è necessario il supporto di più modelli Persona, non assegnare un ID di modello Persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer userà il modello di utente predefinito nell'account. 

È possibile usare il sito Web di indicizzatore Video per modificare i volti rilevati in un video e gestire più modelli di persona personalizzati nell'account, come descritto nel [personalizzare un modello di persona con un sito Web](customize-person-model-with-website.md) argomento. È anche possibile usare l'API, come descritto in [personalizzare un modello di persona usando le API](customize-person-model-with-api.md).