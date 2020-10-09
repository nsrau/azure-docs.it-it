---
title: Informazioni su Azure Kinect Sensor SDK
description: Panoramica del sensore Kinect di Azure Software Development Kit (SDK), delle funzionalità e degli strumenti.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, registrazione, sensore, SDK, accesso, profondità, video, fotocamera, IMU, movimento, sensore, audio, microfono, Matroska, Sensor SDK, download
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276730"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Informazioni su Azure Kinect Sensor SDK

Questo articolo fornisce una panoramica del sensore Kinect di Azure Software Development Kit (SDK), delle funzionalità e degli strumenti.

## <a name="features"></a>Funzionalità

Azure Kinect Sensor SDK fornisce l'accesso a basso livello multipiattaforma per i flussi di configurazione dei dispositivi e sensori hardware di Azure Kinect, tra cui:

- Accesso e controllo delle modalità della videocamera RGB (modalità IR passiva, oltre alle modalità di profondità dei campi visivi ampi e stretti) 
- Accesso e controllo della videocamera RGB, ad esempio esposizione e bilanciamento del bianco 
- Accesso ai sensori di movimento (giroscopio e accelerometro) 
- Streaming sincronizzato della videocamera RGB di profondità, con ritardo configurabile tra videocamere 
- Controllo della sincronizzazione di dispositivi esterni con offset del ritardo configurabile tra dispositivi 
- Accesso ai metadati dei fotogrammi della videocamera per la risoluzione delle immagine, il timestamp e così via 
- Accesso ai dati di calibrazione del dispositivo 

## <a name="tools"></a>Strumenti

- Un [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) per monitorare i flussi di dati del dispositivo e configurare modalità diverse.
- Un'API di registrazione e riproduzione di un lettore [Kinect di Azure](azure-kinect-recorder.md) che usa il [formato del contenitore Matroska](record-file-format.md).
- Uno [strumento di aggiornamento del firmware](azure-kinect-firmware-tool.md)di Kinect DK per Azure.

## <a name="sensor-sdk"></a>SDK del sensore

- [Scaricare il sensore SDK](sensor-sdk-download.md).
- Il sensore SDK è disponibile in [Open Source su GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Per ulteriori informazioni sull'utilizzo, vedere la [documentazione dell'API Sensor SDK](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come Azure Kinect Sensor SDK, è anche possibile:
>[!div class="nextstepaction"]
>[Scaricare il codice SDK del sensore](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Trova e Apri dispositivo](find-then-open-device.md)
