---
title: Introduzione all&quot;hub IoT SDK per gateway (Linux) | Microsoft Docs
description: Come creare un gateway in un computer Linux e ottenere informazioni sui concetti chiave in Azure IoT SDK per gateway, ad esempio moduli e file di configurazione di JSON.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 856ffeeeb8f9d8296ba972a9e070686171f7fde8
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Esplorare l'architettura di IoT Gateway SDK in Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Linux.

1. Aprire una shell.
1. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
1. Eseguire lo script **tools/build.sh** . Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella directory principale della copia locale del repository **azure-iot-gateway-sdk** e generare un makefile. Lo script quindi compila la soluzione senza eseguire i test dell'unità e i test end-to-end. Rimuovere il parametro **--run-unittests** per compilare ed eseguire i test dell'unità. Rimuovere il parametro **--run-e2e-tests** per compilare ed eseguire i test end-to-end.

> [!NOTE]
> Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella directory principale della copia locale del repository **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Per eseguire l'esempio

1. Lo script **build.sh** genera l'output nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**. Questo output include anche i due moduli usati in questo esempio.

    Lo script di compilazione scrive **liblogger.so** nella cartella **build/modules/logger/** e **libhello\_world.so** nella cartella **build/modules/hello_world/**. Usare questi percorsi per il valore **module path**, come illustrato nel file di impostazioni JSON di esempio seguente.
1. Il processo hello\_world\_sample accetta il percorso di un file di configurazione JSON come argomento della riga di comando. Il file JSON di esempio seguente è disponibile nel repository dell'SDK in **samples/hello\_world/src/hello\_world\_lin.json**. Questo file di configurazione funziona così com'è, a meno che non si modifichi lo script di compilazione per inserire moduli o file eseguibili di esempio in percorsi non predefiniti.

   > [!NOTE]
   > I percorsi dei moduli sono relativi alla directory di lavoro corrente da cui viene avviato l'eseguibile hello\_world\_sample, non alla directory in cui si trova il file eseguibile. Per impostazione predefinita, il file di configurazione JSON di esempio prevede la scrittura del file "log.txt" nella directory di lavoro corrente.

    ```json
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links":
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
1. Passare alla cartella **build**.
1. Eseguire il comando seguente:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

