---
title: Distribuire il gateway di connected factory di Azure IoT Suite | Microsoft Docs
description: "Come distribuire un gateway in Windows o Linux per abilitare la connettività alla soluzione preconfigurata di connected factory."
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
ms.date: 04/22/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: e6eaf4e7a3b0aa8241c026f66150d3f6e9d933d0
ms.lasthandoff: 04/21/2017


---

# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Distribuire un gateway in Windows o Linux per la soluzione preconfigurata di connected factory

I passaggi di questo articolo illustrano come distribuire un gateway usando Docker in Windows o Linux. Il gateway abilita la connettività alla soluzione preconfigurata di connected factory.

## <a name="windows-deployment"></a>Distribuzione in Windows

> [!NOTE]
> Se non si ha ancora un dispositivo gateway, Microsoft consiglia di acquistare un gateway commerciale da uno dei partner. Vedere il [catalogo dei dispositivi Azure per IoT] per un elenco di dispositivi gateway compatibili con la soluzione di connected factory. Seguire le istruzioni fornite con il dispositivo per configurare il gateway. In alternativa, usare le istruzioni seguenti per configurare manualmente uno dei gateway esistenti.

### <a name="install-docker"></a>Installare Docker

Installare [Docker per Windows] nel dispositivo gateway basato su Windows. Durante la configurazione di Docker per Windows, selezionare un'unità sul computer host da condividere con Docker. Lo screenshot seguente illustra la condivisione dell'unità D nel sistema Windows:

![Installare Docker][img-install-docker]

Creare quindi una cartella denominata **docker** nella radice dell'unità condivisa.
È anche possibile eseguire questo passaggio dal menu **Settings** (Impostazioni) dopo avere installato Docker.

### <a name="configure-the-gateway"></a>Configurare il gateway

