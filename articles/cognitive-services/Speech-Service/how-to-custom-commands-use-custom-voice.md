---
title: 'Procedura: usare comandi personalizzati con un servizio di riconoscimento vocale personalizzato'
titleSuffix: Azure Cognitive Services
description: In questo articolo si specificherà la voce di output di un'applicazione Commands personalizzata.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294299"
---
# <a name="use-custom-commands-with-custom-voice"></a>Usare Comandi personalizzati con Riconoscimento vocale personalizzato

In questo articolo si apprenderà come selezionare una voce di output personalizzata per un'applicazione comandi personalizzata.

## <a name="select-a-custom-voice"></a>Selezionare una voce personalizzata

1. Nell'applicazione comandi personalizzati selezionare **Impostazioni** dal riquadro sinistro.
1. Selezionare **voce personalizzata** dal riquadro centrale.
1. Selezionare la voce personalizzata o pubblica desiderata dalla tabella.
1. Selezionare **Salva**.

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Per le **voci pubbliche**, i **tipi neurali** sono disponibili solo per aree specifiche. Per verificare la disponibilità, vedere [le voci standard e neurali per area/endpoint](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Per le **voci personalizzate**, è possibile crearle dalla pagina del progetto Voice personalizzato. Vedere [Introduzione alla voce personalizzata](./how-to-custom-voice.md).

A questo punto, l'applicazione risponderà alla voce selezionata, anziché alla voce predefinita.
