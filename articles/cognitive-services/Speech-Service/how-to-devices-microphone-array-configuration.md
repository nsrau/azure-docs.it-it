---
title: Come configurare una matrice di microfoni-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare un array di microfoni in modo che l'SDK di dispositivi vocali possa usarlo.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025062"
---
# <a name="how-to-configure-a-microphone-array"></a>Come configurare un array di microfoni

Questo articolo illustra come configurare una [matrice di microfoni](./speech-devices-sdk-microphone.md). Include l'impostazione dell'angolo di lavoro e il modo in cui selezionare il microfono usato per l'SDK per i dispositivi vocali.

L'SDK per dispositivi vocali funziona meglio con una matrice di microfoni progettata in base alle [linee guida](./speech-devices-sdk-microphone.md). La configurazione della matrice di microfoni può essere fornita dal sistema operativo o fornita tramite uno dei metodi seguenti.

Speech Devices SDK supporta inizialmente le matrici di microfoni selezionando da un set fisso di configurazioni.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

In Windows la configurazione della matrice microfonica viene fornita dal driver audio.

Da v 1.11.0, Speech Devices SDK supporta anche la configurazione da un [file JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
In Windows, le informazioni sulla geometria della matrice del microfono vengono ottenute automaticamente dal driver audio. Pertanto, le proprietà `DeviceGeometry` ,  `SelectedGeometry` e `MicArrayGeometryConfigFile` sono facoltative. Viene usato il [file JSON](https://aka.ms/sdsdk-micarray-json) fornito usando `MicArrayGeometryConfigFile` solo per ottenere l'intervallo di beamforming.

Se viene specificata una matrice di microfoni usando `AudioConfig::FromMicrophoneInput` , il microfono specificato verrà usato. Se un microfono non viene specificato o `AudioConfig::FromDefaultMicrophoneInput` viene chiamato, viene usato il microfono predefinito, specificato in impostazioni audio in Windows.
Lo stack audio Microsoft nell'SDK dei dispositivi vocali supporta solo il campionamento per le frequenze di campionamento che sono multipli integrali di 16 KHz.

## <a name="linux"></a>Linux
In Linux è necessario fornire le informazioni sulla geometria del microfono. L'utilizzo di `DeviceGeometry` e `SelectedGeometry` rimane supportato. Può anche essere fornito tramite il file JSON usando la `MicArrayGeometryConfigFile` Proprietà. Analogamente a Windows, l'intervallo di beamforming può essere fornito dal file JSON.

Se viene specificata una matrice di microfoni usando `AudioConfig::FromMicrophoneInput` , il microfono specificato verrà usato. Se non viene specificato o `AudioConfig::FromDefaultMicrophoneInput` viene chiamato un microfono, viene registrato dal dispositivo ALSA denominato *default*. Per impostazione predefinita, il *valore predefinito* fa sempre riferimento alla scheda 0 dispositivo 0, ma gli utenti possono modificarlo nel `asound.conf` file. 

Microsoft audio stack nell'SDK per dispositivi vocali supporta solo sottocampionando per le tariffe di esempio che sono multipli integrali di 16 KHz. Sono inoltre supportati i formati seguenti: IEEE little endian float a 32 bit, a 32 bit little endian con firma int, a 24 bit little endian con segno int, a 16 bit little endian con segno int e a 8 bit con segno int.

## <a name="android"></a>Android
Attualmente solo [Roobo V1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) è supportato da Speech Devices SDK. Il comportamento è uguale a quello delle versioni precedenti, a meno che ora la `MicArrayGeometryConfigFile` proprietà possa essere usata per specificare un file JSON contenente l'intervallo di beamforming.

## <a name="microphone-array-configuration-json"></a>JSON di configurazione della matrice microfonica

Il file JSON per la configurazione della geometria della matrice microfonica seguirà lo [schema JSON](https://aka.ms/sdsdk-micarray-json). Di seguito sono riportati alcuni esempi che seguono lo schema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Oppure


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Oppure

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere il dispositivo vocale](get-speech-devices-sdk.md)