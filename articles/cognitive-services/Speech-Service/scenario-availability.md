---
title: Disponibilità dello scenario-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni di riferimento sulle aree del servizio Voce.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: af5bb7126af65a755cb1d58788d39cb8bdcbbb3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959032"
---
# <a name="scenario-availability"></a>Disponibilità dello scenario

L'SDK del servizio vocale presenta molti scenari in un'ampia gamma di linguaggi e ambienti di programmazione.  Non tutti gli scenari sono attualmente disponibili in tutti i linguaggi di programmazione o in tutti gli ambienti.  Di seguito è riportata la disponibilità di ogni scenario.

- **Riconoscimento vocale (SR), elenco di frasi, finalità, traduzione e contenitori locali**
  - Tutti i linguaggi di programmazione/ambienti in cui è presente un collegamento a freccia <img src="media/index/link.jpg" height="15" width="15"></img> nella tabella di avvio rapido [qui](https://aka.ms/csspeech).
- **Sintesi vocale (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE e Android)
  - Python
  - Swift
  - Objective-C
  - L'API REST TTS può essere usata in qualsiasi altra situazione.
- **Wake Word (keyword spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - La funzionalità di riattivazione Word (keyword spotter/KWS) può funzionare con qualsiasi tipo di microfono, il supporto ufficiale di KWS, tuttavia, è attualmente limitato agli array di microfoni trovati nell'hardware di Azure Kinect DK o nell'SDK per dispositivi vocali
- **Assistente virtuale per la prima voce**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Trascrizione conversazione**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
- **Trascrizione del Call Center**
  - API REST e può essere usata in qualsiasi situazione
- **Input audio compresso codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android e iOS
