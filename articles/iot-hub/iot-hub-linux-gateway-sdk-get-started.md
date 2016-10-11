<properties
	pageTitle="Introduzione all'IoT SDK per gateway | Microsoft Azure"
	description="Questa procedura dettagliata di Azure IoT Hub Gateway SDK usa Linux per illustrare i concetti chiave che è necessario comprendere quando si usa Azure IoT Hub Gateway SDK."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# IoT SDK per gateway (beta): introduzione all'uso in Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Come compilare l'esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Linux.

1. Aprire una shell.
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk**.
3. Eseguire lo script **tools/build.sh**. Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella directory principale della copia locale del repository **azure-iot-gateway-sdk** e generare un makefile. Lo script quindi compila la soluzione ed esegue i test.

> [AZURE.NOTE]  Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella directory principale della copia locale del repository **azure-iot-gateway-sdk**.

## Per eseguire l'esempio

1. Lo script **build.sh** genera l'output nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**. Vengono inclusi anche i due moduli usati in questo esempio.

    Lo script di compilazione scrive **liblogger\_hl.so** nella cartella **build/modules/logger/** e **libhello\_world\_hl.so** nella cartella **build/modules/hello\_world/**. Usare questi percorsi per il valore di **module path** come illustrato nel file di impostazioni JSON riportato di seguito.

2. Il file **hello\_world\_lin.json** nella cartella **samples/hello\_world/src** è un file di impostazioni JSON di esempio per Linux che è possibile usare per eseguire l'esempio. Le impostazioni JSON di esempio illustrate di seguito presuppongono che si esegua l'esempio dalla radice di una copia locale del repository **azure-iot-gateway-sdk**.

3. Per il modulo **logger\_hl**, nella sezione **args**, impostare il valore di **filename** sul nome e percorso del file che conterrà i dati di log.

    Questo è un esempio di un file di impostazioni JSON per Linux che scrive il file **log.txt** nella cartella in cui si esegue l'esempio.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. Nella shell accedere alla cartella **azure-iot-gateway-sdk**.
4. Eseguire il comando seguente:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_1005_2016-->