1. È necessaria la stringa di connessione **iothubowner** della distribuzione della soluzione di connected factory di Azure IoT Suite per completare la distribuzione del gateway. Nel [portale di Azure] passare all'hub IoT nel gruppo di risorse creato quando si è distribuita la soluzione di connected factory. Fare clic su **Criteri di accesso condiviso** per accedere alla stringa di connessione **iothubowner**:

    ![Trovare la stringa di connessione dell'hub IoT][img-hub-connection]

    Copiare il valore di **Stringa di connessione - chiave primaria**.

1. Configurare il gateway per l'hub IoT eseguendo i due moduli del gateway **una sola volta** da un prompt dei comandi con:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** è il nome dell'applicazione OPC UA creata dal gateway nel formato **publisher.&lt;nome di dominio completo&gt;**. Ad esempio, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** è la stringa di connessione **iothubowner** copiata nel passaggio precedente. Questa stringa di connessione viene usata solo in questo passaggio e non sarà più necessaria.

    La cartella D:\\docker di cui è stato eseguito il mapping (l'argomento `-v`) verrà usata più avanti per rendere permanenti i due certificati X.509 usati dai moduli del gateway.

### <a name="run-the-gateway"></a>Eseguire il gateway

1. Riavviare il gateway usando i comandi seguenti:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e \_GW\_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Per motivi di sicurezza, i due certificati X.509 resi permanenti nella cartella D:\\docker contengono la chiave privata. L'accesso a questa cartella deve essere limitato alle credenziali, in genere **Administrators** (Amministratori) usate per eseguire il contenitore Docker. Fare clic con il pulsante destro del mouse sulla cartella D:\\docker, scegliere **Properties** (Proprietà), quindi **Security** (Sicurezza) e infine **Edit** (Modifica). Assegnare ad **Administrators** (Amministratori) il controllo completo e rimuovere tutti gli altri utenti:

    ![Concedere autorizzazioni alla condivisione Docker][img-docker-share]

1. Verificare la connettività di rete. Provare a effettuare il ping del gateway. Al prompt dei comandi immettere il comando `ping publisher.<your fully qualified domain name>`. Se la destinazione non è raggiungibile, aggiungere l'indirizzo IP e il nome del gateway al file hosts nel gateway. Il file hosts si trova nella cartella "Windows\\System32\\drivers\\etc".

1. Provare quindi a connettersi al server di pubblicazione usando un client OPC UA locale in esecuzione nel gateway. L'URL dell'endpoint OPC UA è `opc.tcp://publisher.<your fully qualified domain name>:62222`. Se non si ha un client OPC UA, è possibile scaricare un [client OPC UA open source].

1. Dopo avere completato correttamente questi test locali, passare alla pagina **Connect your own OPC UA Server** (Connetti il server OPC UA) nel portale della soluzione di connected factory. Immettere l'URL dell'endpoint del server di pubblicazione (`tcp://publisher.<your fully qualified domain name>:62222`) e fare clic su **Connect** (Connetti). Viene visualizzato un avviso relativo al certificato. Fare clic su **Proceed** (Continua). Viene quindi visualizzato un errore che informa che il server di pubblicazione non considera attendibile il client Web UA. Per risolvere questo errore, copiare il certificato **UA Web Client** dalla cartella "D:\\docker\\Rejected Certificates\\certs" alla cartella "D:\\docker\\UA Applications\\certs" nel gateway. Non è necessario riavviare il gateway. Ripetere questo passaggio. Ora è possibile connettersi al gateway dal cloud e aggiungere i server OPC UA alla soluzione.

### <a name="add-your-opc-ua-servers"></a>Aggiungere i server OPC UA

1. Passare alla pagina **Connect your own OPC UA Server** (Connetti il server OPC UA) nel portale della soluzione di connected factory. Seguire gli stessi passaggi della sezione precedente per stabilire una relazione di trust tra il portale della soluzione di connected factory e il server OPC UA. Questo passaggio stabilisce una relazione di trust reciproco dei certificati dal portale della soluzione di connected factory al server OPC UA e crea una connessione.

1. Passare all'albero di nodi OPC UA del server OPC UA, fare clic con il pulsante destro del mouse sui nodi OPC e scegliere **publish** (Pubblica). Perché la pubblicazione possa funzionare in questo modo, il server OPC UA e il server di pubblicazione devono essere nella stessa rete. In altre parole, se il nome di dominio completo del server di pubblicazione è **publisher.mydomain.com**, il nome di dominio completo del server OPC UA deve essere, ad esempio, **myopcuaserver.mydomain.com**. Se la configurazione in uso è diversa, è possibile aggiungere manualmente nodi al file publishesnodes.json presente nella cartella D:\\docker. publishesnodes.json viene automaticamente generato alla prima pubblicazione corretta di un nodo OPC.

1. I dati di telemetria ora passano dal dispositivo gateway. È possibile visualizzare i dati di telemetria nella visualizzazione **Factory Locations** (Posizioni factory) del portale della soluzione di connected factory in **New Factory** (Nuova factory).

## <a name="linux-deployment"></a>Distribuzione in Linux

> [!NOTE]
> Se non si ha ancora un dispositivo gateway, Microsoft consiglia di acquistare un gateway commerciale da uno dei partner. Vedere il [catalogo dei dispositivi Azure per IoT] per un elenco di dispositivi gateway compatibili con la soluzione di connected factory. Seguire le istruzioni fornite con il dispositivo per configurare il gateway. In alternativa, usare le istruzioni seguenti per configurare manualmente uno dei gateway esistenti.

[Installare Docker] nel dispositivo gateway Linux.

### <a name="configure-the-gateway"></a>Configurare il gateway

1. È necessaria la stringa di connessione **iothubowner** della distribuzione della soluzione di connected factory di Azure IoT Suite per completare la distribuzione del gateway. Nel [portale di Azure] passare all'hub IoT nel gruppo di risorse creato quando si è distribuita la soluzione di connected factory. Fare clic su **Criteri di accesso condiviso** per accedere alla stringa di connessione **iothubowner**:

    ![Trovare la stringa di connessione dell'hub IoT][img-hub-connection]

    Copiare il valore di **Stringa di connessione - chiave primaria**.

1. Configurare il gateway per l'hub IoT eseguendo i due moduli del gateway **una sola volta** da una shell con:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** è il nome dell'applicazione OPC UA creata dal gateway nel formato **publisher.&lt;nome di dominio completo&gt;**. Ad esempio, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** è la stringa di connessione **iothubowner** copiata nel passaggio precedente. Questa stringa di connessione viene usata solo in questo passaggio e non sarà più necessaria.

    La cartella /shared di cui è stato eseguito il mapping (l'argomento `-v`) verrà usata più avanti per rendere permanenti i due certificati X.509 usati dai moduli del gateway.

### <a name="run-the-gateway"></a>Eseguire il gateway

1. Riavviare il gateway usando i comandi seguenti:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Per motivi di sicurezza, i due certificati X.509 resi permanenti nella cartella /shared contengono la chiave privata. L'accesso a questa cartella deve essere limitato alle credenziali usate per eseguire il contenitore Docker. Per impostare le autorizzazioni solo per **root**, usare il comando della shell `chmod` sulla cartella.

1. Verificare la connettività di rete. Provare a effettuare il ping del gateway. In una shell immettere il comando `ping publisher.<your fully qualified domain name>`. Se la destinazione non è raggiungibile, aggiungere l'indirizzo IP e il nome del gateway al file hosts nel gateway. Il file hosts si trova in /etc.

1. Provare quindi a connettersi al server di pubblicazione usando un client OPC UA locale in esecuzione nel gateway. L'URL dell'endpoint OPC UA è `opc.tcp://publisher.<your fully qualified domain name>:62222`. Se non si ha un client OPC UA, è possibile scaricare un [client OPC UA open source].

1. Dopo avere completato correttamente questi test locali, passare alla pagina **Connect your own OPC UA Server** (Connetti il server OPC UA) nel portale della soluzione di connected factory. Immettere l'URL dell'endpoint del server di pubblicazione (`tcp://publisher.<your fully qualified domain name>:62222`) e fare clic su **Connect** (Connetti). Viene visualizzato un avviso relativo al certificato. Fare clic su **Proceed** (Continua). Viene quindi visualizzato un errore che informa che il server di pubblicazione non considera attendibile il client Web UA. Per risolvere questo errore, copiare il certificato **UA Web Client** dalla cartella "/shared/Rejected Certificates/certs" alla cartella "/shared/UA Applications/certs" nel gateway. Non è necessario riavviare il gateway. Ripetere questo passaggio. Ora è possibile connettersi al gateway dal cloud e aggiungere i server OPC UA alla soluzione.

### <a name="add-your-opc-ua-servers"></a>Aggiungere i server OPC UA

1. Passare alla pagina **Connect your own OPC UA Server** (Connetti il server OPC UA) nel portale della soluzione di connected factory. Seguire gli stessi passaggi della sezione precedente per stabilire una relazione di trust tra il portale della soluzione di connected factory e il server OPC UA. Questo passaggio stabilisce una relazione di trust reciproco dei certificati dal portale della soluzione di connected factory al server OPC UA e crea una connessione.

1. Passare all'albero di nodi OPC UA del server OPC UA, fare clic con il pulsante destro del mouse sui nodi OPC e scegliere **publish** (Pubblica). Perché la pubblicazione possa funzionare in questo modo, il server OPC UA e il server di pubblicazione devono essere nella stessa rete. In altre parole, se il nome di dominio completo del server di pubblicazione è **publisher.mydomain.com**, il nome di dominio completo del server OPC UA deve essere, ad esempio, **myopcuaserver.mydomain.com**. Se la configurazione in uso è diversa, è possibile aggiungere manualmente nodi al file publishesnodes.json presente nella cartella /shared. publishesnodes.json viene automaticamente generato alla prima pubblicazione corretta di un nodo OPC.

1. I dati di telemetria ora passano dal dispositivo gateway. È possibile visualizzare i dati di telemetria nella visualizzazione **Factory Locations** (Posizioni factory) del portale della soluzione di connected factory in **New Factory** (Nuova factory).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'architettura della soluzione preconfigurata di connected factory, vedere [Procedura dettagliata per la soluzione preconfigurata di connected factory][lnk-walkthrough].

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker per Windows]: https://www.docker.com/docker-windows
[catalogo dei dispositivi Azure per IoT]: https://catalog.azureiotsuite.com/?q=opc
[portale di Azure]: http://portal.azure.com/
[client OPC UA open source]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Installare Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-overview.md

