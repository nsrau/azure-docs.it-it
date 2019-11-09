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
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838304"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizzare un modello Person con Video Indexer

Video Indexer supporta il riconoscimento celebrità nei video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che non sono riconosciuti da Video Indexer vengono comunque rilevati, ma rimangono senza nome. I clienti possono creare modelli person personalizzati e consentire Video Indexer di riconoscere i visi che non sono riconosciuti per impostazione predefinita. I clienti possono creare questi modelli di persona abbinando il nome di un utente con i file di immagine della faccia della persona.  

Se l'account è adatto a diversi casi d'uso, è possibile trarre vantaggio dalla possibilità di creare più modelli di persona per ogni account. Ad esempio, se il contenuto dell'account deve essere ordinato in canali diversi, potrebbe essere necessario creare un modello person separato per ogni canale. 

> [!NOTE]
> Ogni modello di persona supporta fino a 1 milione di persone e ogni account ha un limite di 50 modelli di persona. 

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. Il training di un nuovo volto per un video, aggiorna il modello personalizzato specifico a cui è stato associato il video. 

Se non è necessario il supporto di più modelli Persona, non assegnare un ID di modello Persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer utilizzerà il modello person predefinito nell'account. 

È possibile usare il sito Web Video Indexer per modificare i visi rilevati in un video e per gestire più modelli person personalizzati nell'account, come descritto nell'argomento [personalizzare un modello person usando un sito Web](customize-person-model-with-website.md) . È anche possibile usare l'API, come descritto in [personalizzare un modello di persona usando le API](customize-person-model-with-api.md).
