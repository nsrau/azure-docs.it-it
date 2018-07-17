---
title: Come scegliere la modalità di riconoscimento | Microsoft Docs
description: Come scegliere la modalità di riconoscimento più adatta.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373793"
---
# <a name="speech-recognition-modes"></a>Modalità di riconoscimento vocale

Le API *Riconoscimento vocale* di Microsoft supportano varie modalità di riconoscimento vocale. Scegliere la modalità che offre i risultati di riconoscimento più adatti per l'applicazione.

| Mode | DESCRIZIONE |
|---|---|
| *interactive* | Riconoscimento "Comando e controllo" per gli scenari di applicazione utente interattiva. Gli utenti pronunciano frasi brevi che fungono da comandi per un'applicazione. |
| *dictation* | Scenari di riconoscimento continuo per la dettatura. Gli utenti pronunciano frasi più lunghe che vengono visualizzate come testo. Gli utenti adottano un modo di parlare più formale. |
| *conversation* | Riconoscimento continuo per la trascrizione delle conversazioni degli utenti. Gli utenti adottano un modo di parlare meno formale e possono usare sia frasi brevi che frasi lunghe.

> [!NOTE]
> Queste modalità sono applicabili quando si usano le [API REST](../GetStarted/GetStartedREST.md). Le [librerie client](../GetStarted/GetStartedClientLibraries.md) usano parametri diversi per specificare la modalità di riconoscimento. Per altre informazioni, vedere la libreria client scelta.

Per altre informazioni, vedere la pagina [Modalità di riconoscimento](../concepts.md#recognition-modes).
