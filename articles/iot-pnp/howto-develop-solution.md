---
title: Interagire con un dispositivo di anteprima Plug and Play IoT da una soluzione Azure IoT | Microsoft Docs
description: Uno sviluppatore di dispositivi può apprendere come usare l'SDK del servizio per interagire con i dispositivi Plug and Play IoT.
author: Philmea
ms.author: philmea
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 543f332087aef4147c9274ca980cb56543a68112
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977591"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Connettersi e interagire con un dispositivo di anteprima Plug and Play IoT

Questa guida pratica illustra come usare gli esempi nell'SDK del servizio Node che mostrano come la soluzione IoT può interagire con i dispositivi di anteprima Plug and Play IoT.

Se non è ancora stato completato l'argomento di avvio rapido [Connettere un dispositivo Plug and Play IoT alla soluzione](quickstart-connect-pnp-device-solution.md), è necessario eseguire questa operazione ora. L'argomento di avvio rapido illustra come scaricare e installare l'SDK ed eseguire alcuni degli esempi.

Prima di eseguire gli esempi di servizio, aprire un nuovo terminale, passare alla cartella radice del repository clonato, passare alla cartella **digitaltwins/quickstarts/service**, quindi eseguire il comando seguente per installare le dipendenze:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Eseguire gli esempi di servizio

Usare gli esempi seguenti per esplorare le funzionalità di Node.js Service SDK. Assicurarsi che la variabile di ambiente `IOTHUB_CONNECTION_STRING` sia configurata nella shell usata:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Recuperare un dispositivo gemello digitale ed elencare le interfacce

**get_digital_twin.js** ottiene il gemello digitale associato al dispositivo e stampa il componente nella riga di comando. Non è necessario che un esempio di dispositivo in esecuzione abbia esito positivo.

**get_digital_twin_interface_instance.js** ottiene una singola istanza dell'interfaccia del gemello digitale associato al dispositivo e stampa il componente nella riga di comando. Non è necessario eseguire l'esempio di dispositivo.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Ottenere e impostare le proprietà usando l'SDK del servizio Node

**update_digital_twin.js** aggiorna una proprietà scrivibile sul dispositivo gemello digitale usando una patch completa. Se si vuole, è possibile aggiornare più proprietà in più interfacce. Per avere esito positivo, l'esempio di dispositivo deve essere in esecuzione nello stesso momento. Per esito positivo si intende quando l'esempio di dispositivo stampa qualcosa sull'aggiornamento di una proprietà e l'esempio di servizio stampa un gemello digitale aggiornato nel terminale.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Inviare un comando e recuperare la risposta con l'SDK del servizio Node

**invoke_command.js** richiama un comando sincrono sul dispositivo gemello digitale. Per avere esito positivo, l'esempio di dispositivo deve essere in esecuzione nello stesso momento. Per esito positivo si intende quando l'esempio di dispositivo stampa qualcosa sul riconoscimento di un comando e il client di servizio stampa il risultato del comando nel terminale.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Connettersi al repository pubblico e recuperare una definizione del modello usando l'SDK del servizio Node

Usando le stesse istruzioni degli esempi di servizio e di dispositivo, è necessario impostare la variabile di ambiente seguente:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Questa stringa di connessione è reperibile nel [portale Microsoft Azure Certified per IoT](https://preview.catalog.azureiotsolutions.com) nella scheda **Stringhe di connessione** per il **repository aziendale**.

La stringa di connessione ha un aspetto simile all'esempio seguente:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Dopo aver impostato le quattro variabili di ambiente, eseguire l'esempio nello stesso modo in cui sono stati eseguiti gli altri esempi:

```cmd/sh
node model_repo.js
```

Questo esempio scarica l'interfaccia **ModelDiscovery** e stampa questo modello nel terminale.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Eseguire query nell'hub IoT in base a modelli di funzionalità e interfacce

Il linguaggio di query dell'hub IoT supporta `HAS_INTERFACE` e `HAS_CAPABILITYMODEL` come illustrato negli esempi seguenti:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Creazione di route di gemelli digitali

La soluzione può ricevere notifiche di eventi di modifica del dispositivo gemello digitale. Per sottoscrivere queste notifiche, usare la [funzionalità di routing dell'hub IoT](../iot-hub/iot-hub-devguide-endpoints.md) per inviare le notifiche a un endpoint, ad esempio l'archiviazione BLOB, Hub eventi o una coda del bus di servizio.

Per creare una route di dispositivi gemelli digitali:

1. Nel portale di Azure passare alla risorsa hub IoT.
1. Selezionare **Routing messaggi**.
1. Nella scheda **Route** selezionare **Aggiungi**.
1. Immettere un valore nel campo **Nome** e scegliere un **endpoint**. Se non è stato configurato un endpoint, selezionare **Aggiungi endpoint**.
1. Nell'elenco a discesa **Origine dati** selezionare **Eventi di modifica del dispositivo gemello digitale**.
1. Selezionare **Salva**.

Il codice JSON seguente mostra un esempio di un evento di modifica del dispositivo gemello digitale:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base sulle soluzioni che interagiscono con i dispositivi Plug and Play IoT, è consigliabile acquisire maggiori informazioni sull'[Individuazione dei modelli](concepts-model-discovery.md).
