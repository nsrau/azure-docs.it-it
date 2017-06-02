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
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 117b949d4f8b84fe5ce43fc1dc38e3326bcfcfba
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Esplorare l'architettura di Azure IoT Edge in Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per l'uso dell'SDK in Windows.

1. Aprire un **prompt dei comandi per gli sviluppatori per VS 2015** o un **prompt dei comandi per gli sviluppatori per VS 2017**.
1. Accedere alla cartella radice nella copia locale del repository **iot-edge**.
1. Eseguire lo script **tools\\build.cmd**. Questo script crea un file di soluzione Visual Studio e compila la soluzione. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **iot-edge**. Si possono specificare nello script parametri aggiuntivi per compilare ed eseguire unit test e test end-to-end. Questi parametri sono rispettivamente **--run-unittests** e **--run-e2e-tests**.

## <a name="how-to-run-the-sample"></a>Per eseguire l'esempio

1. Lo script **build.cmd** crea una cartella denominata **build** nella copia locale del repository. Questa cartella contiene i due moduli IoT Edge usati in questo esempio.

    Lo script di compilazione inserisce **logger.dll** nella cartella **build\\modules\\logger\\Debug** e **hello\_world.dll** nella cartella **build\\modules\\hello_world\\Debug**. Usare questi percorsi per i valori **module path**, come illustrato nel file di impostazioni JSON seguente.
1. Il processo hello\_world\_sample accetta il percorso di un file di configurazione JSON come argomento della riga di comando. Il file JSON di esempio seguente è disponibile nel repository dell'SDK in **samples\\hello\_world\\src\\hello\_world\_win.json**. Questo file di configurazione funziona così com'è, a meno che non si modifichi lo script di compilazione per inserire moduli o file eseguibili di esempio in percorsi non predefiniti.

   > [!NOTE]
   > I percorsi dei moduli sono relativi alla directory in cui si trova il file hello\_world\_sample.exe. Per impostazione predefinita, il file di configurazione JSON di esempio prevede la scrittura del file "log.txt" nella directory di lavoro corrente.

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. Accedere alla cartella radice della copia locale del repository **iot-edge**.

1. Eseguire il comando seguente:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

