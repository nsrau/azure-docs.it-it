---
title: Scrivere createOptions per i moduli - Azure IoT Edge Documenti Microsoft
description: Come usare createOptions nel manifesto di distribuzione per configurare i moduli in fase di esecuzioneHow to use createOptions in the deployment manifest to configure modules at runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550344"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Come configurare le opzioni di creazione del contenitore per i moduli IoT Edge

Il parametro **createOptions** nel manifesto di distribuzione consente di configurare i contenitori di moduli in fase di esecuzione. Questo parametro espande il controllo sui moduli e consente attività quali l'autorizzazione o la limitazione dell'accesso del modulo alle risorse del dispositivo host o la configurazione della rete.

I moduli IoT Edge vengono implementati come contenitori compatibili con Docker nel dispositivo IoT Edge. Docker offre molte opzioni per la creazione di contenitori e queste opzioni si applicano anche ai moduli IoT Edge. Per ulteriori informazioni, vedere Opzioni di [creazione del contenitore Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Opzioni di creazione del formato

Il manifesto di distribuzione di IoT Edge accetta le opzioni di creazione formattate come JSON. Ad esempio, prendi le opzioni di creazione che vengono incluse automaticamente per ogni modulo edgeHub:

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

In questo esempio edgeHub viene utilizzato il parametro **HostConfig.PortBindings** per eseguire il mapping delle porte esposte nel contenitore a una porta nel dispositivo host.

Se si usano le estensioni di Azure IoT Tools per Visual Studio o Visual Studio Code, è possibile scrivere le opzioni di creazione in formato JSON nel file **deployment.template.json.** Quindi, quando si usa l'estensione per compilare la soluzione IoT Edge o generare il manifesto di distribuzione, verrà eseguito il codice JSON nel formato previsto dal runtime di IoT Edge. Ad esempio:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Un suggerimento per la scrittura `docker inspect` delle opzioni di creazione consiste nell'utilizzare il comando. Come parte del processo di sviluppo, `docker run <container name>`eseguire il modulo in locale utilizzando . Una volta che il modulo funziona nel `docker inspect <container name>`modo desiderato, eseguire . Questo comando restituisce i dettagli del modulo in formato JSON. Individuare i parametri configurati e copiare il codice JSON. Ad esempio:

[![Risultati di docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) ispezionare edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Scenari comuni

Le opzioni di creazione del contenitore consentono molti scenari, ma ecco alcuni che vengono più spesso quando si creano soluzioni IoT Edge:Container create options enable many scenarios, but here are some that come up most often when building IoT Edge solutions:

* [Concedere ai moduli l'accesso all'archiviazione host](how-to-access-host-storage-from-module.md)
* [Mappare la porta host alla porta del moduloMap host port to module port](#map-host-port-to-module-port)
* [Limitare la memoria del modulo e l'utilizzo della CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mappare la porta host alla porta del moduloMap host port to module port

Se il modulo deve comunicare con un servizio esterno alla soluzione IoT Edge e non utilizza il routing dei messaggi a tale scopo, è necessario eseguire il mapping di una porta host a una porta del modulo.

>[!TIP]
>Questo mapping delle porte non è necessario per la comunicazione da modulo a modulo sullo stesso dispositivo. Se il modulo A deve eseguire una query su un'API ospitata nel modulo B, può farlo senza alcun mapping delle porte. Il modulo B deve esporre una porta nel `EXPOSE 8080`relativo file docker, ad esempio: . Il modulo A può quindi eseguire una query sull'API utilizzando il nome del modulo B, ad esempio: `http://ModuleB:8080/api`.

In primo luogo, assicurarsi che una porta all'interno del modulo è esposta per l'ascolto delle connessioni. È possibile eseguire questa operazione utilizzando un'istruzione [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) nel file dockerfile. Ad esempio: `EXPOSE 8080`. L'istruzione expose per impostazione predefinita al protocollo TCP, se non specificato, oppure è possibile specificare UDP.

Quindi, utilizzare l'impostazione **PortBindings** nel gruppo **HostConfig** del [contenitore Docker creare opzioni](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) per eseguire il mapping della porta esposta nel modulo a una porta nel dispositivo host. Ad esempio, se è stata esposta la porta 8080 all'interno del modulo e si desidera eseguire il mapping alla porta 80 del dispositivo host, le opzioni di creazione nel file template.json sono simili all'esempio seguente:

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

Una volta eseguito il rigoroso per il manifesto di distribuzione, la stessa configurazione sarà simile all'esempio seguente:Once stringed for the deployment manifest, the same configuration would look like the following example:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Limitare la memoria del modulo e l'utilizzo della CPU

È possibile dichiarare la quantità di risorse host che un modulo può utilizzare. Questo controllo è utile per garantire che un modulo non possa consumare troppa memoria o utilizzo della CPU e impedire l'esecuzione di altri processi nel dispositivo. È possibile gestire queste impostazioni con il [contenitore Docker per creare opzioni](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) nel gruppo **HostConfig,** tra cui:You can manage these settings with Docker container create options in the HostConfig group, including:

* **Memoria**: Limite di memoria in byte. Ad esempio, 268435456 byte e 256 MB.
* **MemorySwap**: Limite di memoria totale (memoria e swap). Ad esempio, 536870912 byte - 512 MB
* **CpuPeriod**: La durata di un periodo della CPU in microsecondi. Il valore predefinito è 100000, pertanto, ad esempio, un valore pari a 25000 limita un contenitore al 25% delle risorse della CPU.

Nel formato template.json, questi valori sono simili all'esempio seguente:In the template.json format, these values would look like the following example:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Una volta rigorosi per il manifesto di distribuzione finale, questi valori sarebbero simili all'esempio seguente:Once stringed for the final deployment manifest, these values would look like the following example:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di opzioni di creazione in azione, vedere i seguenti esempi di IoT Edge:

* [Visione personalizzata e azure IoT Edge in un Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Esempio di archiviazione blob di Azure IoT EdgeAzure IoT Edge blob storage sample](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
