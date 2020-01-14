---
title: 'Avvio rapido: Riconoscere la voce, le finalità e le entità - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660512"
---
In questo argomento di avvio rapido si useranno [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e il servizio LUIS (Language Understanding) per riconoscere le finalità dai dati audio acquisiti da un microfono. In particolare, si userà Speech SDK per acquisire la voce e un dominio predefinito di LUIS per identificare le finalità per la domotica, come l'accensione e lo spegnimento di una luce. 

Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale e l'identificazione delle finalità da un microfono richiedono solo pochi passaggi:

> [!div class="checklist"]
>
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````IntentRecognizer```` tramite l'oggetto ````SpeechConfig```` specificato.
> * Usando l'oggetto ````IntentRecognizer````, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento ````IntentRecognitionResult```` restituito.
