---
title: Introduzione ad Azure IoT Edge (Windows) | Microsoft Docs
description: Come creare un gateway di Azure IoT Edge in un computer Windows e ottenere informazioni sui concetti chiave in Azure IoT Edge, ad esempio moduli e file di configurazione di JSON.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Esplorare l'architettura di Azure IoT Edge in Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Eseguire l'esempio

Lo script **build.cmd** genera l'output nella cartella **build** nella copia locale dell'archivio **iot-edge**. L'output include molti file ma l'esempio è incentrato su tre:
- Due moduli IoT Edge: **build\\modules\\logger\\Debug\\logger.dll** e **build\\modules\\hello_world\\Debug\\hello\_world.dll**. 
- Un file eseguibile: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. Il processo accetta un file di configurazione JSON come argomento della riga di comando.

Il quarto file usato in questo esempio non è nella cartella di compilazione, ma è stato incluso al momento della clonazione del repository iot-edge:
- Un file di configurazione JSON: **samples\\hello\_world\\src\\hello\_world\_win.json**. Questo file contiene i percorsi ai due moduli. Dichiara inoltre dove logger.dll scrive l'output. Il valore predefinito è **log.txt** nella directory di lavoro corrente. 

   >[!NOTE]
   >Se si spostano i moduli di esempio o se ne aggiungono per i test, aggiornare i valori di **module.path** nel file di configurazione per la corrispondenza. I percorsi dei moduli sono relativi alla directory in cui si trova il file **hello\_world\_sample.exe**. 

Per eseguire l'esempio, seguire questa procedura:

1. Accedere alla cartella **build** nella radice della copia locale dell'archivio **iot-edge**.

1. Eseguire il comando seguente:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. L'output seguente indica che l'esempio funziona correttamente:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Premere **Invio** per arrestare il processo.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
