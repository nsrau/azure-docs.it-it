---
title: Come scegliere la modalità di riconoscimento di Riconoscimento vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Come scegliere la modalità di riconoscimento più adatta di Riconoscimento vocale Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: ed270ed19959240bc1b90ba6171792cf4369e273
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221596"
---
# <a name="bing-speech-recognition-modes"></a>Modalità di riconoscimento di Riconoscimento vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Le API Riconoscimento vocale Bing supportano varie modalità di riconoscimento vocale. Scegliere la modalità che offre i risultati di riconoscimento più adatti per l'applicazione.

| Mode | DESCRIZIONE |
|---|---|
| *interactive* | Riconoscimento "Comando e controllo" per gli scenari di applicazione utente interattiva. Gli utenti pronunciano frasi brevi che fungono da comandi per un'applicazione. |
| *dictation* | Scenari di riconoscimento continuo per la dettatura. Gli utenti pronunciano frasi più lunghe che vengono visualizzate come testo. Gli utenti adottano un modo di parlare più formale. |
| *conversation* | Riconoscimento continuo per la trascrizione delle conversazioni degli utenti. Gli utenti adottano un modo di parlare meno formale e possono usare sia frasi brevi che frasi lunghe.

> [!NOTE]
> Queste modalità sono applicabili quando si usano le [API REST](../GetStarted/GetStartedREST.md). Le [librerie client](../GetStarted/GetStartedClientLibraries.md) usano parametri diversi per specificare la modalità di riconoscimento. Per altre informazioni, vedere la libreria client scelta.

Per altre informazioni, vedere la pagina [Modalità di riconoscimento](../concepts.md#recognition-modes).
