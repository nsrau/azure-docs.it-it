---
title: Distribuire il gateway di fabbrica connessa - Azure | Microsoft Docs
description: "Come distribuire un gateway in Windows o Linux per abilitare la connettività alla soluzione preconfigurata di fabbrica connessa."
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
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 4606cb676c3ab7c8c8511579f43d251ff7d2ae8a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>Distribuire un gateway perimetrale per la soluzione preconfigurata di fabbrica connessa in Windows o Linux

Per distribuire un gateway perimetrale per la soluzione preconfigurata di *fabbrica connessa*, sono necessari due componenti software:

- Il *proxy OPC* stabilisce una connessione alla fabbrica connessa. Il proxy OPC attende quindi i messaggi di comando e controllo dal browser OPC integrato in esecuzione nel portale della soluzione di fabbrica connessa.

- Il *server di pubblicazione OPC* si connette ai server UA OPC locali esistenti, dai quali inoltra i messaggi di telemetria alla fabbrica connessa. È possibile connettere un dispositivo classico OPC usando l'[adapter classico OPC per OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Entrambi i componenti sono open source e disponibili come codice sorgente in GitHub e come contenitori Docker in DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Per nessuno dei due componenti sono necessari un indirizzo IP pubblico o porte di ingresso aperte nel firewall del gateway. I componenti proxy OPC e server di pubblicazione OPC usano solo la porta in uscita 443.

Le procedure descritte in questo articolo mostrano come distribuire un gateway perimetrale usando Docker in Windows o Linux. Il gateway abilita la connettività alla soluzione preconfigurata di fabbrica connessa. È anche possibile usare i componenti senza fabbrica connessa.

