---
title: 'Avvio rapido: Creare un assistente vocale personalizzato - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80241763"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per creare un'applicazione di assistente vocale personalizzato che si connette a un bot già creato e configurato. Se è necessario creare un bot, vedere l'[esercitazione correlata](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) per una guida più completa.

Dopo aver soddisfatto alcuni prerequisiti, la connessione dell'assistente vocale personalizzato richiede solo pochi passaggi:
> [!div class="checklist"]
> * Creare un oggetto `BotFrameworkConfig` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto `DialogServiceConnector` tramite l'oggetto `BotFrameworkConfig` specificato.
> * Usando l'oggetto `DialogServiceConnector`, avviare il processo di ascolto per una singola espressione.
> * Controllare l'elemento `ActivityReceivedEventArgs` restituito.
