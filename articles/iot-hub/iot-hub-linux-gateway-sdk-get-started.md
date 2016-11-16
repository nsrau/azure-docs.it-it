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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23176a9251a90a985a5d2fbce23ceeb9d0925234


---
# <a name="azure-iot-gateway-sdk-beta-get-started-using-linux"></a>IoT SDK per gateway di Azure (beta): introduzione all'uso in Linux
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
   
    Lo script di compilazione scrive **liblogger_hl.so** nella cartella **build/modules/logger/** e **libhello_world_hl.so** nella cartella **build/modules/hello_world/**. Usare questi percorsi per il valore di **module path** come illustrato nel file di impostazioni JSON riportato di seguito.
2. Il file **hello_world_lin.json** nella cartella **samples/hello_world/src** è un file di impostazioni JSON di esempio per Linux che è possibile usare per eseguire l'esempio. Le impostazioni JSON di esempio illustrate di seguito presuppongono che si esegua l'esempio dalla radice di una copia locale del repository **azure-iot-gateway-sdk** .
3. Per il modulo **logger_hl**, nella sezione **args**, impostare il valore di **filename** sul nome e percorso del file che conterrà i dati di log.
   
    Questo è un esempio di un file di impostazioni JSON per Linux che scrive il file **log.txt** nella cartella in cui si esegue l'esempio.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger_hl.so"
          },
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world_hl.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. Nella shell accedere alla cartella **azure-iot-gateway-sdk** .
5. Eseguire il comando seguente:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO2-->


