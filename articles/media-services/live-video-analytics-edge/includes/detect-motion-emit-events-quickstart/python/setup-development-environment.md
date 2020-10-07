---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691191"
---
1. Clonare il repository da questo percorso: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python.
1. In Visual Studio Code aprire la cartella in cui è stato scaricato il repository.
1. In Visual Studio Code passare alla cartella *src/cloud-to-device-console-app*. In questa cartella creare un file e assegnargli il nome *appsettings.json*. Questo file conterrà le impostazioni necessarie per eseguire il programma.
1. Copiare il contenuto dal file *~/clouddrive/lva-sample/appsettings.json* generato in precedenza in questo argomento di avvio rapido.

    Il testo sarà simile all'output seguente.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Passare alla cartella *src/edge* e creare un file denominato *.env*.
1. Copiare il contenuto del file */clouddrive/lva-sample/edge-deployment/.env*. Il testo sarà simile al codice seguente.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    