---
title: Flussi dispositivo dell'hub IoT (anteprima) | Microsoft Docs
description: Panoramica dei flussi dispositivo dell'hub IoT.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: eba08521cb28a29a08c8e4e48f0ec321cb0397c7
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137292"
---
# <a name="iot-hub-device-streams-preview"></a>Device Streams di Hub IoT (anteprima)

I *flussi dispositivo* dell'hub IoT di Azure facilitano la creazione di tunnel TCP bidirezionali sicuri per un'ampia gamma di scenari di comunicazione da cloud a dispositivo. Un flusso di dispositivo viene filtrato da un *endpoint di streaming* dell'hub IoT che funge da proxy tra gli endpoint del servizio e del dispositivo. Questa configurazione, illustrata nel diagramma riportato di seguito, è particolarmente utile quando i dispositivi sono protetti da un firewall di rete o si trovano all'interno di una rete privata. Di conseguenza, i flussi dispositivo dell'hub IoT rispondono alla necessità dei clienti di raggiungere i dispositivi IoT in modo adatto al firewall e senza la necessità di aprire ampiamente le porte del firewall di rete in ingresso o in uscita.

!["Panoramica di flussi dispositivo dell'IoT Hub"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Usando i flussi dispositivo dell'hub IoT, i dispositivi rimarranno protetti e sarà sufficiente aprire le connessioni TCP in uscita per l'endpoint di streaming dell'hub IoT tramite la porta 443. Dopo aver stabilito un flusso, le applicazioni di lato servizio e di lato dispositivo avranno ognuna accesso a livello di codice a un oggetto client di WebSocket per inviare e ricevere byte non elaborati tra di loro. Le garanzie di affidabilità e ordinamento fornite da questo tunnel sono paragonabili a TCP.

## <a name="benefits"></a>Vantaggi

I flussi dispositivo dell'hub IoT offrono i vantaggi seguenti:

* **Connettività sicura e adatta al firewall:** i dispositivi IoT possono essere raggiunti dagli endpoint di servizio senza l'apertura della porta in entrata del firewall nel perimetro della rete o del dispositivo (è richiesta solo la connettività in uscita all'hub IoT tramite la porta 443).

* **Autenticazione:** sia il lato dispositivo che il lato servizio del tunnel devono essere autenticati con l'hub IoT usando le credenziali corrispondenti.

* **Crittografia:** per impostazione predefinita, i flussi dispositivo dell'hub IoT usano connessioni abilitate per TLS. Ciò garantisce che il traffico sia sempre crittografato indipendentemente dal fatto che l'applicazione usi o no la crittografia.

* **Semplicità di connettività:** in molti casi, grazie all'uso dei flussi dispositivo non è necessaria una configurazione complessa di reti private virtuali per abilitare la connettività ai dispositivi IoT.

* **Compatibilità con stack TCP/IP:** i flussi dispositivo dell'hub IoT possono supportare il traffico di applicazione TCP/IP. Ciò significa che una vasta gamma di protocolli proprietari e di protocolli basati su standard può sfruttare questa funzionalità.

* **Semplicità d'uso in configurazioni di rete privata:** il servizio può comunicare con un dispositivo tramite l'ID del dispositivo, anziché tramite l'indirizzo IP del dispositivo. Ciò è utile quando un dispositivo si trova all'interno di una rete privata e dispone di un indirizzo IP privato oppure quando l'indirizzo IP del dispositivo viene assegnato in modo dinamico e non è noto al lato servizio.

## <a name="device-stream-workflows"></a>Flussi di lavoro di dispositivo flusso

Un flusso dispositivo viene avviato quando il servizio richiede la connessione a un dispositivo fornendo il relativo ID dispositivo. Questo flusso di lavoro è adatto a un modello di comunicazione client/server, tra cui RDP e SSH in cui un utente vuole connettersi in remoto al server SSH o RDP in esecuzione nel dispositivo usando un programma client SSH o RDP.

