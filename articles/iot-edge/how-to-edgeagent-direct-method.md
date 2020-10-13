---
title: Metodi diretti edgeAgent predefiniti-Azure IoT Edge
description: Monitorare e gestire una distribuzione di IoT Edge usando metodi diretti predefiniti nel modulo di runtime di IoT Edge Agent
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240355"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicare con edgeAgent usando metodi diretti predefiniti

Monitorare e gestire le distribuzioni di IoT Edge usando i metodi diretti inclusi nel modulo IoT Edge Agent. I metodi diretti vengono implementati nel dispositivo e quindi possono essere richiamati dal cloud. L'agente IoT Edge include metodi diretti che consentono di monitorare e gestire i dispositivi di IoT Edge in modalità remota.

Per altre informazioni sui metodi diretti, su come usarli e su come implementarli nei moduli personali, vedere [comprendere e richiamare metodi diretti dall'hub](../iot-hub/iot-hub-devguide-direct-methods.md)Internet.

I nomi di questi metodi diretti vengono gestiti senza distinzione tra maiuscole e minuscole.

## <a name="ping"></a>Ping

Il metodo **ping** è utile per verificare se IOT Edge è in esecuzione in un dispositivo o se il dispositivo ha una connessione aperta all'hub Internet. Utilizzare questo metodo diretto per effettuare il ping dell'agente IoT Edge e ottenerne lo stato. Un Ping riuscito restituisce un payload vuoto e **"status": 200**.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `ping` e un payload JSON vuoto `{}` .

![Richiama il metodo diretto ' ping ' in portale di Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Riavvia modulo

Il metodo **RestartModule** consente la gestione remota dei moduli in esecuzione su un dispositivo IOT Edge. Se un modulo segnala uno stato di errore o un altro comportamento non integro, è possibile attivare l'agente di IoT Edge per riavviarlo. Un comando di riavvio riuscito restituisce un payload vuoto e **"status": 200**.

Il metodo RestartModule è disponibile nella versione IoT Edge 1.0.9 e versioni successive. 

È possibile usare il metodo diretto RestartModule su qualsiasi modulo eseguito in un dispositivo IoT Edge, incluso il modulo edgeAgent stesso. Tuttavia, se si usa questo metodo diretto per arrestare il edgeAgent, non si riceverà un risultato positivo perché la connessione viene interrotta durante il riavvio del modulo.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `RestartModule` e il payload JSON seguente:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Richiama il metodo diretto ' RestartModule ' in portale di Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Metodi sperimentali

Sono disponibili nuove opzioni di metodo diretto come funzionalità sperimentali per il test, tra cui:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): recuperare i registri dei moduli e caricarli nell'archivio BLOB di Azure.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): controllare lo stato di una richiesta di caricamento dei log.
* [Getlogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): recupera i registri dei moduli inline nella risposta del metodo diretto.

## <a name="next-steps"></a>Passaggi successivi

[Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"](module-edgeagent-edgehub.md)
