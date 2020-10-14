---
title: Come gestire le cose Plug and Play i dispositivi gemelli digitali
description: Come gestire gli Internet delle cose Plug and Play dispositivo usando le API gemelle digitali
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5f1c52b764634f8086763aca67dfc32b507d2edd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042848"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Gestisci le cose Plug and Play i dispositivi gemelli digitali

Internet delle cose Plug and Play supporta **Get Digital Twin** e aggiorna le operazioni del dispositivo **digitale gemello** per gestire i gemelli digitali. È possibile usare le [API REST](/rest/api/iothub/service/digitaltwin) o uno degli SDK del [servizio](libraries-sdks.md).

Al momento della stesura di questo documento, la versione dell'API del dispositivo gemello digitale è `2020-09-30` .

## <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Un dispositivo Plug and Play implementa un modello descritto da [Digital gemells Definition Language V2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Gli sviluppatori di soluzioni possono usare l'API di aggiornamento dei dispositivi **digitali gemelli** per aggiornare lo stato del componente e le proprietà del dispositivo gemello digitale.

Il dispositivo Plug and Play usato come esempio in questo articolo implementa il modello di [controller di temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) con i componenti del [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

Il frammento di codice seguente mostra la risposta a una richiesta **Get Digital Twin** formattata come oggetto JSON. Per altre informazioni sul formato di dispositivi gemelli digitali, Plug and Play vedere l'articolo su come ottenere informazioni sui [gemelli digitali](./concepts-digital-twin.md#digital-twin-json-format):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

I dispositivi gemelli digitali consentono di aggiornare un intero componente o una proprietà usando una [patch JSON](http://jsonpatch.com/).

Ad esempio, è possibile aggiornare la `targetTemperature` proprietà come indicato di seguito:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Con l'aggiornamento precedente viene impostato il valore desiderato di una proprietà nel livello del componente corrispondente `$metadata` , come illustrato nel frammento di codice seguente. L'hub Internet Aggiorna la versione desiderata della proprietà:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Aggiungere, sostituire o rimuovere un componente

Le operazioni a livello di componente richiedono un indicatore di oggetto vuoto `$metadata` all'interno del valore.

Un'operazione di aggiunta o sostituzione di un componente consente di impostare i valori desiderati per tutte le proprietà specificate. Cancella inoltre i valori desiderati per le proprietà scrivibili non fornite con l'aggiornamento.

La rimozione di un componente Cancella i valori desiderati di tutte le proprietà scrivibili presenti. Un dispositivo Sincronizza infine questa rimozione e interrompe la segnalazione delle singole proprietà. Il componente viene quindi rimosso dal dispositivo gemello digitale.

Nell'esempio di patch JSON seguente viene illustrato come aggiungere, sostituire o rimuovere un componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Aggiungere, sostituire o rimuovere una proprietà

Un'operazione di aggiunta o sostituzione consente di impostare il valore desiderato di una proprietà. Il dispositivo può sincronizzare lo stato e segnalare un aggiornamento del valore insieme a un `ack` codice, una versione e una descrizione.

La rimozione di una proprietà cancella il valore desiderato della proprietà, se impostata. Il dispositivo può quindi interrompere la segnalazione di questa proprietà e viene rimossa dal componente. Se questa proprietà è l'ultima nel componente, anche il componente verrà rimosso.

Nell'esempio di patch JSON seguente viene illustrato come aggiungere, sostituire o rimuovere una proprietà in un componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regole per l'impostazione del valore desiderato di una proprietà di un dispositivo gemello digitale

**Nome**

Il nome di un componente o di una proprietà deve essere un nome DTDL V2 valido.

I caratteri consentiti sono a-z, A-Z, 0-9 (non come primo carattere) e il carattere di sottolineatura (non come primo o ultimo carattere).

Un nome può avere una lunghezza di 1-64 caratteri.

**Valore proprietà**

Il valore deve essere una [Proprietà DTDL V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)valida.

Sono supportati tutti i tipi primitivi. All'interno di tipi complessi, enumerazioni, mappe e oggetti sono supportati. Per altre informazioni, vedere [schemi DTDL V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Le proprietà non supportano la matrice o uno schema complesso con una matrice.

Per un oggetto complesso è supportata una profondità massima di cinque livelli.

Tutti i nomi dei campi all'interno di un oggetto complesso devono essere nomi DTDL V2 validi.

Tutte le chiavi della mappa devono essere nomi DTDL V2 validi.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Risolvere gli errori di aggiornamento dell'API del dispositivo gemello digitale

L'API dei dispositivi gemelli digitali genera il seguente messaggio di errore generico:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Se viene visualizzato questo errore, assicurarsi che la patch di aggiornamento segua le [regole per l'impostazione del valore desiderato di una proprietà di un dispositivo gemello digitale](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Quando si aggiorna un componente, verificare che sia impostato l' [oggetto vuoto $Metadata marcatore](#add-replace-or-remove-a-component) .

Gli aggiornamenti possono avere esito negativo se i valori segnalati di un dispositivo non sono conformi alle [convenzioni plug and Play](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese informazioni sui dispositivi gemelli digitali, di seguito sono riportate alcune risorse aggiuntive:

- [Interagire con un dispositivo dalla soluzione](quickstart-service-node.md)
- [API REST Digital Twin](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)