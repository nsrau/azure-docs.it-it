---
title: Azure Kinect Firmware Tool
description: Informazioni su come eseguire una query e aggiornare il firmware del dispositivo usando lo strumento firmware di Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, aggiornamento
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276718"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Strumento firmware di Azure Kinect DK

Lo strumento firmware Kinect di Azure può essere usato per eseguire query e aggiornare il firmware del dispositivo di Azure Kinect DK.

## <a name="list-connected-devices"></a>Elencare i dispositivi connessi

È possibile ottenere un elenco dei dispositivi connessi usando l'opzione-l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Controllare la versione del firmware del dispositivo

È possibile controllare le versioni del firmware correnti del primo dispositivo collegato utilizzando l'opzione-q, ad esempio `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Se è collegato più di un dispositivo, è possibile specificare il dispositivo su cui si vuole eseguire la query aggiungendo il numero di serie completo al comando, ad esempio:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Aggiornare il firmware del dispositivo

L'uso più comune di questo strumento consiste nell'aggiornare il firmware del dispositivo. Eseguire l'aggiornamento chiamando lo strumento utilizzando l' `-u` opzione. Un aggiornamento del firmware può richiedere alcuni minuti, a seconda dei file del firmware che devono essere aggiornati.

Per istruzioni dettagliate sull'aggiornamento del firmware, vedere aggiornamento del [firmware Kinect di Azure](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Se è collegato più di un dispositivo, è possibile specificare il dispositivo su cui si vuole eseguire la query aggiungendo il numero di serie completo al comando.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Reimpostare il dispositivo

È possibile reimpostare un oggetto DK Kinect di Azure collegato usando l'opzione-r, se è necessario ottenere il dispositivo in uno stato noto.

Se è collegato più di un dispositivo, è possibile specificare il dispositivo su cui si vuole eseguire la query aggiungendo il numero di serie completo al comando.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Esaminare il firmware

Il controllo del firmware consente di ottenere le informazioni sulla versione da un file bin del firmware prima di aggiornare un dispositivo effettivo.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Opzioni dello strumento di aggiornamento del firmware

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Istruzioni dettagliate per l'aggiornamento del firmware del dispositivo](update-device-firmware.md)