> [!NOTE]
> Entrambi i componenti possono essere usati come moduli in [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Scegliere un dispositivo gateway

Se non si ha ancora un dispositivo gateway, Microsoft consiglia di acquistare un gateway commerciale da uno dei partner. Per un elenco dei dispositivi gateway compatibili con la soluzione di fabbrica connessa, vedere il [catalogo dei dispositivi Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Seguire le istruzioni fornite con il dispositivo per configurare il gateway.

In alternativa, usare le istruzioni seguenti per configurare manualmente un dispositivo gateway esistente.

## <a name="install-and-configure-docker"></a>Installare e configurare Docker

Installare [Docker per Windows](https://www.docker.com/docker-windows) nel dispositivo gateway basato su Windows o usare uno strumento di gestione pacchetti per installare Docker nel dispositivo gateway basato su Linux.

Durante l'installazione di Docker per Windows, selezionare un'unità nel computer host da condividere con Docker. Lo screenshot seguente mostra la condivisione dell'unità **D** nel sistema Windows per consentire l'accesso all'unità host da un contenitore Docker:

![Installare Docker per Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> È anche possibile eseguire questo passaggio dopo aver installato Docker dalla finestra di dialogo **Impostazioni**. Fare clic con il pulsante destro del mouse sull'icona **Docker** nell'area di notifica di Windows e scegliere **Impostazioni**. Se nel sistema sono stati distribuiti i principali aggiornamenti di Windows, ad esempio l'aggiornamento Windows Fall Creators, annullare la condivisione delle unità e condividerle nuovamente per aggiornare i diritti di accesso.

Se si usa Linux, non sono necessarie altre attività di configurazione per abilitare l'accesso al file system.

In Windows creare una cartella nell'unità condivisa con Docker, mentre in Linux creare una cartella all'interno del file system radice. In questa procedura dettagliata la cartella è denominata `<SharedFolder>`.

Quando si fa riferimento a `<SharedFolder>` in un comando di Docker, assicurarsi di usare la sintassi corretta per il sistema operativo specifico. Ecco due esempi, uno per Windows e uno per Linux:

- Se si usa la cartella `D:\shared` in Windows come `<SharedFolder>`, la sintassi del comando di Docker è `d:/shared`.

- Se si usa la cartella `/shared` in Linux come `<SharedFolder>`, la sintassi del comando di Docker è `/shared`.

Per altri dettagli, vedere le informazioni di riferimento sul motore di Docker relative all'[uso di volumi](https://docs.docker.com/engine/admin/volumes/volumes/).

## <a name="configure-the-opc-components"></a>Configurare i componenti OPC

Prima di installare i componenti OPC, completare i passaggi seguenti per preparare l'ambiente:

1. Per completare la distribuzione del gateway, è necessaria la stringa di connessione **iothubowner** dell'hub IoT nella distribuzione della fabbrica connessa. Nel [portale di Azure](http://portal.azure.com/) passare all'hub IoT nel gruppo di risorse creato durante la distribuzione della soluzione di fabbrica connessa. Fare clic su **Criteri di accesso condiviso** per accedere alla stringa di connessione **iothubowner**:

    ![Trovare la stringa di connessione dell'hub IoT](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Copiare il valore di **Stringa di connessione - chiave primaria**.

1. Per consentire la comunicazione tra contenitori Docker, è necessario usare una rete di bridge definita dall'utente. Per creare una rete di bridge per i contenitori, eseguire i comandi seguenti al prompt dei comandi:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Per verificare che la rete di bridge **iot_edge** sia stata creata, eseguire il comando seguente:

    ```cmd/sh
    docker network ls
    ```

    La rete di bridge **iot_edge** è inclusa nell'elenco delle reti.

Per eseguire il server di pubblicazione OPC, immettere il comando seguente al prompt dei comandi:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Il modulo GitHub [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) e le [informazioni di riferimento sull'esecuzione di Docker](https://docs.docker.com/engine/reference/run/) offrono altre informazioni su:

  - Opzioni della riga di comando di Docker specificate prima del nome del contenitore (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - Significato dei parametri della riga di comando per il server di pubblicazione OPC specificate dopo il nome del contenitore (`microsoft/iot-edge-opc-publisher:2.1.3`).

- `<IoTHubOwnerConnectionString>` è la stringa di connessione dei criteri di accesso condiviso **iothubowner** per il portale di Azure. Questa stringa di connessione è stata copiata in un passaggio precedente. Questa stringa di connessione è necessaria solo per la prima esecuzione del server di pubblicazione OPC. Nelle esecuzioni successive è necessario ometterla perché rappresenta un rischio per la sicurezza.

- La cartella `<SharedFolder>` usata e la sua sintassi sono descritte nella sezione [Installare e configurare Docker](#install-and-configure-docker). Il server di pubblicazione OPC usa `<SharedFolder>` per eseguire operazioni di lettura e scrittura sul file di configurazione del server di pubblicazione OPC, scrivere il file di log e rendere entrambi i file disponibili all'esterno del contenitore.

- Il server di pubblicazione OPC legge la propria configurazione dal file **publishednodes.json**, che viene letto dalla cartella `<SharedFolder>/docker` e in questa stessa cartella viene scritto. Questo file di configurazione definisce i dati del nodo OPC UA in un server OPC UA specifico che deve sottoscrivere il server di pubblicazione OPC. La sintassi completa del file **publishednodes.json** è descritta nella pagina del modulo [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) in GitHub. Quando si aggiunge un gateway, inserire un file **publishednodes.json** vuoto nella cartella:

    ```json
    [
    ]
    ```

- Ogni volta che il server OPC UA comunica al server di pubblicazione OPC una modifica dei dati, il nuovo valore viene inviato all'hub IoT. A seconda delle impostazioni di invio in batch, il server di pubblicazione OPC può accumulare i dati prima di inviarli in batch all'hub IoT.

- Docker non supporta la risoluzione dei nomi NetBIOS, ma solo quella dei nomi DNS. Se nella rete non è presente un server DNS, è possibile usare la soluzione alternativa mostrata nell'esempio della riga di comando precedente. L'esempio precedente usa il parametro `--add-host` per aggiungere una voce nel file degli host dei contenitori. Questa voce abilita la ricerca dei nomi host per l'oggetto `<OpcServerHostname>` specifico, risolvendoli nell'indirizzo IP `<IpAddressOfOpcServerHostname>` specifico.

- OPC UA usa certificati X.509 per l'autenticazione e la crittografia. È necessario aggiungere il certificato del server di pubblicazione OPC nel server OPC UA a cui ci si connette per garantire che questo consideri attendibile il server di pubblicazione OPC. L'archivio certificati del server di pubblicazione OPC si trova nella cartella `<SharedFolder>/CertificateStores`. Il certificato del server di pubblicazione OPC si trova nella cartella `trusted/certs` all'interno della cartella `CertificateStores`.

  I passaggi per configurare il server OPC UA dipendono dal dispositivo usato. Questi passaggi sono in genere descritti nel manuale dell'utente del server OPC UA.

Per installare il proxy OPC, eseguire il comando seguente al prompt dei comandi:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

È necessario eseguire l'installazione solo una volta in ogni sistema.

Usare il comando seguente per eseguire il proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

Il proxy OPC salva la stringa di connessione durante l'installazione. Nelle esecuzioni successive è necessario omettere la stringa di connessione perché rappresenta un rischio per la sicurezza.

## <a name="enable-your-gateway"></a>Abilitare il gateway

Eseguire i passaggi seguenti per abilitare il gateway nella soluzione preconfigurata di fabbrica connessa:

1. Quando entrambi i componenti sono in esecuzione, passare alla pagina **Connettere il server OPC UA personale** nel portale della soluzione di fabbrica connessa. Questa pagina è disponibile solo per gli amministratori della soluzione. Immettere l'URL dell'endpoint del server di pubblicazione (opc.tcp://publisher:62222) e fare clic su **Connetti**.

1. Stabilire una relazione di trust tra il portale della soluzione di fabbrica connessa e il server di pubblicazione OPC. Quando viene visualizzato un avviso del certificato, fare clic su **Continua**. Viene quindi visualizzato un errore che indica che il server di pubblicazione OPC non considera attendibile il client Web UA. Per risolvere questo errore, copiare il certificato del **client Web UA** dalla cartella `<SharedFolder>/CertificateStores/rejected/certs` alla cartella `<SharedFolder>/CertificateStores/trusted/certs` nel gateway. Non è necessario riavviare il gateway.

Ora è possibile connettersi al gateway dal cloud e aggiungere i server OPC UA alla soluzione.

## <a name="add-your-own-opc-ua-servers"></a>Aggiungere i server OPC UA

Per aggiungere i server OPC UA alla soluzione preconfigurata di fabbrica connessa:

1. Passare alla pagina **Connettere il server OPC UA personale** nel portale della soluzione di fabbrica connessa.

    1. Avviare il server OPC UA a cui connettersi. Verificare che il server OPC UA sia raggiungibile dal server di pubblicazione OPC e dal proxy OPC in esecuzione nel contenitore. Vedere i commenti precedenti sulla risoluzione dei nomi.
    1. Immettere l'URL dell'endpoint del server OPC UA (`opc.tcp://<host>:<port>`) e fare clic su **Connetti**.
    1. Durante la configurazione della connessione, viene stabilita una relazione di trust tra il portale della soluzione di fabbrica connessa (client OPC UA) e il server OPC UA a cui si sta tentando di eseguire la connessione. Nel dashboard della soluzione di fabbrica connessa viene visualizzato un avviso relativo all'**impossibilità di verificare il certificato del server al quale si vuole eseguire la connessione**. Quando viene visualizzato un avviso del certificato, fare clic su **Continua**.
    1. La configurazione del certificato del server OPC UA a cui si sta tentando di eseguire la connessione risulta più complessa. Per i computer basati su server OPC UA, è possibile che sia sufficiente confermare una finestra di dialogo di avviso visualizzata nel dashboard. Per i sistemi con server OPC UA incorporati, consultare la documentazione del server OPC UA per informazioni sull'esecuzione di questa attività. Per completare questa attività, può essere necessario il certificato del client OPC UA del portale della soluzione di fabbrica connessa. Un amministratore può scaricare il certificato nella pagina **Connettere il server OPC UA personale**:

        ![Portale della soluzione](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Passare all'albero dei nodi OPC UA del server OPC UA, fare clic con il pulsante destro del mouse sui nodi OPC di cui si vogliono inviare i valori alla soluzione di fabbrica connessa e scegliere **Pubblica**.

1. I dati di telemetria ora passano dal dispositivo gateway. È possibile visualizzare i dati di telemetria nella visualizzazione **Località della fabbrica** del portale della soluzione di fabbrica connessa in **Nuova factory**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'architettura della soluzione preconfigurata di fabbrica connessa, vedere [Procedura dettagliata per la soluzione preconfigurata di fabbrica connessa](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

Informazioni sull'[implementazione di riferimento del modulo di pubblicazione OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).