---
title: 'Avvio rapido: Registrare i flussi dei sensori di Azure Kinect in un file'
description: Questo argomento di avvio rapido illustra come registrare i flussi di dati di Sensor SDK in un file.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, registrare, riprodurre, lettore, matroska, mkv, flussi, profondità, rgb, videocamera, colore, imu, audio, sensore
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277833"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Avvio rapido: Registrare i flussi dei sensori di Azure Kinect in un file

Questo argomento di avvio rapido fornisce informazioni su come usare lo strumento [registratore di Azure Kinect](azure-kinect-recorder.md) per registrare i flussi di dati di Sensor SDK in un file.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questo argomento di avvio rapido presuppone che:

- Azure Kinect DK sia connesso al PC host e alimentato correttamente.
- Sia stata completata la [configurazione](set-up-azure-kinect-dk.md) dell'hardware.

## <a name="create-recording"></a>Creare la registrazione

1. Aprire un prompt dei comandi e specificare il percorso del [registratore di Azure Kinect](azure-kinect-recorder.md), che si trova nella directory degli strumenti installati con il nome `k4arecorder.exe`. Ad esempio: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Registrare 5 secondi.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Per impostazione predefinita, il registratore usa la modalità di profondità NFOV Unbinned e genera output RGB 1080p a 30 fps, inclusi i dati IMU. Per una panoramica completa delle opzioni e alcuni suggerimenti per la registrazione, vedere [Registratore di Azure Kinect](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Riprodurre la registrazione

Per riprodurre una registrazione, è possibile usare il [visualizzatore di Azure Kinect](azure-kinect-viewer.md).

1. Avviare [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Espandere la scheda **Open Recording** (Apri registrazione) e aprire la registrazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come registrare i flussi dei sensori in un file, è il momento di

> [!div class="nextstepaction"]
> [Creare la prima applicazione Azure Kinect DK](build-first-app.md)
