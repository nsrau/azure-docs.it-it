---
title: Risolvere i problemi dei dispositivi di Speech SDK - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Questo articolo fornisce informazioni che consentono di risolvere i problemi riscontrati quando si usa il SDK di dispositivi di riconoscimento vocale.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606307"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Risolvere i problemi di Speech Devices SDK

Questo articolo fornisce informazioni che consentono di risolvere i problemi riscontrati quando si usa il SDK di dispositivi di riconoscimento vocale.

## <a name="certificate-failures"></a>Errori di certificato

Se si verificano errori del certificato quando si usano i servizi di riconoscimento vocale, assicurarsi che il dispositivo abbia corretti di data e ora:

1. Passare a **Impostazioni**. In **System** (Sistema) selezionare **Date & time** (Data e ora).

    ![In Settings (Impostazioni) selezionare Date & time (Data e ora)](media/speech-devices-sdk/qsg-12.png)

1. Mantenere selezionata l'opzione **Automatic date & time** (Data e ora automatiche). In **Select time zone** (Seleziona il fuso orario) selezionare il fuso orario corrente.

    ![Opzioni per la selezione della data e del fuso orario](media/speech-devices-sdk/qsg-13.png)

    Dopo aver verificato che l'ora del kit di sviluppo corrisponde a quella del PC, il kit di sviluppo è connesso a Internet.

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare le note sulla versione](devices-sdk-release-notes.md)
