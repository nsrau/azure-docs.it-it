---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "81421813"
---
In questo argomento di avvio rapido si useranno [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e il servizio LUIS (Language Understanding) per riconoscere le finalità dai dati audio acquisiti da un microfono. In particolare, si userà Speech SDK per acquisire la voce e un dominio predefinito di LUIS per identificare le finalità per la domotica, come l'accensione e lo spegnimento di una luce. 

Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale e l'identificazione delle finalità da un microfono richiedono solo pochi passaggi:

> [!div class="checklist"]
>
> * Creare un oggetto `SpeechConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `IntentRecognizer` tramite l'oggetto `SpeechConfig` specificato.
> * Usando l'oggetto `IntentRecognizer`, avviare il processo di riconoscimento per una singola espressione.
> * Controllare l'elemento `IntentRecognitionResult` restituito.
