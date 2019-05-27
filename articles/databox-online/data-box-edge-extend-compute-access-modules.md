---
title: Gestione della rete di calcolo sul bordo di finestra di dati Azure per i moduli di accesso | Microsoft Docs
description: Viene descritto come estendere la rete di calcolo in dispositivi perimetrali finestra di dati per l'accesso ai moduli tramite un indirizzo IP esterno.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917236"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Abilitare la rete di calcolo sul bordo di finestra di dati di Azure

Questo articolo descrive come i moduli in esecuzione in dispositivi perimetrali casella dei dati di Azure possono accedere alla rete di calcolo abilitata nel dispositivo.

Per configurare la rete, è possibile eseguire i passaggi seguenti:

- Consentire a un'interfaccia di rete nel dispositivo Edge casella dei dati per il calcolo
- Aggiungere un modulo di accesso rete calcolo sul bordo di finestra di dati
- Verificare che il modulo può accedere all'interfaccia di rete abilitata

In questa esercitazione si userà un modulo dell'app server Web per illustrare lo scenario.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

- Un dispositivo Edge casella dei dati con installazione dispositivo completata.
- È stata completata **Configura calcolo** passaggio come indicato di [esercitazione: Trasformare i dati con Azure Data finestra Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) nel dispositivo. Il dispositivo deve avere una risorsa dell'IoT Hub associata, un dispositivo IoT e un dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Abilitare l'interfaccia di rete per il calcolo

Per accedere ai moduli in esecuzione nel dispositivo tramite una rete esterna, è necessario assegnare un indirizzo IP a un'interfaccia di rete nel dispositivo. È possibile gestire queste impostazioni di calcolo dall'interfaccia utente Web locale.

Per configurare le impostazioni di calcolo, seguire questa procedura nell'interfaccia utente Web locale.

1. Nell'interfaccia utente Web locale passare a **Configurazione > Impostazioni calcolo**.  

2. **Abilitare** l'interfaccia di rete che si desidera usare per connettersi a un modulo di calcolo che verranno eseguite nel dispositivo.

    - Se si usano indirizzi IP statici, immettere un indirizzo IP per l'interfaccia di rete.
    - Se si usa DHCP, gli indirizzi IP vengono assegnati automaticamente. In questo esempio Usa DHCP.

    ![Abilitare le impostazioni di calcolo 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selezionare **Applica** per applicare le impostazioni. Prendere nota dell'indirizzo IP assegnato all'interfaccia di rete se si usa DHCP.

    ![Abilitare le impostazioni di calcolo](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Aggiungi modulo dell'app server Web

Eseguire i passaggi seguenti per aggiungere un modulo dell'app server Web nel dispositivo Edge casella dei dati.

1. Passare alla risorsa dell'IoT Hub associata con il dispositivo periferico casella dei dati e quindi selezionare **dispositivo IoT Edge**.
2. Selezionare il dispositivo IoT Edge associato con il dispositivo periferico casella dei dati. Nel **dettagli dispositivo**, selezionare **impostare i moduli**. Sul **aggiungere i moduli**, selezionare **+ Aggiungi** e quindi selezionare **modulo di IoT Edge**.
3. Nel **moduli personalizzati di IoT Edge** pannello:

    1. Specificare una **nome** per il modulo dell'app server Web che si desidera distribuire.
    2. Fornire un' **URI immagine** per l'immagine del modulo. Un modulo corrispondente al nome specificato e un tag viene recuperato. In questo caso `nginx:stable` effettuerà il pull un'immagine di nginx stabile (contrassegnata come stabili) al pubblico [repository Docker](https://hub.docker.com/_/nginx/).
    3. Nel **opzioni di creazione del contenitore**, incollare il codice di esempio seguente:  

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

        Questa configurazione consente di accedere al modulo tramite l'IP di rete di calcolo *http* sulla porta TCP 8080 (con la porta predefinita server Web è 80).

        ![Specificare le informazioni della porta nel Pannello di modulo personalizzato IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selezionare **Salva**.

## <a name="verify-module-access"></a>Verificare l'accesso di modulo

1. Verificare il modulo è stato distribuito correttamente e sia in esecuzione. Nel **dettagli dispositivo** pagina il **moduli** scheda, lo stato di runtime del modulo deve essere **in esecuzione**.  
2. Connettere il modulo di app server web. Aprire una finestra del browser e digitare:

    `http://<compute-network-IP-address>:8080`

    Si noterà che l'app server Web sia in esecuzione.

    ![Verificare la connessione al modulo tramite la porta specificata](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](data-box-edge-manage-users.md).
