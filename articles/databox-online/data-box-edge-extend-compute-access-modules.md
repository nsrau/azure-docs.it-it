---
title: Gestire la rete di calcolo in Azure Data Box Edge per accedere ai moduli Documenti Microsoft
description: Viene descritto come estendere la rete di calcolo in Data Box Edge per accedere ai moduli tramite un indirizzo IP esterno.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917236"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Abilitare la rete di calcolo in Azure Data Box EdgeEnable compute network on your Azure Data Box Edge

Questo articolo descrive come i moduli in esecuzione in Azure Data Box Edge possono accedere alla rete di calcolo abilitata nel dispositivo.

Per configurare la rete, attenersi alla seguente procedura:

- Abilitare un'interfaccia di rete nel dispositivo Data Box Edge per il calcoloEnable a network interface on your Data Box Edge device for compute
- Aggiungere un modulo per accedere alla rete di calcolo in Data Box Edge
- Verificare che il modulo possa accedere all'interfaccia di rete abilitata

In questa esercitazione si userà un modulo dell'app webserver per illustrare lo scenario.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, avrai bisogno di:

- Un dispositivo Data Box Edge con la configurazione del dispositivo completata.
- È stato completato **Configure compute** step at the [Tutorial: Transform data with Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) nel dispositivo. Al dispositivo devono essere associate una risorsa dell'hub IoT, un dispositivo IoT e un dispositivo IoT Edge.Your device should have an associated IoT Hub resource, an IoT device, and an IoT Edge device.

## <a name="enable-network-interface-for-compute"></a>Abilitare l'interfaccia di rete per il calcoloEnable network interface for compute

Per accedere ai moduli in esecuzione sul dispositivo tramite una rete esterna, è necessario assegnare un indirizzo IP a un'interfaccia di rete sul dispositivo. È possibile gestire queste impostazioni di calcolo dall'interfaccia utente Web locale.

Per configurare le impostazioni di calcolo, seguire questa procedura nell'interfaccia utente Web locale.

1. Nell'interfaccia utente Web locale passare a **Configurazione > Impostazioni calcolo**.  

2. **Abilitare** l'interfaccia di rete che si desidera utilizzare per connettersi a un modulo di calcolo che verrà eseguito nel dispositivo.

    - Se si usano indirizzi IP statici, immettere un indirizzo IP per l'interfaccia di rete.
    - Se si utilizza DHCP, gli indirizzi IP vengono assegnati automaticamente. In questo esempio viene utilizzato DHCP.

    ![Abilitare le impostazioni di calcolo 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selezionare **Applica** per applicare le impostazioni. Prendere nota dell'indirizzo IP assegnato all'interfaccia di rete se si utilizza DHCP.

    ![Abilitare le impostazioni di calcolo](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Aggiungi modulo app webserver

Eseguire la procedura seguente per aggiungere un modulo di app server Web nel dispositivo Data Box Edge.

1. Passare alla risorsa hub IoT associata al dispositivo Data Box Edge e quindi selezionare **IoT Edge device**.
2. Selezionare il dispositivo IoT Edge associato al dispositivo Data Box Edge. In **Dettagli dispositivo**selezionare **Imposta moduli**. In **Aggiungi moduli**, selezionare **Aggiungi,** quindi selezionare **Modulo Edge IoT**.
3. Nel pannello **Moduli personalizzati IoT Edge:**

    1. Specificare un **nome** per il modulo dell'app del server Web che si desidera distribuire.
    2. Specificare un **URI di immagine** per l'immagine del modulo. Viene recuperato un modulo corrispondente al nome e ai tag specificati. In questo `nginx:stable` caso, estrarrà un'immagine nginx stabile (contrassegnata come stable) dal [repository Docker](https://hub.docker.com/_/nginx/)pubblico .
    3. In Opzioni di **creazione contenitore**incollare il codice di esempio seguente:  

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

        Questa configurazione consente di accedere al modulo utilizzando l'IP di rete di calcolo su *http* sulla porta TCP 8080 (con la porta server Web predefinita 80).

        ![Specificare le informazioni sulla porta nel pannello del modulo personalizzato IoT EdgeSpecify port information in IoT Edge custom module blade](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selezionare **Salva**.

## <a name="verify-module-access"></a>Verificare l'accesso al modulo

1. Verificare che il modulo sia stato distribuito correttamente e sia in esecuzione. Nella scheda **Moduli** della pagina **Dettagli dispositivo** deve essere **in esecuzione**lo stato di runtime del modulo.  
2. Connettersi al modulo dell'app del server Web. Aprire una finestra del browser e digitare:

    `http://<compute-network-IP-address>:8080`

    Si dovrebbe vedere che l'applicazione webserver è in esecuzione.

    ![Verificare la connessione al modulo sulla porta specificataVerify connection to module over specified port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](data-box-edge-manage-users.md).
