---
title: Distribuire l'app Kubernetes senza stato sulla GPU Pro Azure Stack Edge tramite IoT Edge modulo | Microsoft Docs
description: Viene descritto come distribuire un'applicazione Kubernetes senza stato sul dispositivo GPU Pro Azure Stack Edge usando un modulo IoT Edge a cui si accede tramite un indirizzo IP esterno.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 4bc598080b96886e6734ac3709761465a1a28d49
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899526"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Usare IoT Edge modulo per eseguire un'applicazione Kubernetes senza stato sul dispositivo GPU Pro Azure Stack Edge

Questo articolo descrive come è possibile usare un modulo IoT Edge per distribuire un'applicazione senza stato nel dispositivo Azure Stack Edge Pro.

Per distribuire l'applicazione senza stato, seguire questa procedura:

- Prima di distribuire un modulo IoT Edge, assicurarsi che i prerequisiti siano completati.
- Aggiungere un modulo di IoT Edge per accedere alla rete di calcolo nel Azure Stack Edge Pro.
- Verificare che il modulo sia in grado di accedere all'interfaccia di rete abilitata.

In questo articolo sulle procedure si userà un modulo app webserver per illustrare lo scenario.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

- Un dispositivo Azure Stack Edge Pro. Assicurarsi che:

    - Le impostazioni di rete di calcolo sono configurate nel dispositivo.
    - Il dispositivo viene attivato in base ai passaggi descritti in [esercitazione: attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Il passaggio di **calcolo** è stato completato in base all' [esercitazione: configurare il calcolo nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md) sul dispositivo. Il dispositivo deve disporre di una risorsa Hub di Internet delle cose associata, un dispositivo Internet delle cose e un dispositivo IoT Edge.


## <a name="add-webserver-app-module"></a>Aggiungi modulo app webserver

Per aggiungere un modulo app webserver nel dispositivo Azure Stack Edge Pro, seguire questa procedura.

1. Nella risorsa hub tutto associata al dispositivo passare a **gestione automatica dispositivi > IOT Edge**.
1. Selezionare e fare clic sul dispositivo IoT Edge associato al dispositivo Azure Stack Edge Pro. 

    ![Selezionare IoT Edge dispositivo](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Selezionare **Imposta moduli**. In **imposta moduli nel dispositivo**selezionare **+ Aggiungi** e quindi selezionare **IOT Edge modulo**.

    ![Seleziona modulo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. Nel **modulo Add IOT Edge**:

    1. Specificare un **nome** per il modulo app webserver che si desidera distribuire.
    2. Nella scheda **Impostazioni modulo** specificare un **URI dell'immagine** per l'immagine del modulo. Viene recuperato un modulo che corrisponde al nome e ai tag forniti. In questo caso, `nginx:stable` effettuerà il pull di un'immagine di nginx stabile (contrassegnata come stabile) dal [repository Docker](https://hub.docker.com/_/nginx/)pubblico.

        ![Aggiungi modulo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Nella scheda **Crea opzioni del contenitore** incollare il codice di esempio seguente:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Questa configurazione consente di accedere al modulo usando l'IP di rete di calcolo su *http* sulla porta TCP 8080 (con la porta predefinita del server Web 80). Selezionare **Aggiungi**.

        ![Specificare le informazioni sulla porta nel pannello IoT Edge modulo personalizzato](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Selezionare **Rivedi e crea**. Esaminare i dettagli del modulo e selezionare **Crea**.

## <a name="verify-module-access"></a>Verificare l'accesso al modulo

1. Verificare che il modulo sia stato distribuito correttamente e che sia in esecuzione. Nella scheda **moduli** è necessario che lo stato di runtime del modulo sia **in esecuzione**.  

    ![Verificare che lo stato del modulo sia in esecuzione](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Per ottenere l'endpoint esterno dell'app webserver, [accedere al dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. Nel riquadro sinistro del dashboard filtrare in base allo spazio dei nomi **iotedge** . Passare a **individuazione e bilanciamento del carico > Servizi**. Nell'elenco dei servizi elencati individuare l'endpoint esterno per il modulo app webserver. 

    ![Connetti a app webserver in endpoint esterno](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Selezionare l'endpoint esterno per aprire una nuova finestra del browser.

    Si noterà che l'app webserver è in esecuzione.

    ![Verificare la connessione al modulo sulla porta specificata](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come esporre un'applicazione con stato tramite un modulo di IoT Edge<!--insert link-->.
