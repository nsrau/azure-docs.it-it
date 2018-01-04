---
title: Distribuire il gateway di factory connesso - Azure | Documenti Microsoft
description: "Come distribuire un gateway in Windows o Linux per abilitare la connettività alla soluzione factory connesso preconfigurato."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: dobett
ms.openlocfilehash: 1506488193638400af7c71b3ecd00e99512daa62
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>Distribuire un gateway per la soluzione factory connesso preconfigurato in Windows o Linux

Sono necessari due componenti software per distribuire un gateway esterno per il *factory connesso* preconfigurato soluzione:

- Il *OPC Proxy* stabilisce una connessione alla factory connesso. Il Proxy OPC resterà in attesa di messaggi di comando e controllo dal Browser OPC integrato che viene eseguita nel portale di soluzione factory connesso.

- Il *Publisher OPC* si connette al server UA OPC di on-premise esistenti e inoltra i messaggi di dati di telemetria da essi factory connesso. È possibile connettere un dispositivo classico OPC utilizzando il [l'adapter classico OPC per OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Entrambi i componenti sono open source e sono disponibili come origine in GitHub e come contenitori di Docker in cui DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [Server di pubblicazione OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Server di pubblicazione OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Non è necessario aprire le porte in ingresso nel firewall gateway o un indirizzo IP pubblico per uno dei due componenti. I componenti OPC Proxy e server di pubblicazione OPC utilizzano solo la porta 443 in uscita.

I passaggi descritti in questo articolo viene descritto come distribuire un gateway con Docker in Windows o Linux. Il gateway consente la connettività alla soluzione factory connesso preconfigurato. È inoltre possibile utilizzare i componenti senza factory connesso.

> [!NOTE]
> Entrambi i componenti possono essere utilizzati come moduli [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Scegliere un dispositivo gateway

Se non si ha ancora un dispositivo gateway, Microsoft consiglia di acquistare un gateway commerciale da uno dei partner. Per un elenco di dispositivi gateway compatibili con la soluzione factory connesso, visitare il [catalogo dispositivi Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Seguire le istruzioni fornite con il dispositivo per configurare il gateway.

In alternativa, è possibile utilizzare le istruzioni seguenti per configurare manualmente un dispositivo gateway esistente.

## <a name="install-and-configure-docker"></a>Installare e configurare Docker

Installare [Docker per Windows](https://www.docker.com/docker-windows) nel dispositivo gateway basato su Windows o utilizzare una gestione pacchetti per installare docker nel dispositivo gateway basato su Linux.

Durante l'installazione di Docker per Windows, selezionare un'unità nel computer host da condividere con Docker. La schermata seguente mostra la condivisione di **D** unità del sistema Windows per consentire l'accesso per l'unità host all'interno di un contenitore docker:

![Installare Docker per Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> È anche possibile eseguire questo passaggio dopo l'installazione di docker dal **impostazioni** finestra di dialogo. Fare clic su di **Docker** icona nella barra delle applicazioni di Windows e scegliere **impostazioni**.

Se si utilizza Linux, è richiesta alcuna configurazione aggiuntiva per abilitare l'accesso al file system.

In Windows, creare una cartella nell'unità che è condiviso con Docker, in Linux, creare una cartella nel file System radice. Questa procedura dettagliata fa riferimento a questa cartella come `<SharedFolder>`.

Quando si fa riferimento per il `<SharedFolder>` in un comando di Docker, assicurarsi di utilizzare la sintassi corretta per il sistema operativo. Di seguito sono riportati due esempi, uno per Windows e uno per Linux:

- Se si sta utilizzando la cartella `D:\shared` in Windows come il `<SharedFolder>`, la sintassi del comando di Docker è `//d/shared`.

- Se si sta utilizzando la cartella `/shared` in Linux come il `<SharedFolder>`, la sintassi del comando di Docker è `/shared`.

Per ulteriori informazioni, vedere il [utilizzare volumi](https://docs.docker.com/engine/admin/volumes/volumes/) riferimento motore docker.

## <a name="configure-the-opc-components"></a>Configurare i componenti OPC

Prima di installare i componenti OPC, completare i passaggi seguenti per preparare l'ambiente:

1. Per completare la distribuzione del gateway, è necessario il **iothubowner** stringa di connessione dell'IoT Hub nella distribuzione factory connesso. Nel [portale di Azure](http://portal.azure.com/), passare all'Hub di IoT nel gruppo di risorse creato al momento della distribuzione della soluzione factory connesso. Fare clic su **Criteri di accesso condiviso** per accedere alla stringa di connessione **iothubowner**:

    ![Trovare la stringa di connessione dell'hub IoT](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Copia il **chiave primaria di stringa di connessione** valore.

1. Per consentire la comunicazione tra i contenitori di docker, è necessaria una rete di bridge definita dall'utente. Per creare un bridge di rete per i contenitori, eseguire i comandi seguenti a un prompt dei comandi:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Per verificare il **iot_edge** è stato creato un bridge di rete, eseguire il comando seguente:

    ```cmd/sh
    docker network ls
    ```

    Il **iot_edge** un bridge di rete è incluso nell'elenco delle reti.

Per eseguire il server di pubblicazione OPC, eseguire il comando seguente a un prompt dei comandi:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Il [OPC server di pubblicazione GitHub](https://github.com/Azure/iot-edge-opc-publisher) e [docker riferimento](https://docs.docker.com/engine/reference/run/) fornite ulteriori informazioni su:

  - Le opzioni della riga di comando docker specificate prima il nome del contenitore (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - Il significato dei parametri della riga di comando OPC Publisher specificato dopo il nome del contenitore (`microsoft/iot-edge-opc-publisher:2.1.3`).

- Il `<IoTHubOwnerConnectionString>` è il **iothubowner** condiviso stringa di connessione di criteri di accesso dal portale di Azure. La stringa di connessione è stato copiato in un passaggio precedente. È necessario solo la stringa di connessione per la prima esecuzione del server di pubblicazione OPC. Nelle esecuzioni successive è necessario omettere perché rappresenta un rischio di sicurezza.

- Il `<SharedFolder>` si utilizza e la relativa sintassi è descritto nella sezione [installare e configurare Docker](#install-and-configure-docker). Server di pubblicazione OPC utilizza il `<SharedFolder>` per leggere il file di configurazione del server di pubblicazione OPC, scrivere il file di log ed entrambi questi file disponibili di fuori del contenitore.

- Server di pubblicazione OPC legge la configurazione dal **publishednodes.json** file, è necessario inserire nel `<SharedFolder>/docker` cartella. Questo file di configurazione definisce i dati del nodo OPC UA in un determinato server OPC UA che deve sottoscrivere la pubblicazione OPC.

- Ogni volta che il server OPC UA avvisa OPC server di pubblicazione di una modifica dei dati, il nuovo valore viene inviato all'IoT Hub. A seconda delle impostazioni di invio in batch, il server di pubblicazione OPC prima accumulare i dati prima di inviare i dati all'IoT Hub in un batch.

- La sintassi completa del **publishednodes.json** descritto nel file di [Publisher OPC](https://github.com/Azure/iot-edge-opc-publisher) pagina su GitHub.

    Il frammento seguente mostra un esempio semplice di un **publishednodes.json** file. In questo esempio viene illustrato come pubblicare il **CurrentTime** valore da un server OPC UA con il nome host **win10pc**:

    ```json
    [
      {
        "EndpointUrl": "opc.tcp://win10pc:48010",
        "OpcNodes": [
          {
            "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
          }
        ]
      }
    ]
    ```

    Nel **publishednodes.json** file, il UA OPC server specificato per l'URL dell'endpoint. Se si specifica il nome host con un'etichetta del nome host (ad esempio **win10pc**) anziché un indirizzo IP dell'esempio precedente, la risoluzione degli indirizzi di rete del contenitore deve essere in grado di risolvere questa etichetta del nome host in un indirizzo IP.

- Docker non supporta la risoluzione dei nomi NetBIOS, solo la risoluzione dei nomi DNS. Se non si dispone di un server DNS nella rete, è possibile utilizzare la soluzione alternativa illustrata nell'esempio precedente della riga di comando. L'esempio di riga di comando precedente Usa il `--add-host` parametro per aggiungere una voce nel file hosts di contenitori. Questa voce Abilita ricerca del nome host per il dato `<OpcServerHostname>`, risoluzione per l'indirizzo IP specificato `<IpAddressOfOpcServerHostname>`.

- OPC UA Usa certificati x. 509 per l'autenticazione e crittografia. È necessario inserire il certificato dell'editore OPC sul server OPC UA che ci si connette a, per assicurarsi di che considerare attendibile l'editore OPC. Archivio certificati del server di pubblicazione OPC si trova nella `<SharedFolder>/CertificateStores` cartella. È possibile trovare il certificato del server di pubblicazione OPC nel `trusted/certs` cartella la `CertificateStores` cartella.

  I passaggi per configurare il server OPC UA dipendono dal dispositivo in uso. in genere, questi passaggi sono documentati nel manuale dell'utente del server UA OPC.

Per installare il Proxy OPC, eseguire il comando seguente a un prompt dei comandi:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

È sufficiente eseguire l'installazione di una volta in un sistema.

Usare il comando seguente per eseguire il Proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

Proxy OPC Salva la stringa di connessione durante l'installazione. Nelle esecuzioni successive è necessario omettere la stringa di connessione perché rappresenta un rischio di sicurezza.

## <a name="enable-your-gateway"></a>Abilitare il gateway

Completare i passaggi seguenti per abilitare il gateway nella soluzione factory connesso preconfigurato:

1. Quando si eseguono entrambi i componenti, selezionare il **connettere il proprio Server UA OPC** portale soluzione factory connesso. Questa pagina è disponibile solo per gli amministratori nella soluzione. Immettere l'URL dell'endpoint server di pubblicazione (opc.tcp://publisher: 62222) e fare clic su **Connetti**.

1. Stabilire una relazione di trust tra il portale di factory connessi e il server di pubblicazione OPC. Quando viene visualizzato un avviso di certificato, fare clic su **procedere**. Successivamente, viene visualizzato un errore che il server di pubblicazione OPC non ritiene attendibile il client Web agente utente. Per risolvere questo errore, copiare il **Client Web UA** certificato dal `<SharedFolder>/CertificateStores/rejected/certs` cartella in cui il `<SharedFolder>/CertificateStores/trusted/certs` cartella nel gateway. Non è necessario riavviare il gateway.

Ora è possibile connettersi al gateway dal cloud e aggiungere i server OPC UA alla soluzione.

## <a name="add-your-own-opc-ua-servers"></a>Aggiungere i propri server UA OPC

Per aggiungere la propria OPC Usa server per la factory connesso preconfigurato soluzione:

1. Individuare il **connettere il proprio server OPC UA** pagina nel portale di soluzione factory connesso. La stessa procedura come esempio della sezione precedente per stabilire una relazione di trust tra il portale di factory connessi e il server UA OPC.

    ![Portale della soluzione](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Esplorare l'albero di nodi OPC UA del server OPC UA pulsante destro del mouse che si desidera inviare a factory connesso e selezionare i nodi OPC **pubblicare**.

1. I dati di telemetria ora passano dal dispositivo gateway. È possibile visualizzare i dati di telemetria nel **percorsi Factory** visualizzate nel portale di factory connesso in **nuova Factory**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'architettura della soluzione factory connesso preconfigurato, vedere [factory connesso preconfigurato procedura dettagliata sulla soluzione](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

Informazioni sull'[implementazione di riferimento del modulo di pubblicazione OPC](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-connected-factory-publisher).