---
title: Connettere un dispositivo tramite C in mbed | Documentazione Microsoft
description: "Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in un dispositivo mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: it-it
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Compilare ed eseguire la soluzione di esempio C

Le istruzioni seguenti descrivono i passaggi per connettere un dispositivo [Freescale FRDM-K64F abilitato per mbed][lnk-mbed-home] alla soluzione di monitoraggio remoto.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connettere il dispositivo mbed alla rete e al computer desktop

1. Connettere il dispositivo mbed alla rete con un cavo Ethernet. Questo passaggio è necessario perché l'applicazione di esempio richiede l'accesso a Internet.

1. Vedere [Getting Started with mbed][lnk-mbed-getstarted] (Guida introduttiva a mbed) per connettere il dispositivo mbed al PC desktop.

1. Se il PC desktop esegue Windows, vedere [PC Configuration][lnk-mbed-pcconnect] (Configurazione PC) per configurare l'accesso alla porta seriale al dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Creare un progetto mbed e importare il codice di esempio

Seguire questa procedura per aggiungere il codice di esempio al progetto mbed. Importare il progetto iniziale di monitoraggio remoto e quindi modificare il progetto affinché usi il protocollo MQTT anziché il protocollo AMQP. Attualmente, è necessario il protocollo MQTT per usare le funzionalità di gestione dei dispositivi di hub IoT.

1. Nel Web browser passare al [sito per sviluppatori](https://developer.mbed.org/)mbed.org. Se non si è iscritti, viene visualizzata un'opzione per creare un account (gratuito). In caso contrario, accedere con le credenziali dell'account. Fare quindi clic su **Compiler** nell'angolo superiore destro della pagina. Questa azione consente di visualizzare l'interfaccia *Workspace* (Area di lavoro).

1. Verificare che la piattaforma hardware usata venga visualizzata nell'angolo superiore destro della finestra oppure fare clic sull'icona nell'angolo destro per selezionare la piattaforma hardware.

1. Fare clic su **Import** nel menu principale. Quindi fare clic su **Click here to import from URL** (Fare clic qui per eseguire l'importazione dall'URL).
   
    ![Avviare l'importazione nell'area di lavoro mbed][6]

1. Nella finestra popup immettere il collegamento per il codice di esempio https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ e quindi fare clic su **Import** (Importa).
   
    ![Importare il codice di esempio all'area di lavoro mbed][7]

1. Nella finestra del compilatore mbed è possibile osservare che l'importazione del progetto include anche diverse librerie. Alcune vengono messe a disposizione e gestite dal team IoT di Azure ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), altre invece sono librerie di terze parti disponibili nel catalogo delle librerie di mbed.
   
    ![Visualizzare il progetto mbed][8]

1. Nell'**area di lavoro del programma**, fare clic con il pulsante destro del mouse sulla libreria **iothub\_amqp\_transport**, fare clic su **Elimina**, quindi su **OK** per confermare.

1. Nell'**area di lavoro del programma**, fare clic con il pulsante destro del mouse sulla libreria **azure\_amqp\_c**, fare clic su **Elimina**, quindi su **OK** per confermare.

1. Fare clic con il pulsante destro del mouse sul progetto **remote_monitoring** nell'**area di lavoro del programma**, selezionare **Import Library** (Importa libreria), quindi **From URL** (Da URL).
   
    ![Avviare l'importazione della libreria nell'area di lavoro mbed][6]

1. Nella finestra popup immettere il collegamento per la libreria di trasporto MQTT https://developer.mbed.org/users/AzureIoTClient/code/iothub/\_mqtt\_transport/ e quindi fare clic su **Import** (Importa).
   
    ![Importare la libreria nell'area di lavoro mbed][12]

1. Ripetere il passaggio precedente per aggiungere la libreria MQTT da https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c /.

1. L'area di lavoro dovrebbe ora apparire così:

    ![Visualizzare l'area di lavoro mbed][13]

1. Aprire il file remote\_monitoring\remote_monitoring.c e sostituire le dichiarazioni `#include` esistenti con il codice seguente:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. Eliminare tutto il restante codice nel file remote\_monitoring\remote\_monitoring.c.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Aggiungere codice per richiamare la funzione **remote\_monitoring\_run** e quindi creare ed eseguire l'applicazione del dispositivo.

1. Aggiungere una funzione **main** con il codice seguente alla fine del file remote\_monitoring.c per richiamare la funzione **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Fare clic su **Compila** per compilare il programma. È possibile ignorare eventuali avvisi, ma, se la compilazione genera errori, correggerli prima di continuare.

1. Se la compilazione ha esito positivo, il sito Web del compilatore mbed genera un file .bin con il nome del progetto che viene scaricato nel computer locale. Copiare il file bin sul dispositivo. Il salvataggio del file .bin nel dispositivo causa il riavvio del dispositivo e l'esecuzione del programma contenuto nel file .bin. È possibile riavviare manualmente il programma in qualsiasi momento facendo clic sul pulsante reset sul dispositivo mbed.

1. Connettersi al dispositivo con un'applicazione client SSH, ad esempio PuTTY. È possibile determinare la porta seriale usata dal dispositivo controllando Gestione dispositivi di Windows.
   
    ![][11]

1. In PuTTY fare clic sul tipo di connessione **Serial** . Poiché il dispositivo si connette in genere a 9600 baud, immettere 9600 nella casella **Velocità** . Fare quindi clic su **Apri**.

1. Inizia l'esecuzione del programma. Potrebbe essere necessario reimpostare la scheda (premere CTRL+INTERR o premere il pulsante reset della scheda) se il programma non viene avviato automaticamente alla connessione.
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

