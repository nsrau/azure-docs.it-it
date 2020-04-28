---
title: Gestire la rete di calcolo su Azure Data Box Edge per accedere ai moduli | Microsoft Docs
description: Viene descritto come estendere la rete di calcolo nel Data Box Edge per accedere ai moduli tramite un indirizzo IP esterno.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65917236"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Abilitare la rete di calcolo nel Azure Data Box Edge

Questo articolo descrive il modo in cui i moduli in esecuzione nel Azure Data Box Edge possono accedere alla rete di calcolo abilitata nel dispositivo.

Per configurare la rete, seguire questa procedura:

- Abilitare un'interfaccia di rete nel dispositivo Data Box Edge per il calcolo
- Aggiungere un modulo per accedere alla rete di calcolo nel Data Box Edge
- Verificare che il modulo sia in grado di accedere all'interfaccia di rete abilitata

In questa esercitazione si userà un modulo app webserver per illustrare lo scenario.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

- Un dispositivo Data Box Edge con la configurazione del dispositivo completata.
- Il passaggio di **calcolo** è stato completato in base all' [esercitazione: trasformare i dati con Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) nel dispositivo. Il dispositivo deve disporre di una risorsa Hub di Internet delle cose associata, un dispositivo Internet delle cose e un dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Abilitare l'interfaccia di rete per il calcolo

Per accedere ai moduli in esecuzione nel dispositivo tramite una rete esterna, è necessario assegnare un indirizzo IP a un'interfaccia di rete nel dispositivo. È possibile gestire queste impostazioni di calcolo dall'interfaccia utente Web locale.

Per configurare le impostazioni di calcolo, seguire questa procedura nell'interfaccia utente Web locale.

1. Nell'interfaccia utente Web locale passare a **Configurazione > Impostazioni calcolo**.  

2. **Abilitare** l'interfaccia di rete che si vuole usare per connettersi a un modulo di calcolo che verrà eseguito nel dispositivo.

    - Se si usano indirizzi IP statici, immettere un indirizzo IP per l'interfaccia di rete.
    - Se si usa DHCP, gli indirizzi IP vengono assegnati automaticamente. In questo esempio viene usato DHCP.

    ![Abilita impostazioni di calcolo 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selezionare **Applica** per applicare le impostazioni. Prendere nota dell'indirizzo IP assegnato all'interfaccia di rete se si utilizza DHCP.

    ![Abilitare le impostazioni di calcolo](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Aggiungi modulo app webserver

Per aggiungere un modulo app webserver nel dispositivo Data Box Edge, seguire questa procedura.

1. Passare alla risorsa dell'hub Internet delle cose associata al dispositivo Data Box Edge e quindi selezionare **IOT Edge dispositivo**.
2. Selezionare il dispositivo IoT Edge associato al dispositivo Data Box Edge. In **Dettagli dispositivo**selezionare **imposta moduli**. In **Aggiungi moduli**selezionare **+ Aggiungi** e quindi selezionare **IOT Edge modulo**.
3. Nel pannello **IOT Edge moduli personalizzati** :

    1. Specificare un **nome** per il modulo app webserver che si desidera distribuire.
    2. Fornire un **URI dell'immagine** per l'immagine del modulo. Viene recuperato un modulo che corrisponde al nome e ai tag forniti. In questo caso, `nginx:stable` effettuerà il pull di un'immagine di nginx stabile (contrassegnata come stabile) dal [repository Docker](https://hub.docker.com/_/nginx/)pubblico.
    3. In **contenitore crea opzioni**incollare il codice di esempio seguente:  

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

        Questa configurazione consente di accedere al modulo usando l'IP di rete di calcolo su *http* sulla porta TCP 8080 (con la porta predefinita del server Web 80).

        ![Specificare le informazioni sulla porta nel pannello IoT Edge modulo personalizzato](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selezionare **Salva**.

## <a name="verify-module-access"></a>Verificare l'accesso al modulo

1. Verificare che il modulo sia stato distribuito correttamente e che sia in esecuzione. Nella scheda **moduli** della pagina **Dettagli dispositivo** è necessario che lo stato di runtime del modulo sia **in esecuzione**.  
2. Connettersi al modulo app del server Web. Aprire una finestra del browser e digitare:

    `http://<compute-network-IP-address>:8080`

    Si noterà che l'app webserver è in esecuzione.

    ![Verificare la connessione al modulo sulla porta specificata](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](data-box-edge-manage-users.md).
