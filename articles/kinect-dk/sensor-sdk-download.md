---
title: Download di Azure Kinect Sensor SDK
description: Informazioni su come scaricare e installare Azure Kinect Sensor SDK in Windows e Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, download dell'aggiornamento, più recente, disponibile, installazione
ms.openlocfilehash: 48cdd35a80d68a7ec0d900639c0fca3a2c66787e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171789"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download di Azure Kinect Sensor SDK

Questa pagina include i collegamenti di download per ogni versione di Azure Kinect Sensor SDK. Il programma di installazione include tutti i file necessari per sviluppare per Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Contenuto di Azure Kinect Sensor SDK

- Intestazioni e librerie per creare un'applicazione con Azure Kinect DK.
- DLL ridistribuibili necessarie per le applicazioni che usano Azure Kinect DK.
- [Visualizzatore di Azure Kinect](azure-kinect-viewer.md).
- [Registratore di Azure Kinect](azure-kinect-recorder.md).
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Collegamento di download per Windows

[Programma di installazione Microsoft](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe) | [Codice sorgente GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Quando si installa l'SDK, ricordare il percorso in cui si esegue l'installazione, ad esempio"C:\Programmi\Azure Kinect SDK 1.2". In questo percorso sono disponibili gli strumenti a cui si fa riferimento negli articoli.

Le versioni precedenti di Azure Kinect Sensor SDK e del firmware relativo sono disponibili in [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Istruzioni per l'installazione in Linux

Attualmente, l'unica distribuzione supportata è Ubuntu 18.04. Per richiedere il supporto per altre distribuzioni, vedere [questa pagina](https://aka.ms/azurekinectfeedback).

Prima di tutto, è necessario configurare il [repository pacchetti di Microsoft](https://packages.microsoft.com/), seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

A questo punto, è possibile installare i pacchetti necessari. Il pacchetto `k4a-tools` include il [visualizzatore di Azure Kinect](azure-kinect-viewer.md), il [registratore di Azure Kinect](record-sensor-streams-file.md) e [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Per installarlo, eseguire

 `sudo apt install k4a-tools`

 Il pacchetto `libk4a<major>.<minor>-dev` contiene le intestazioni e i file CMake da compilare con `libk4a`.
Il pacchetto `libk4a<major>.<minor>` contiene gli oggetti condivisi necessari per eseguire i file eseguibili che dipendono da `libk4a`.

 Per le esercitazioni di base è richiesto il pacchetto `libk4a<major>.<minor>-dev`. Per installarlo, eseguire

 `sudo apt install libk4a1.1-dev`

Se il comando riesce, l'SDK è pronto per l'uso.

## <a name="change-log-and-older-versions"></a>Log delle modifiche e versioni precedenti

Il log delle modifiche per Azure Kinect Sensor SDK è disponibile [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Le versioni precedenti di Azure Kinect Sensor SDK sono disponibili [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Passaggi successivi

[Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)
