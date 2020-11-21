---
title: Ottenere Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale funziona con un'ampia gamma di dispositivi e origini audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. In questo articolo si apprenderà come ottenere l'accesso all'SDK per i dispositivi vocali e iniziare a sviluppare.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026370"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Ottenere Speech Devices SDK di Servizi cognitivi

Speech Devices SDK è una libreria presintonizzata progettata per funzionare con un kit di sviluppo progettato per lo sviluppo e per diverse configurazioni di matrici di microfoni.

## <a name="choose-a-development-kit"></a>Scegliere un kit di sviluppo

|Dispositivi|Specifiche|Descrizione|Scenari|
|--|--|--|--|
|Kit di sviluppo [Urbetter](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 mic Array, ARM SOC, WIFI, Ethernet, HDMI, videocamera USB. <br>Linux|Un SDK per dispositivi vocali a livello di settore che adatta Microsoft MIC array e supporta l'I/O esteso, ad esempio HDMI/Ethernet e altre periferiche USB <br> [Contattare Urbetter](http://www.urbetter.com/products_56/278.html)|Trascrizione di conversazioni, istruzione, ospedale, robot, OTT box, agente vocale, Drive Thru|
|[Roobo smart audio Dev Kit](http://ddk.roobo.com)<br>[Configurazione](speech-devices-sdk-roobo-v1.md)  /  di [Guida introduttiva](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Roobo smart audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 mic Array, ARM SOC, WIFI, audio out, IO. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Il primo SDK per dispositivi vocali per adattare Microsoft MIC array e front processing SDK per lo sviluppo di scenari di traduzione e sintesi vocale di alta qualità|Trascrizione delle conversazioni, Smart speaker, agente vocale, indossabile|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Configurazione](../../kinect-dk/set-up-azure-kinect-dk.md)  /  di [Guida introduttiva](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 fotocamere RGB e profondità della matrice MIC. <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Kit per sviluppatori con sensori di intelligenza artificiale avanzati per la creazione di sofisticati modelli di visione artificiale e riconoscimento vocale. Combina una fotocamera di livello superiore e una fotocamera di profondità con una videocamera e un sensore di orientamento, il tutto in un piccolo dispositivo con più modalità, opzioni e SDK per gestire una gamma di tipi di calcolo.|Trascrizione di conversazioni, Robotica, compilazione intelligente|
|Roobo smart audio Dev Kit 2<br>[Configurazione](speech-devices-sdk-roobo-v2.md)<br>![Roobo smart audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|L'SDK per dispositivi vocali di seconda generazione che fornisce un sistema operativo alternativo e altre funzionalità in una progettazione di riferimento economicamente conveniente.|Trascrizione delle conversazioni, Smart speaker, agente vocale, indossabile|


## <a name="download-the-speech-devices-sdk"></a>Scaricare Speech Devices SDK

Scaricare l' [SDK per i dispositivi vocali](./speech-devices-sdk.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione a Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)