Il processo di creazione del flusso dispositivo comporta una negoziazione tra il dispositivo, il servizio e gli endpoint principali e di streaming dell'hub IoT. Mentre l'endpoint principale dell'hub IoT gestisce la creazione di un flusso dispositivo, l'endpoint di streaming gestisce i flussi di traffico tra il servizio e il dispositivo.

### <a name="device-stream-creation-flow"></a>Flusso di creazione del flusso dispositivo

La creazione a livello di codice di un flusso dispositivo tramite SDK include i passaggi seguenti, illustrati anche nella figura riportata di seguito:

!["Processo di handshake flusso dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. L'applicazione del dispositivo registra anticipatamente un callback di cui ricevere notifica quando viene avviato un nuovo flusso dispositivo nel dispositivo. In genere questo passaggio si verifica quando il dispositivo viene avviato e si connette all'hub IoT.

2. Il programma del lato servizio avvia un flusso dispositivo quando necessario fornendo l'ID dispositivo (_non_ l'indirizzo IP).

3. L'hub IoT informa il programma del lato dispositivo richiamando il callback registrato nel passaggio 1. Il dispositivo può accettare o rifiutare la richiesta di avvio flusso. Questa logica può essere specifica per lo scenario dell'applicazione. Se la richiesta di streaming viene rifiutata dal dispositivo, l'hub IoT informa il servizio di conseguenza; in caso contrario, attenersi alla procedura seguente.

4. Il dispositivo crea una connessione TCP in uscita sicura per l'endpoint di streaming tramite la porta 443 e consente di aggiornare la connessione a un WebSocket. L'URL dell'endpoint di streaming, così come le credenziali da usare per l'autenticazione, vengono entrambe fornite dall'hub IoT al dispositivo come parte della richiesta inviata nel passaggio 3.

5. Il servizio riceve una notifica dell'accettazione del flusso da parte del dispositivo e procede alla creazione di un proprio client WebSocket per l'endpoint di streaming. In modo analogo riceve le informazioni sull'URL dell'endpoint di streaming e sull'autenticazione dall'hub IoT.

Nel processo di handshake precedente:

* Il processo di handshake deve essere completato entro 60 secondi (passaggi da 2 a 5); in caso contrario l'handshake avrà esito negativo con un timeout e il servizio verrà informato di conseguenza.

* Dopo il completamento del flusso di creazione di flusso precedente, l'endpoint di streaming funge da proxy e trasferisce il traffico tra il servizio e il dispositivo tramite i rispettivi WebSocket.

* Il dispositivo e il servizio necessitano entrambi di connettività in uscita per l'endpoint principale dell'hub IoT, così come per l'endpoint di streaming tramite la porta 443. L'URL di questi endpoint è disponibile nella scheda *Panoramica* del portale dell'hub IoT.

* Le garanzie di affidabilità e ordinamento di un flusso stabilito sono paragonabili a TCP.

* Tutte le connessioni all'hub IoT e agli endpoint di streaming usano TLS e sono crittografate.

### <a name="termination-flow"></a>Flusso di terminazione

Un flusso stabilito termina quando una delle connessioni TCP al gateway viene disconnessa (dal servizio o dal dispositivo). Ciò può avvenire volontariamente chiudendo il WebSocket nei programmi del servizio o del dispositivo oppure involontariamente in caso di errore di processo o di timeout della connettività di rete. Al termine della connessione del dispositivo o del servizio all'endpoint di streaming, anche l'altra connessione TCP verrà terminata (in modo forzato) e il servizio e il dispositivo saranno responsabili di ricreare il flusso, se necessario.

## <a name="connectivity-requirements"></a>Requisiti di connettività

Sia il lato dispositivo che il lato servizio di un flusso dispositivo devono essere in grado di stabilire connessioni abilitate per TLS con l'hub IoT e il relativo endpoint di streaming. Ciò richiede la connettività in uscita sulla porta 443 con questi endpoint. Il nome host associato a questi endpoint si può trovare nella scheda *Panoramica* dell'hub IoT, come illustrato nella figura seguente:

