<properties
    pageTitle="Introduzione all'IoT SDK per gateway | Microsoft Azure"
    description="Procedura dettagliata dell'Azure IoT SDK per gateway con Windows che illustra i concetti chiave che è necessario comprendere quando si usa l'Azure IoT SDK per gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-(beta)---get-started-using-windows"></a>IoT SDK per gateway (beta): introduzione all'uso in Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Windows.

1. Aprire un **prompt dei comandi per gli sviluppatori per VS2015** .
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
3. Eseguire lo script **tools\\build.cmd**. Questo script crea un file di soluzione Visual Studio, compila la soluzione ed esegue i test. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Per eseguire l'esempio

1. Lo script **build.cmd** crea una cartella denominata **build** nella copia locale del repository. Questa cartella contiene i due moduli usati in questo esempio.

    Lo script di compilazione scrive **logger_hl.dll** nella cartella **build\\modules\\logger\\Debug** e **hello_world_hl.dll** nella cartella **build\\modules\\hello_world\\Debug**. Usare questi percorsi per il valore di **module path** come illustrato nel file di impostazioni JSON riportato di seguito.

2. Il file **hello_world_win.json** nella cartella **samples\\hello_world\\src** è un file di impostazioni JSON di esempio per Windows che è possibile usare per eseguire l'esempio. Le impostazioni JSON di esempio illustrate di seguito presuppongono che sia stato clonato il repository SDK per gateway nella directory principale dell'unità **C:** . Se il repository è stato scaricato in un altro percorso, è necessario modificare di conseguenza i valori di **module path** nel file **samples\\hello_world\\src\\hello_world_win.json**.

3. Per il modulo **logger_hl**, nella sezione **args**, impostare il valore di **filename** sul nome e percorso del file che conterrà i dati di log.

    Questo è un esempio di un file di impostazioni JSON per Windows che verrà scritto nel file **log.txt** nella directory principale dell'unità **C:**.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
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

3. Al prompt dei comandi, accedere alla directory principale della copia locale del repository **azure-iot-gateway-sdk** .
4. Eseguire il comando seguente:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md


<!--HONumber=Oct16_HO2-->


