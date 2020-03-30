---
title: Metodi diretti edgeAgent incorporati - Azure IoT EdgeBuilt-in edgeDirect methods - Azure IoT Edge
description: Monitorare e gestire una distribuzione di IoT Edge usando i metodi diretti incorporati nel modulo di runtime dell'agente IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240355"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicare con edgeAgent utilizzando i metodi diretti incorporati

Monitorare e gestire le distribuzioni di IoT Edge utilizzando i metodi diretti inclusi nel modulo dell'agente IoT Edge. I metodi diretti vengono implementati nel dispositivo e quindi possono essere richiamati dal cloud. L'agente IoT Edge include metodi diretti che consentono di monitorare e gestire i dispositivi IoT Edge in remoto.

Per ulteriori informazioni sui metodi diretti, su come utilizzarli e su come implementarli nei propri moduli, vedere [Comprendere e richiamare metodi diretti dall'hub IoT](../iot-hub/iot-hub-devguide-direct-methods.md).

I nomi di questi metodi diretti vengono gestiti senza distinzione tra maiuscole e minuscole.

## <a name="ping"></a>Ping

Il metodo ping è utile per verificare se IoT Edge è in esecuzione su un dispositivo o se il dispositivo dispone di una connessione aperta all'hub IoT.The **ping** method is useful for checking whether IoT Edge is running on a device, or whether the device has an open connection to IoT Hub. Utilizzare questo metodo diretto per eseguire il ping dell'agente IoT Edge e ottenerlo. Un ping riuscito restituisce un payload vuoto e **"status": 200**.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Nel portale di Azure richiamare il `ping` metodo con `{}`il nome del metodo e un payload JSON vuoto.

![Richiamare il metodo diretto 'ping' nel portale di AzureInvoke direct method 'ping' in Azure portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Modulo di riavvio

Il metodo **RestartModule** consente la gestione remota dei moduli in esecuzione in un dispositivo IoT Edge. Se un modulo segnala uno stato non riuscito o un altro comportamento non integro, è possibile attivare l'agente IoT Edge per riavviarlo. Un comando restart riuscito restituisce un payload vuoto e **"status": 200**.

Il metodo RestartModule è disponibile in IoT Edge versione 1.0.9 e successive. 

È possibile utilizzare il metodo diretto RestartModule in qualsiasi modulo in esecuzione su un dispositivo IoT Edge, incluso il modulo edgeAgent stesso. Tuttavia, se si utilizza questo metodo diretto per arrestare edgeAgent, non si riceverà un risultato positivo poiché la connessione viene interrotta durante il riavvio del modulo.

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

Nel portale di Azure richiamare il `RestartModule` metodo con il nome del metodo e il payload JSON seguente:In the Azure portal, invoke the method with the method name and the following JSON payload:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Richiamare il metodo diretto 'RestartModule' nel portale di AzureInvoke direct method 'RestartModule' in Azure portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Metodi sperimentali

Sono disponibili nuove opzioni di metodo diretto come funzionalità sperimentali da testare, tra cui:

* [UploadLogs:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)recuperare i log dei moduli e caricarli in Archiviazione BLOB di Azure.UploadLogs : Retrieve module logs and upload them to Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Controllare lo stato di una richiesta di caricamento dei log.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): recupera i log dei moduli inline nella risposta del metodo diretto.

## <a name="next-steps"></a>Passaggi successivi

[Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"](module-edgeagent-edgehub.md)
