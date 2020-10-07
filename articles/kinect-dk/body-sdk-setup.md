---
title: 'Avvio rapido: Configurare il rilevamento del corpo di Azure Kinect DK'
description: Questo argomento di avvio rapido illustra come configurare Body Tracking SDK per Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensore, accesso, profondità, sdk, rilevamento, corpo, articolazione, configurazione, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277850"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Avvio rapido: Configurare il rilevamento del corpo di Azure Kinect

Questo argomento di avvio rapido illustra la procedura per eseguire il rilevamento del corpo in Azure Kinect DK.

## <a name="system-requirements"></a>Requisiti di sistema

Body Tracking SDK richiede una GPU NVIDIA installata nel PC host. Il requisito consigliato del PC host di rilevamento del corpo è descritto nella pagina [Requisiti di sistema](system-requirements.md).

## <a name="install-software"></a>Installazione del software

### <a name="install-the-latest-nvidia-driver"></a>[Installare il driver NVIDIA più recente](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Scaricare e installare il driver NVIDIA più recente per la scheda grafica. I driver meno recenti potrebbero non essere compatibili con i file binari CUDA ridistribuiti con Body Tracking SDK.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual C++ Redistributable per Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Scaricare e installare Microsoft Visual C++ Redistributable per Visual Studio 2015. 

## <a name="set-up-hardware"></a>Configurare l'hardware

### <a name="set-up-azure-kinect-dk"></a>[Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)

Avviare il [visualizzatore di Azure Kinect](azure-kinect-viewer.md) per verificare che Azure Kinect DK sia configurato correttamente.

## <a name="download-the-body-tracking-sdk"></a>Scaricare Body Tracking SDK
 
1. Selezionare il collegamento per [scaricare Body Tracking SDK](body-sdk-download.md)
2. Installare Body Tracking SDK nel PC.

## <a name="verify-body-tracking"></a>Verificare il rilevamento del corpo

Avviare il **visualizzatore di rilevamento del corpo di Azure Kinect** per verificare che Body Tracking SDK sia configurato correttamente. Il visualizzatore viene installato con il programma di installazione MSI dell'SDK. È possibile trovarlo nel menu Start o in `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe`.

Se la GPU non è sufficientemente potente e si vuole comunque testare il risultato, è possibile avviare il **visualizzatore di rilevamento del corpo di Azure Kinect** nella riga di comando con il comando seguente: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Se tutte le impostazioni sono configurate correttamente, verrà visualizzata una finestra con una nuvola di punti 3D e i corpi rilevati.


![Visualizzatore 3D di rilevamento del corpo](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Esempi

È possibile trovare gli esempi su come usare Body Tracking SDK [qui](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Creare la prima applicazione di rilevamento del corpo](build-first-body-app.md)

