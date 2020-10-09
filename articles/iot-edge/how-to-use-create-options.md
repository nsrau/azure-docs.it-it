---
title: Scrivere createOptions per i moduli-Azure IoT Edge | Microsoft Docs
description: Come usare createOptions nel manifesto della distribuzione per configurare i moduli in fase di esecuzione
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550344"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Come configurare le opzioni di creazione di contenitori per i moduli IoT Edge

Il parametro **createOptions** nel manifesto di distribuzione consente di configurare i contenitori dei moduli in fase di esecuzione. Questo parametro espande il controllo sui moduli e consente attività come consentire o limitare l'accesso del modulo alle risorse del dispositivo host oppure configurare la rete.

IoT Edge moduli sono implementati come contenitori compatibili con Docker nel dispositivo IoT Edge. Docker offre molte opzioni per la creazione di contenitori e queste opzioni si applicano anche ai moduli IoT Edge. Per altre informazioni, vedere [Opzioni di creazione di contenitori Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Opzioni di creazione formato

Il manifesto di distribuzione di IoT Edge accetta le opzioni di creazione formattate come JSON. Ad esempio, eseguire le opzioni create incluse automaticamente per ogni modulo edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Questo esempio di edgeHub usa il parametro **Hostconfig. PortBindings** per eseguire il mapping delle porte esposte nel contenitore a una porta sul dispositivo host.

Se si usano le estensioni degli strumenti di Azure per Visual Studio o Visual Studio Code, è possibile scrivere le opzioni di creazione in formato JSON nell' **deployment.template.jssu** file. Quindi, quando si usa l'estensione per compilare la soluzione IoT Edge o generare il manifesto di distribuzione, stringify il codice JSON nel formato previsto dal runtime di IoT Edge. Ad esempio:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Un suggerimento per la scrittura di opzioni di creazione consiste nell'usare il `docker inspect` comando. Nell'ambito del processo di sviluppo eseguire il modulo localmente usando `docker run <container name>` . Quando il modulo funziona nel modo desiderato, eseguirlo `docker inspect <container name>` . Questo comando restituisce i dettagli del modulo in formato JSON. Trovare i parametri configurati e copiare il codice JSON. Ad esempio:

[![Risultati dell'ispezione di Docker edgeHub ](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Scenari comuni

Le opzioni di creazione del contenitore consentono molti scenari, ma di seguito sono riportate più spesso quando si compilano soluzioni IoT Edge:

* [Consentire ai moduli di accedere all'archiviazione host](how-to-access-host-storage-from-module.md)
* [Eseguire il mapping della porta host alla porta del modulo](#map-host-port-to-module-port)
* [Limitare l'utilizzo della memoria e della CPU del modulo](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Eseguire il mapping della porta host alla porta del modulo

Se il modulo deve comunicare con un servizio esterno alla soluzione IoT Edge e non usa il routing dei messaggi a tale scopo, è necessario eseguire il mapping di una porta host a una porta del modulo.

>[!TIP]
>Questo mapping delle porte non è necessario per la comunicazione da modulo a modulo sullo stesso dispositivo. Se il modulo A deve eseguire una query su un'API ospitata sul modulo B, questa operazione può essere eseguita senza alcun mapping delle porte. Il modulo B deve esporre una porta nella relativa dockerfile, ad esempio: `EXPOSE 8080` . Il modulo A può quindi eseguire una query sull'API usando il nome del modulo B, ad esempio: `http://ModuleB:8080/api` .

Assicurarsi prima di tutto che una porta all'interno del modulo sia esposta per restare in ascolto delle connessioni. A tale scopo, è possibile usare un'istruzione [Expose](https://docs.docker.com/engine/reference/builder/#expose) in dockerfile. Ad esempio: `EXPOSE 8080`. Se non è specificata, l'istruzione Expose viene utilizzata per impostazione predefinita sul protocollo TCP oppure è possibile specificare UDP.

Quindi, usare l'impostazione **PortBindings** nel gruppo **Hostconfig** delle opzioni di [creazione del contenitore Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) per eseguire il mapping della porta esposta nel modulo a una porta sul dispositivo host. Se ad esempio è stata esposta la porta 8080 all'interno del modulo e si vuole eseguirne il mapping alla porta 80 del dispositivo host, le opzioni di creazione nel template.jsnel file avranno un aspetto simile all'esempio seguente:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Una volta file per il manifesto di distribuzione, la stessa configurazione sarà simile all'esempio seguente:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Limitare l'utilizzo della memoria e della CPU del modulo

È possibile dichiarare la quantità di risorse host che un modulo può utilizzare. Questo controllo è utile per garantire che un modulo non possa utilizzare troppa memoria o utilizzo della CPU e impedire l'esecuzione di altri processi sul dispositivo. È possibile gestire queste impostazioni con le [Opzioni di creazione di contenitori Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) nel gruppo **Hostconfig** , tra cui:

* **Memoria**: limite di memoria in byte. Ad esempio, 268435456 byte = 256 MB.
* **MemorySwap**: limite di memoria totale (memoria + scambio). Ad esempio, 536870912 byte = 512 MB
* **CpuPeriod**: lunghezza di un periodo di CPU in microsecondi. Il valore predefinito è 100000, quindi, ad esempio, il valore 25000 limita un contenitore al 25% delle risorse della CPU.

Nel template.jsin formato, questi valori avranno un aspetto simile all'esempio seguente:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Una volta file per il manifesto finale della distribuzione, questi valori avranno un aspetto simile all'esempio seguente:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di creazione di opzioni in azione, vedere gli esempi di IoT Edge seguenti:

* [Visione personalizzata e Azure IoT Edge in un dispositivo Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Esempio di archiviazione BLOB Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
