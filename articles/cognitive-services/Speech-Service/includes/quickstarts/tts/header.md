---
title: 'Guida introduttiva: Eseguire la sintesi vocale - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818348"
---
In questo argomento di avvio rapido si userà [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per convertire il testo in sintesi vocale. Dopo aver soddisfatto alcuni prerequisiti, il rendering della voce sintetizzata negli altoparlanti predefiniti richiede solo quattro passaggi:
> [!div class="checklist"]
> * Creare un oggetto ````SpeechConfig```` dalla chiave e dall'area di sottoscrizione.
> * Creare un oggetto ````SpeechSynthesizer```` tramite l'oggetto ````SpeechConfig```` specificato.
> * Usare l'oggetto ````SpeechSynthesizer```` per pronunciare il testo.
> * Verificare la presenza di errori nell'oggetto ````SpeechSynthesisResult```` restituito.
