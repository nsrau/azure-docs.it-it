---
title: Confronto tra Windows Kinect DK per Azure
description: Differenze di hardware e software tra Kinect DK di Azure e Kinect per Windows V2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, V2, Kinect di Azure, confronto, SDK, differenze, hardware, software
ms.openlocfilehash: ec105cef0c52b02c763487fbe5b2d8c018315f4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67452514"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Confronto tra Kinect e Kinect per Windows V2 di Azure

L'hardware e il Software Development Kit di Azure Kinect DK presentano differenze rispetto a Kinect per Windows V2. Tutte le applicazioni Kinect esistenti per Windows V2 non funzioneranno direttamente con Azure Kinect DK e richiederanno il porting al nuovo SDK.  

## <a name="hardware"></a>Hardware

Nella tabella seguente sono elencate le differenze di alto livello tra il kit di sviluppo Kinect di Azure e Kinect per Windows V2.

|    |      | Azure Kinect DK |  Kinect per Windows V2 |
|----------|---------------|--------| ------------|
| **Audio** | Dettagli  | 7-matrice circolare MIC | matrice a fasi lineare da 4 Mic |
| **Sensore di movimento** | Dettagli | 3-asse accelerometro 3-giroscopio asse | accelerometro a 3 assi |
| **Fotocamera RGB**    | Dettagli | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **Fotocamera di profondità**  | Metodo   | Ora del volo | Ora del volo |
|                   | Soluzione | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **Connettività** | Data | USB 3.1 gen 1 con tipo USB-C  | USB 3,1 gen 1|
|  | Potenza | ALIMENTATORE esterno o USB-C | PSU esterno |
|  | Sincronizzazione | Profondità & RGB interna, dispositivo esterno a dispositivo| RGB & Depth solo interno |
| **Meccanico** | Dimensioni | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Massa | 440 g | 970 g |
| | Montaggio | Un UNC 1/4-20. Quattro punti di viti interni | Un UNC 1/4-20 |

Ulteriori dettagli sono disponibili nel documento [hardware di Azure Kinect dk](hardware-specification.md) .

## <a name="sensor-access"></a>Accesso ai sensori

La tabella seguente fornisce un confronto delle funzionalità di accesso ai sensori di dispositivo di basso livello.

| **Funzionalità**| **Kinect di Azure** | **Kinect per Windows** | **Note** |
|---------|---------|------------|---------|
| **Depth** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **Colore** | ✔️ | ✔️ | Il formato colori supporta le differenze, il DK di Azure Kinect supporta questi controlli della fotocamera: esposizione, bilanciamento del bianco, luminosità, contrasto, saturazione, nitidezza e controllo di guadagno |
| **Audio** | ✔️ | ✔️ | È possibile accedere ai microfoni di Azure Kinect DK con l'SDK vocale o l'API nativa di Windows |
| **IMU** | ✔️ |  | Azure Kinect DK è dotato di un IMU completo a 6 assi e Kinect per Windows offre solo asse 1 |
| **Dati di calibrazione** | ✔️ | ✔️ | Calibrazione modello fotocamera compatibile con OpenCV |
| **Profondità-sincronizzazione interna RGB** | ✔️ | ✔️ |  |
| **Sincronizzazione esterna**| ✔️|  | Azure Kinect DK consente un ritardo programmabile per la sincronizzazione esterna |
| **Condividere l'accesso con più client** | | ✔️ | Azure Kinect Sensor SDK si basa su WinUSB/libUSB per accedere al dispositivo e non dispone di un servizio implementato per consentire la condivisione dell'accesso ai dispositivi con più processi |
| **Strumento di registrazione/riproduzione del flusso** | ✔️ | ✔️ | Azure Kinect DK usa un'implementazione basata su contenitori Matroska Open Source |

## <a name="features"></a>Funzionalità

Il set di funzionalità di Azure Kinect SDK è diverso da Kinect per Windows V2, come descritto di seguito:

| **Funzionalità Kinect V2** | **Tipo di dati Kinect V2** | **SDK/servizio Kinect di Azure** |
|--------|--------|------|
| Accesso ai dati dei sensori |DepthFrame| [SDK per i sensori-recuperare le immagini](retrieve-images.md) 
| |InfraredFrame | [SDK per i sensori-recuperare le immagini](retrieve-images.md) 
| | ColorFrame | [SDK per i sensori-recuperare le immagini](retrieve-images.md) | 
| | AudioBeamFrame |Attualmente non supportato 
| Rilevamento del corpo | BodyFrame | Body Tracking SDK |
| | BodyIndexFrame | Body Tracking SDK  |
| Mapping delle coordinate|CoordinateMapper| [SDK del sensore-trasformazioni di immagini](use-image-transformation.md) |
|Rilevamento viso | FaceFrame | [Servizi cognitivi: viso](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Riconoscimento vocale    |    N/D                      |    [Servizi cognitivi: sintesi vocale](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Passaggi successivi

[Pagine per sviluppatori di Kinect per Windows](https://developer.microsoft.com/windows/kinect)
