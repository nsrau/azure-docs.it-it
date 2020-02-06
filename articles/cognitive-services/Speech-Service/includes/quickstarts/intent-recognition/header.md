---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900463"
---
In questo argomento di avvio rapido si useranno [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e il servizio LUIS (Language Understanding) per riconoscere le finalità dai dati audio acquisiti da un microfono. In particolare, si userà Speech SDK per acquisire la voce e un dominio predefinito di LUIS per identificare le finalità per la domotica, come l'accensione e lo spegnimento di una luce. 

Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale e l'identificazione delle finalità da un microfono richiedono solo pochi passaggi:

> [!div class="checklist"]
>
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `IntentRecognizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usando l'oggetto `IntentRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `IntentRecognitionResult` restituito.
