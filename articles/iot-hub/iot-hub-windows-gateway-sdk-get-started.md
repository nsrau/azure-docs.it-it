---
title: Introduzione all&quot;hub IoT SDK per gateway | Documentazione Microsoft
description: Procedura dettagliata sull&quot;SDK per gateway IoT di Azure con l&quot;uso di Windows per illustrare i concetti chiave dell&quot;SDK per gateway IoT di Azure.
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
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2d8b98fbd5345edd5dc6891df12f05eccd8e7ca8
ms.openlocfilehash: 6f2fe4fd3442d97955519348416b35fe6f9075d1


---
# <a name="azure-iot-gateway-sdk---get-started-using-windows"></a>Azure IoT Gateway SDK: introduzione all'uso di Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio
Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per l'uso dell'SDK in Windows.

1. Aprire un **prompt dei comandi per gli sviluppatori per VS2015** .
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
3. Eseguire lo script **tools\\build.cmd**. Questo script crea un file di soluzione Visual Studio, compila la soluzione ed esegue i test. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Per eseguire l'esempio
1. Lo script **build.cmd** crea una cartella denominata **build** nella copia locale del repository. Questa cartella contiene i due moduli usati in questo esempio.
   
    Lo script di compilazione inserisce **logger.dll** nella cartella **build\\modules\\logger\\Debug** e **hello_world.dll** nella cartella **build\\modules\\hello_world\\Debug**. Usare questi percorsi per il valore **module path**, come illustrato nel file di impostazioni JSON seguente.
2. Il processo hello_world_sample accetta il percorso di un file di configurazione JSON come argomento nella riga di comando. Il file JSON di esempio riportato di seguito viene fornito come parte del repository in **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Il file funziona così com'è, a meno che non sia stato modificato lo script di compilazione per inserire moduli o file eseguibili di esempio in percorsi non predefiniti. 

   > [!NOTE]
   > I percorsi dei moduli sono relativi alla directory in cui si trova il file hello_world_sample.exe. Per impostazione predefinita, il file di configurazione JSON di esempio prevede la scrittura del file "log.txt" nella directory di lavoro corrente.
   
    ```
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
3. Passare alla cartella radice nella copia locale del repository **azure-iot-gateway-sdk**.

4. Eseguire il comando seguente:
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO5-->


