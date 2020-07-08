---
title: Usare le funzioni di calibrazione Kinect di Azure
description: Informazioni su come usare le funzioni di calibrazione per Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, sistema di coordinate, calibrazione, funzioni, fotocamera, intrinseco, estrinseco, progetto, Unproject, trasformazione, RGB-d, punto cloud
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276685"
---
# <a name="use-azure-kinect-calibration-functions"></a>Usare le funzioni di calibrazione Kinect di Azure

Le funzioni di calibrazione consentono la trasformazione dei punti tra i sistemi di coordinate di ciascun sensore nel dispositivo Kinect di Azure. Le applicazioni che richiedono la conversione di immagini intere possono sfruttare le operazioni accelerate disponibili nelle [funzioni di trasformazione](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Recuperare i dati di calibrazione

È necessario recuperare la calibrazione del dispositivo per eseguire le trasformazioni del sistema di coordinate. I dati di calibrazione vengono archiviati nel tipo di dati [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) . Viene ottenuto dal dispositivo tramite la funzione [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). I dati di calibrazione non sono specifici solo per ogni dispositivo, ma anche per la modalità operativa delle fotocamere. Di conseguenza [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) richiede `depth_mode` i `color_resolution` parametri e come input.

### <a name="opencv-compatibility"></a>Compatibilità con OpenCV

I parametri di calibrazione sono compatibili con [OpenCV](https://opencv.org/). Per ulteriori informazioni sui singoli parametri di calibrazione della fotocamera, vedere anche la [documentazione di OpenCV](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Vedere anche [esempio di compatibilità OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) dell'SDK che illustra la conversione tra il tipo di [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) e le strutture di dati OpenCV corrispondenti.

## <a name="coordinate-transformation-functions"></a>Funzioni di trasformazione delle coordinate

La figura seguente illustra i diversi sistemi di coordinate di Kinect di Azure e le funzioni per la conversione tra di essi. Vengono omessi i sistemi di coordinate 3D del giroscopio e dell'accelerometro per semplificare la figura.

   ![Trasformazione coordinata](./media/how-to-guides/coordinate-transformation.png)

Evidenziare la distorsione della lente: le coordinate 2D fanno sempre riferimento all'immagine distorta nell'SDK. L' [esempio di disdistorsione](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) dell'SDK illustra l'indistorsione dell'immagine. In generale, i punti 3D non saranno mai interessati dalla distorsione dell'obiettivo.

### <a name="convert-between-3d-coordinate-systems"></a>Eseguire la conversione tra sistemi di coordinate 3D

La funzione [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) converte un punto 3D del sistema di coordinate di origine in un punto 3D del sistema di coordinate di destinazione usando la taratura estrinseca della fotocamera. L'origine e la destinazione possono essere impostate su uno dei quattro sistemi di coordinate 3D, ovvero la fotocamera a colori, la videocamera di profondità, il giroscopio o l'accelerometro. Se l'origine e la destinazione sono identiche, il punto 3D di input non modificato viene restituito come output.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Eseguire la conversione tra sistemi di coordinate 2D e 3D

La funzione [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) converte un punto 3D del sistema di coordinate di origine in una coordinata di pixel 2D della fotocamera di destinazione. Questa funzione è spesso definita funzione del progetto. Mentre l'origine può essere impostata su uno dei quattro sistemi di coordinate 3D, la destinazione deve essere la fotocamera di profondità o colore. Se l'origine e la destinazione sono diverse, il punto 3D di input viene convertito nel sistema di coordinate 3D della fotocamera di destinazione usando [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Quando il punto 3D è rappresentato nel sistema di coordinate della fotocamera di destinazione, le coordinate corrispondenti dei pixel 2D vengono calcolate usando la taratura intrinseca della fotocamera di destinazione. Se un punto 3D scende dall'area visibile della fotocamera di destinazione, il valore valido è impostato su 0.

La funzione [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) converte una coordinata di pixel 2D della fotocamera di origine in un punto 3D del sistema di coordinate della fotocamera di destinazione. L'origine deve essere una fotocamera con colore o profondità. La destinazione può essere impostata su uno qualsiasi dei quattro sistemi di coordinate 3D. Oltre alla coordinata 2D pixel, il valore di profondità del pixel (in millimetri) nell'immagine della fotocamera di origine è necessario come input per la funzione, un modo per derivare il valore di profondità nella geometria della fotocamera a colori consiste nell'usare la funzione [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). La funzione calcola il raggio 3D che conduce dal punto focale della fotocamera di origine attraverso la coordinata pixel specificata usando la calibrazione intrinseca della fotocamera di origine. Il valore depth viene quindi usato per trovare la posizione esatta del punto 3D in questo raggio. Questa operazione viene spesso definita funzione Unproject. Se le fotocamere di origine e di destinazione sono diverse, la funzione trasforma il punto 3D nel sistema di coordinate della destinazione tramite [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Se una coordinata 2D pixel esce dall'area visibile della fotocamera di origine, il valore valido viene impostato su 0.

### <a name="converting-between-2d-coordinate-systems"></a>Conversione tra sistemi di coordinate 2D

La funzione [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) converte una coordinata di pixel 2D della fotocamera di origine in una coordinata pixel 2D della fotocamera di destinazione. L'origine e la destinazione devono essere impostate su colore o fotocamera di profondità. La funzione richiede il valore di profondità del pixel (in millimetri) nell'immagine della fotocamera di origine come input, un modo per derivare il valore di profondità nella geometria della fotocamera dei colori consiste nell'usare la funzione [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Chiama [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) per eseguire la conversione in un punto 3D del sistema della fotocamera di origine. Chiama quindi [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) per eseguire la conversione in una coordinata di pixel 2D dell'immagine della fotocamera di destinazione. Il valore valido è impostato su 0, se [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) o [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) restituisce un risultato non valido.

## <a name="related-samples"></a>Esempi correlati

- [Esempio di compatibilità OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Esempio di disdistorsione](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Esempio di Cloud Point veloce](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono le tarature della fotocamera, è anche possibile apprendere come
>[!div class="nextstepaction"]
>[Acquisire la sincronizzazione dei dispositivi](capture-device-synchronization.md)

È anche possibile esaminare

[Sistemi di coordinate](coordinate-systems.md)
