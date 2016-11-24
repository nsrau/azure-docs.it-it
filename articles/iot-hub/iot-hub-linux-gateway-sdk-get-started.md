---
title: Introduzione all&quot;hub IoT SDK per gateway | Documentazione Microsoft
description: "In questa procedura dettagliata dell&quot;IoT SDK per gateway di Azure viene usato Linux per illustrare i concetti chiave che è necessario comprendere quando si usa IoT SDK per gateway di Azure."
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
ms.date: 08/25/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: fb085ca229beba1757efa100ffca1e42089aedfa
ms.openlocfilehash: 827ecc587dabfba58e87d192001c2714a1d7ce4a


---
# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>IoT SDK per gateway di Azure (beta): introduzione all'uso in Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio
Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Linux.

1. Aprire una shell.
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
3. Eseguire lo script **tools/build.sh** . Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella directory principale della copia locale del repository **azure-iot-gateway-sdk** e generare un makefile. Lo script quindi compila la soluzione ed esegue i test.

> [!NOTE]
> Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella directory principale della copia locale del repository **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Per eseguire l'esempio
1. Lo script **build.sh** genera l'output nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**. Vengono inclusi anche i due moduli usati in questo esempio.
   
    Lo script di compilazione scrive **liblogger.so** nella cartella **build/modules/logger/** e **libhello_world.so** nella cartella **build/modules/hello_world/**. Usare questi percorsi per il valore di **module path** come illustrato nel file di impostazioni JSON riportato di seguito.
2. Il processo hello_world_sample accetta il percorso di un file di configurazione JSON come argomento nella riga di comando. Un file JSON di esempio viene fornito come parte del repository in **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** ed è riportato di seguito. Il file funzionerà così com'è a meno che non sia stato modificato lo script di compilazione per inserire moduli o file eseguibili di esempio in percorsi non predefiniti.

   > [!NOTE]
   > I percorsi dei moduli sono relativi alla directory di lavoro corrente da cui viene avviato l'eseguibile hello_world_sample, non alla directory in cui si trova il file eseguibile. Per impostazione predefinita, il file di configurazione JSON di esempio prevede la scrittura del file "log.txt" nella directory di lavoro corrente.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger.so"
          },
          "args" : 
          {
            "filename":"log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```
3. Passare alla cartella **azure-iot-gateway-sdk**.
4. Eseguire il comando seguente:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO3-->


