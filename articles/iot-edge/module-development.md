---
title: Sviluppare i moduli per Azure IoT Edge | Microsoft Docs
description: Sviluppare moduli personalizzati per Azure IoT Edge in grado di comunicare con il runtime e l'hub IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8907af07fff7b315eec263d38b686c17218ed9d2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445473"
---
# <a name="develop-your-own-iot-edge-modules"></a>Sviluppare moduli di IoT Edge personalizzati

I moduli di IoT Edge di Azure possono connettersi con altri servizi di Azure e contribuire alla pipeline di dati cloud più estesa. Questo articolo descrive come sviluppare i moduli per comunicare con il runtime di IoT Edge e l'hub IoT e di conseguenza il resto del cloud di Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente di runtime di IoT Edge

Il runtime di IoT Edge offre l'infrastruttura per integrare le funzionalità di più moduli di IoT Edge e distribuirle nei dispositivi IoT Edge. Qualsiasi programma può essere incluso in un pacchetto di IoT Edge. Per sfruttare appieno i vantaggi delle funzionalità di comunicazione e gestione IoT Edge, un programma in esecuzione in un modulo può usare l'SDK per dispositivi Azure per la connessione all'hub IoT Edge locale.
::: moniker range=">=iotedge-2020-11"
I moduli possono anche usare qualsiasi client MQTT per connettersi al broker MQTT locale dell'hub IoT Edge.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Creazione del pacchetto del programma come modulo di IoT Edge