!["Endpoint di flusso del dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

In alternativa, è possibile recuperare le informazioni sugli endpoint usando l'interfaccia della riga di comando di Azure nella sezione delle proprietà dell'hub, in particolare chiavi `property.hostname` e `property.deviceStreams`.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

L'output è un oggetto JSON di tutti gli endpoint a cui il servizio e il dispositivo dell'hub potrebbero dover connettersi per stabilire un flusso di dispositivo.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Verificare che sia installata l'interfaccia della riga di comando di Azure versione 2.0.57 o successiva. È possibile scaricare la versione più recente dal [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pagina.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Consentire la connettività in uscita al dispositivo degli endpoint di streaming

Come accennato all'inizio di questo articolo, il dispositivo crea una connessione in uscita per endpoint di streaming dell'IoT Hub durante il processo di avvio del dispositivo flussi. I firewall nel dispositivo o nella relativa rete devono consentire la connettività in uscita al gateway di streaming tramite la porta 443 (la comunicazione avviene su una connessione WebSocket crittografata con TLS).

Il nome host dell'endpoint di streaming del dispositivo è reperibile nel portale dell'hub IoT nella scheda Panoramica. !["Endpoint di flusso del dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

In alternativa, è possibile trovare queste informazioni tramite l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Verificare che sia installata l'interfaccia della riga di comando di Azure versione 2.0.57 o successiva. È possibile scaricare la versione più recente dal [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pagina.
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Risolvere i problemi tramite i log attività di Device Streams

È possibile impostare backup dei log di monitoraggio di Azure per raccogliere i log di attività dei flussi di dispositivo nell'IoT Hub. Ciò può essere molto utile in scenari di risoluzione dei problemi.

Attenersi alla procedura seguente per configurare i log di monitoraggio di Azure per le attività flusso di dispositivo dell'IoT Hub:

1. Passare alla scheda *Impostazioni di diagnostica* nell'hub IoT e fare clic sul collegamento *Attiva diagnostica*.

   !["Abilitazione dei log di diagnostica"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Specificare un nome per le impostazioni di diagnostica e scegliere l'opzione *Invia a Log Analytics*. Si passerà per scegliere una risorsa dell'area di lavoro di Log Analitica esistente o crearne uno nuovo. Inoltre, controllare *DeviceStreams* nell'elenco.

    !["Abilita i log dei flussi di dispositivi"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. È ora possibile accedere ai log di flussi dispositivo nella scheda*Log* del portale dell'hub IoT. I log attività di flusso dispositivo verranno visualizzati nella tabella `AzureDiagnostics` e avranno `Category=DeviceStreams`.

   Come illustrato di seguito, l'identità del dispositivo di destinazione e il risultato dell'operazione disponibile anche nei log.

   !["Log di flusso di accesso dispositivo"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Disponibilità a livello di area

Durante l'anteprima pubblica, i flussi dispositivo dell'hub IoT sono disponibili nelle aree Stati Uniti centrali e Stati Uniti centrali EUAP. Assicurarsi quindi che l'hub venga creato in una di queste aree.

## <a name="sdk-availability"></a>Disponibilità di SDK

I due lati di ogni flusso (lato servizio e lato dispositivo) usano l'SDK dell'hub IoT per stabilire il tunnel. Durante l'anteprima pubblica, i clienti possono scegliere i linguaggi SDK seguenti:

* i linguaggi SDK C e C# supportano i flussi dispositivo sul lato dispositivo.

* L'SDK C# e Node.js supportano i flussi dispositivo sul lato servizio.

## <a name="iot-hub-device-stream-samples"></a>Esempi di flusso di dispositivo IoT Hub

Esistono due [esempi di avvio rapido](/azure/iot-hub) disponibile nella pagina dell'IoT Hub. Questi esempi illustrano l'utilizzo dei flussi di dispositivo dalle applicazioni.

* Il *echo* esempio viene illustrato utilizzabili a livello di flussi di dispositivo (per chiamare direttamente l'API SDK).

* L'esempio *proxy locale* illustra il tunneling del traffico di applicazioni client/server commerciali (ad esempio SSH, RDP o Web) tramite i flussi dispositivo.

Questi esempi sono descritti in modo dettagliato nelle sezioni seguenti.

### <a name="echo-sample"></a>Esempio echo

L'esempio echo illustra l'uso a livello di codice dei flussi dispositivo per inviare e ricevere byte tra le applicazioni di servizio e di dispositivo. Si noti che è possibile usare programmi di servizio e dispositivi in diverse lingue. Ad esempio, è possibile usare il programma di dispositivo C con il C# programma di servizio.

Ecco gli esempi di echo:

* [C#servizio e programma del servizio](quickstart-device-streams-echo-csharp.md)

* [Programma del servizio Node. js](quickstart-device-streams-echo-nodejs.md)

* [Programma dispositivo C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Esempio relativo al proxy locale (per SSH o RDP)

L'esempio relativo al proxy locale illustra un modo per abilitare il tunneling del traffico di un'applicazione esistente che prevede la comunicazione tra un programma client e un programma server. Questa configurazione funziona per protocolli client/server come SSH e RDP, in cui il lato servizio funge da client (eseguendo programmi client RDP o SSH) e il lato dispositivo funge da server (eseguendo programmi server RDP o daemon SSH).

Questa sezione descrive l'uso di flussi dispositivo per abilitare l'utente per SSH in un dispositivo tramite flussi dispositivo (nel caso di RDP o di altre applicazioni client/server la procedura è simile usando la porta corrispondente del protocollo).

La configurazione si basa su due programmi di*proxy locale* mostrati nella figura seguente, ovvero il *proxy locale per dispositivi* e il*proxy locale per servizi*. I programmi proxy locali sono tenuti a eseguire l'[handshake di avvio del flusso dispositivo](#device-stream-creation-flow) con l'hub IoT e a interagire con client SSH e daemon SSH usando normali socket client/server.

!["Configurazione di proxy dispositivo flusso per SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. L'utente esegue il proxy locale per servizi per avviare un flusso dispositivo nel dispositivo.

2. Il proxy locale per dispositivi accetta la richiesta di avvio del flusso e viene stabilito il tunnel per l'endpoint di streaming dell'hub IoT (come illustrato in precedenza).

3. Il proxy locale per dispositivi si connette all'endpoint di daemon SSH in ascolto dalla porta 22 nel dispositivo.

4. Il proxy locale per servizi è in ascolto da una porta designata in attesa di nuove connessioni SSH da parte dell'utente (nell'esempio viene usata la porta 2222, ma il proxy può essere configurato per qualsiasi altra porta disponibile). L'utente punta il client SSH alla porta del proxy locale per servizi in localhost.

### <a name="notes"></a>Note

* I passaggi precedenti guidano nel completamento di un tunnel end-to-end tra il client SSH (sul lato destro) e il daemon SSH (sul lato sinistro). Parte di questa connettività end-to-end prevede l'invio di traffico all'hub IoT tramite un flusso dispositivo.

* Nella figura le frecce indicano la direzione in cui vengono stabilite le connessioni tra gli endpoint. In particolare, si noti che non vi è nessuna connessione in ingresso che si dirige verso il dispositivo (questo viene spesso bloccato da un firewall).

* La scelta di usare la porta 2222 nel proxy locale per servizi è una scelta arbitraria. Il proxy può essere configurato per usare qualsiasi altra porta disponibile.

* La scelta della porta 22 è dipendente dal protocollo e specifiche per la connessione SSH in questo caso. Nel caso di RDP, è necessario usare la porta 3389. Questa può essere configurata nei programmi di esempio forniti.

Consultare i collegamenti seguenti per istruzioni su come eseguire i programmi di proxy locali nel linguaggio di propria scelta. Analogamente all'[esempio echo](#echo-sample), è possibile eseguire programmi proxy locali per dispositivi e per servizi in linguaggi diversi, poiché sono pienamente interoperativi.

* [C#servizio e programma del servizio](quickstart-device-streams-proxy-csharp.md)

* [Programma del servizio Node. js](quickstart-device-streams-proxy-nodejs.md)

* [Programma dispositivo C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per altre informazioni sui flussi di dispositivo.

> [!div class="nextstepaction"]
> [I flussi di dispositivo su IoT mostrano (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)