Per distribuire il programma in un dispositivo di IoT Edge, è necessario prima eseguirne il contenitore ed eseguirlo con un motore compatibile con Docker. IoT Edge USA [Moby](https://github.com/moby/moby), il progetto open source dietro a Docker, come motore compatibile con Docker. Gli stessi parametri a cui si è abituati con Docker possono essere passati ai moduli IoT Edge. Per ulteriori informazioni, vedere [come configurare le opzioni di creazione di contenitori per IOT Edge moduli](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Tramite l'hub di IoT Edge

L'hub di IoT Edge offre due funzionalità principali: proxy per l'hub IoT e comunicazioni locali.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connessione all'hub di IoT Edge da un modulo

La connessione all'hub IoT Edge locale da un modulo prevede gli stessi passaggi di connessione di tutti i client. Per altre informazioni, vedere [connessione all'hub IOT Edge](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Per usare il routing IoT Edge tramite AMQP o MQTT, è possibile usare ModuleClient da Azure. Creare un'istanza di ModuleClient per connettere il modulo all'hub IoT Edge in esecuzione nel dispositivo, nello stesso modo in cui le istanze di DeviceClient connettono i dispositivi IoT all'hub IoT. Per altre informazioni sulla classe ModuleClient e sui relativi metodi di comunicazione, vedere le informazioni di riferimento sulle API per il linguaggio SDK preferito: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)o [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Per usare IoT Edge broker MQTT, è necessario portare il proprio client MQTT e avviare la connessione con le informazioni recuperate dall'API del carico di lavoro di IoT Edge daemon. <!--Need to add details here-->

Per ulteriori informazioni sulla scelta tra routing o pubblicazione/sottoscrizione con MQTT broker, vedere la pagina relativa alla [comunicazione locale](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitive broker MQTT

#### <a name="send-a-message-on-a-user-defined-topic"></a>Inviare un messaggio su un argomento definito dall'utente

Con il IoT Edge broker MQTT, è possibile pubblicare messaggi in qualsiasi argomento definito dall'utente. A tale scopo, autorizzare il modulo per la pubblicazione in argomenti specifici, quindi ottenere un token dall'API del carico di lavoro da usare come password durante la connessione al broker MQTT e infine pubblicare i messaggi sugli argomenti autorizzati con il client di MQTT desiderato.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Ricevere messaggi in un argomento definito dall'utente

Con IoT Edge broker MQTT, la ricezione di messaggi è simile. Assicurarsi prima di tutto che il modulo sia autorizzato a sottoscrivere argomenti specifici, quindi ottenere un token dall'API del carico di lavoro da usare come password durante la connessione al broker di MQTT e infine sottoscrivere i messaggi sugli argomenti autorizzati con il client MQTT di propria scelta.

::: moniker-end

### <a name="iot-hub-primitives"></a>Primitive di hub IoT

Hub IoT visualizza un'istanza del modulo analogamente a un dispositivo, nel senso che:

* Può inviare [messaggi da dispositivo a cloud](../iot-hub/iot-hub-devguide-messaging.md);
* Può ricevere [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) destinati in particolare alla propria identità.
* ha un modulo gemello distinto e isolato dal [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) e dagli altri moduli gemelli del dispositivo.

Attualmente, i moduli non possono ricevere messaggi da cloud a dispositivo o usare la funzionalità di caricamento file.

Quando si scrive un modulo, è possibile connettersi all'hub IoT Edge e usare le primitive dell'hub cose come si farebbe quando si usa l'hub Internet con un'applicazione per dispositivi. L'unica differenza tra IoT Edge moduli e le applicazioni per dispositivi Internet è che è necessario fare riferimento all'identità del modulo invece che all'identità del dispositivo.

#### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud

Un modulo IoT Edge può inviare messaggi al cloud tramite l'hub IoT Edge che funge da broker locale e propaga i messaggi nel cloud. Per consentire un'elaborazione complessa di messaggi da dispositivo a cloud, un modulo IoT Edge può anche intercettare ed elaborare i messaggi inviati da altri moduli o dispositivi all'hub IoT Edge locale e inviare nuovi messaggi con dati elaborati. È quindi possibile creare catene di moduli IoT Edge per compilare pipeline di elaborazione locali.

Per inviare messaggi di telemetria da dispositivo a cloud usando il routing, usare il ModuleClient dell'SDK Azure. Con l'SDK di Azure, ogni modulo ha il concetto di endpoint di *input* e di *output* del modulo, che viene mappato a argomenti MQTT speciali. Usare il `ModuleClient.sendMessageAsync` metodo e invierà messaggi nell'endpoint di output del modulo. Configurare quindi una route in edgeHub per inviare questo endpoint di output all'hub Internet.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

L'invio di messaggi di telemetria da dispositivo a cloud con MQTT Broker è simile alla pubblicazione di messaggi in argomenti definiti dall'utente, ma usando il seguente argomento speciale dell'hub per il modulo: `devices/<device_name>/<module_name>/messages/events` . Le autorizzazioni devono essere impostate in modo appropriato. Il Bridge MQTT deve essere configurato anche per l'invio dei messaggi in questo argomento al cloud.

::: moniker-end

Per elaborare i messaggi usando il routing, configurare prima di tutto una route per inviare messaggi provenienti da un altro endpoint (modulo o dispositivo) all'endpoint di input del modulo, quindi restare in ascolto dei messaggi sull'endpoint di input del modulo. Ogni volta che viene restituito un nuovo messaggio, una funzione di callback viene attivata dall'SDK di Azure. Elaborare il messaggio con questa funzione di callback e, facoltativamente, inviare nuovi messaggi nella coda dell'endpoint del modulo.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

L'elaborazione di messaggi tramite MQTT Broker è simile alla sottoscrizione di messaggi in argomenti definiti dall'utente, ma tramite l'IoT Edge argomenti speciali della coda di output del modulo: `devices/<device_name>/<module_name>/messages/events` . Le autorizzazioni devono essere impostate in modo appropriato. Facoltativamente, è possibile inviare nuovi messaggi sugli argomenti di propria scelta.

::: moniker-end

#### <a name="twins"></a>Elementi gemelli

I dispositivi gemelli sono una delle primitive fornite dall'hub Internet. Sono disponibili documenti JSON che archiviano le informazioni sullo stato, inclusi metadati, configurazioni e condizioni. Ogni modulo o dispositivo ha un proprio gemello.

Per ottenere un modulo gemello con l'SDK di Azure, chiamare il `ModuleClient.getTwin` metodo.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Per ottenere un modulo gemello con un client MQTT, è necessario un po' di più lavoro perché ottenere un gemello non è un modello di MQTT tipico. Il modulo deve prima sottoscrivere l'argomento speciale dell'hub Internet `$iothub/twin/res/#` . Questo nome di argomento viene ereditato dall'hub Internet e tutti i dispositivi e i moduli devono sottoscrivere lo stesso argomento. Non significa che i dispositivi ricevano il gemello tra loro. Hub e edgeHub sa quale gemello deve essere consegnato, anche se tutti i dispositivi sono in ascolto dello stesso nome di argomento. Una volta effettuata la sottoscrizione, è necessario che il modulo chieda il gemello pubblicando un messaggio nel seguente argomento speciale dell'hub degli argomenti con un ID richiesta `$iothub/twin/GET/?$rid=1234` . Questo ID della richiesta è un ID arbitrario, ovvero un GUID, che verrà inviato dall'hub di tutti insieme ai dati richiesti. Questo è il modo in cui un client può associare le richieste alle risposte. Il codice risultato è un codice di stato simile a HTTP, dove ha esito positivo è codificato come 200.

::: moniker-end

Per ricevere una patch del modulo gemello con l'SDK di Azure, implementare una funzione di callback e registrarla con il `ModuleClient.moduleTwinCallback` metodo dell'SDK di Azure, in modo che la funzione di callback venga attivata ogni volta che viene ricevuta una patch del dispositivo gemello.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Per ricevere una patch del modulo gemello con un client MQTT, il processo è molto simile alla ricezione di dispositivi gemelli completi: un client deve sottoscrivere l'argomento speciale Hub `$iothub/twin/PATCH/properties/desired/#` . Una volta effettuata la sottoscrizione, quando l'hub Internet delle cose invia una modifica della sezione desiderata del dispositivo gemello, il client lo riceve.

::: moniker-end

#### <a name="receive-direct-methods"></a>Ricezione di metodi diretti

Per ricevere un metodo diretto con l'SDK di Azure, implementare una funzione di callback e registrarla con il `ModuleClient.methodCallback` metodo dell'SDK di Azure, in modo che la funzione di callback venga attivata ogni volta che viene attivato un metodo diretto.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Per ricevere un metodo diretto con qualsiasi client MQTT, il processo è molto simile alla ricezione di patch gemelle. Il client deve confermare che ha ricevuto la chiamata e può restituire alcune informazioni nello stesso momento. Un argomento speciale dell'hub delle cose da sottoscrivere è `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Supporto per lingue e architetture

IoT Edge supporta più sistemi operativi, architetture di dispositivi e linguaggi di sviluppo, in modo da poter compilare lo scenario in base alle esigenze. Usare questa sezione per comprendere le opzioni per lo sviluppo di moduli di IoT Edge personalizzati. Per ulteriori informazioni sul supporto e sui requisiti degli strumenti per ogni lingua, vedere [preparare l'ambiente di sviluppo e test per IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Per tutte le lingue elencate nella tabella seguente, IoT Edge supporta lo sviluppo per i dispositivi AMD64 e ARM32 Linux.

| Linguaggio di sviluppo | Strumenti di sviluppo |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Il supporto per lo sviluppo e il debug per i dispositivi Linux ARM64 è in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per altre informazioni, vedere [Sviluppare ed eseguire il debug di moduli IoT Edge ARM64 in Visual Studio Code (anteprima)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Per tutte le lingue elencate nella tabella seguente, IoT Edge supporta lo sviluppo per i dispositivi Windows AMD64.

| Linguaggio di sviluppo | Strumenti di sviluppo |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (nessuna funzionalità di debug)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Passaggi successivi

[Preparare l'ambiente di sviluppo e test per IoT Edge](development-environment.md)

[Utilizzare Visual Studio per sviluppare moduli C# per IoT Edge](how-to-visual-studio-develop-module.md)

[Usare Visual Studio Code per sviluppare moduli per IoT Edge](how-to-vs-code-develop-module.md)

[Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md)
