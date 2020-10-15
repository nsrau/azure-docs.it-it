---
title: Che cosa sono i modelli di dispositivo in Azure IoT Central | Microsoft Docs
description: I modelli di dispositivo IoT Central di Azure consentono di specificare il comportamento dei dispositivi connessi all'applicazione. Un modello di dispositivo specifica i dati di telemetria, le proprietà e i comandi che devono essere implementati dal dispositivo. Un modello di dispositivo definisce anche l'interfaccia utente per il dispositivo in IoT Central, ad esempio i moduli e i dashboard utilizzati da un operatore.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91813169"
---
# <a name="what-are-device-templates"></a>Che cosa sono i modelli di dispositivo?

_Questo articolo si applica agli sviluppatori di dispositivi e ai generatori di soluzioni._

Un modello di dispositivo in IoT Central di Azure è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette all'applicazione. Ad esempio, il modello di dispositivo definisce i dati di telemetria inviati da un dispositivo in modo che IoT Central possibile creare visualizzazioni che usano le unità e i tipi di dati corretti.

Un generatore di soluzioni aggiunge modelli di dispositivo a un'applicazione IoT Central. Uno sviluppatore di dispositivi scrive il codice del dispositivo che implementa i comportamenti definiti nel modello di dispositivo.

Un modello di dispositivo include le sezioni seguenti:

- _Un modello di funzionalità del dispositivo (DCM)_. Questa parte del modello di dispositivo definisce il modo in cui il dispositivo interagisce con l'applicazione. Uno sviluppatore di dispositivi implementa i comportamenti definiti in DCM.
    - _Interfacce_. Un DCM contiene una o più interfacce che definiscono i dati di telemetria, le proprietà e i comandi che devono essere implementati dal dispositivo.
- _Proprietà del cloud_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di specificare i metadati del dispositivo da archiviare. Le proprietà del cloud non vengono mai sincronizzate con i dispositivi e esistono solo nell'applicazione. Le proprietà del cloud non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il DCM.
- _Personalizzazioni_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di eseguire l'override di alcune delle definizioni in DCM. Le personalizzazioni sono utili se lo sviluppatore della soluzione vuole perfezionare il modo in cui l'applicazione gestisce un valore, ad esempio modificando il nome visualizzato per una proprietà o il colore usato per visualizzare un valore di telemetria. Le personalizzazioni non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il DCM.
- _Viste_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di definire le visualizzazioni per visualizzare i dati dal dispositivo e i moduli per gestire e controllare un dispositivo. Le visualizzazioni utilizzano le proprietà DCM, cloud e customizations. Le visualizzazioni non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il DCM.

## <a name="device-capability-models"></a>Modelli di funzionalità del dispositivo

Un DCM definisce il modo in cui un dispositivo interagisce con l'applicazione IoT Central. Lo sviluppatore del dispositivo deve verificare che il dispositivo implementi i comportamenti definiti in DCM, in modo che IoT Central possibile monitorare e gestire il dispositivo. Un DCM è costituito da una o più _interfacce_e ogni interfaccia può definire una raccolta di tipi di dati di _telemetria_ , _proprietà del dispositivo_e _comandi_. Uno sviluppatore di soluzioni può importare un file JSON che definisce il DCM in un modello di dispositivo o usare l'interfaccia utente Web in IoT Central per creare o modificare un DCM. Le modifiche a un DCM eseguito tramite l'interfaccia utente Web richiedono il [controllo delle versioni del modello di dispositivo](./howto-version-device-template.md).

Uno sviluppatore di soluzioni può anche esportare un file JSON che contiene il DCM. Uno sviluppatore di dispositivi può usare questo documento JSON per comprendere il modo in cui il dispositivo deve comunicare con l'applicazione IoT Central.

Il file JSON che definisce il DCM usa il [linguaggio DTDL (Digital Twin Definition Language) V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central prevede che il file JSON contenga il DCM con le interfacce definite inline, anziché in file distinti.

Un dispositivo molto comune è costituito da:

- Parti personalizzate, ovvero le operazioni che rendono univoco il dispositivo.
- Parti standard, ovvero cose comuni a tutti i dispositivi.

Queste parti sono denominate _interfacce_ in DCM. Le interfacce definiscono i dettagli di ogni parte implementata dal dispositivo. Le interfacce sono riutilizzabili in DCMs.

Nell'esempio seguente viene illustrata la struttura del modello di funzionalità del dispositivo per un dispositivo sensore ambientale con due interfacce:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Un modello di funzionalità include alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un semplice nome di risorsa uniforme.
- `@type`: dichiara che questo oggetto è un modello di funzionalità.
- `@context`: specifica la versione di DTDL usata per il modello di funzionalità.
- `implements`: elenca le interfacce implementate dal dispositivo.

Ogni voce dell'elenco di interfacce nella sezione Implements include:

- `name`: nome di programmazione dell'interfaccia.
- `schema`: interfaccia implementata dal modello di funzionalità.

In un'interfaccia sono presenti alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un semplice nome di risorsa uniforme.
- `@type`: dichiara che questo oggetto è un'interfaccia.
- `@context`: specifica la versione di DTDL usata per l'interfaccia.
- `contents`: elenca le proprietà, la telemetria e i comandi che compongono il dispositivo.

Sono disponibili alcuni campi facoltativi che è possibile usare per aggiungere altri dettagli al modello di funzionalità, ad esempio il nome visualizzato e la descrizione.

## <a name="interfaces"></a>Interfacce

DTDL consente di descrivere le funzionalità del dispositivo. Le funzionalità correlate sono raggruppate in interfacce. Le interfacce descrivono le proprietà, la telemetria e i comandi che una parte del dispositivo implementa:

- `Properties`. Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Usare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato di spegnimento di un pompa di raffreddamento. Le proprietà possono anche rappresentare le proprietà di base dei dispositivi, ad esempio la versione del firmware del dispositivo. È possibile dichiarare le proprietà in sola lettura o in scrittura. Solo i dispositivi possono aggiornare il valore di una proprietà di sola lettura. Un operatore può impostare il valore di una proprietà scrivibile da inviare a un dispositivo.
- `Telemetry`. I campi di telemetria rappresentano le misurazioni dai sensori. Ogni volta che il dispositivo acquisisce una misura del sensore, deve inviare un evento di telemetria contenente i dati del sensore.
- `Commands`. I comandi rappresentano i metodi che gli utenti del dispositivo possono eseguire sul dispositivo. Ad esempio, un comando reset o un comando per attivare o disattivare un ventilatore.

Nell'esempio seguente viene illustrata la definizione dell'interfaccia del sensore ambientale:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Questo esempio mostra due proprietà (una sola lettura e una scrivibile), un tipo di telemetria e due comandi. Una descrizione del campo minima presenta:

- `@type` per specificare il tipo di funzionalità: `Telemetry` , `Property` o `Command` .  In alcuni casi, il tipo include un tipo semantico per consentire IoT Central di fare alcune ipotesi sulla gestione del valore.
- `name` per il valore di telemetria.
- `schema` per specificare il tipo di dati per la telemetria o la proprietà. Questo valore può essere un tipo primitivo, ad esempio Double, Integer, Boolean o String. Sono supportati anche tipi di oggetti complessi, matrici e mappe.
- `commandType` per specificare come gestire il comando.

I campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

## <a name="properties"></a>Proprietà

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura indicano che il dispositivo segnala gli aggiornamenti del valore della proprietà all'applicazione IoT Central. L'applicazione IoT Central non può impostare il valore di una proprietà di sola lettura.

È anche possibile contrassegnare una proprietà come scrivibile in un'interfaccia. Un dispositivo può ricevere un aggiornamento a una proprietà scrivibile dall'applicazione IoT Central, nonché segnalare gli aggiornamenti del valore della proprietà per l'applicazione.

Non è necessario che i dispositivi siano connessi per impostare i valori delle proprietà. I valori aggiornati vengono trasferiti alla successiva connessione del dispositivo all'applicazione. Questo comportamento si applica sia alle proprietà di sola lettura che a quelle scrivibili.

Non usare le proprietà per inviare dati di telemetria dal dispositivo. Una proprietà di sola lettura, ad esempio, `temperatureSetting=80` deve indicare che la temperatura del dispositivo è stata impostata su 80 e che il dispositivo sta provando a raggiungere o rimanere in corrispondenza della temperatura.

Per le proprietà scrivibili, l'applicazione del dispositivo restituisce un codice di stato desiderato, una versione e una descrizione per indicare se ha ricevuto e applicato il valore della proprietà.

## <a name="telemetry"></a>Telemetria

IoT Central consente di visualizzare i dati di telemetria nei dashboard e nei grafici e di usare le regole per attivare le azioni quando vengono raggiunte le soglie. IoT Central USA le informazioni contenute in DCM, ad esempio tipi di dati, unità e nomi visualizzati, per determinare la modalità di visualizzazione dei valori di telemetria.

È possibile usare la funzionalità di esportazione dei dati IoT Central per trasmettere i dati di telemetria ad altre destinazioni, ad esempio archiviazione o hub eventi.

## <a name="commands"></a>Comandi

I comandi sono sincroni o asincroni. Per impostazione predefinita, un comando sincrono deve essere eseguito entro 30 secondi e il dispositivo deve essere connesso all'arrivo del comando. Se il dispositivo risponde nel tempo o il dispositivo non è connesso, il comando ha esito negativo.

Usare i comandi asincroni per le operazioni a esecuzione prolungata. Il dispositivo invia le informazioni di stato usando i messaggi di telemetria. Questi messaggi di stato hanno le proprietà di intestazione seguenti:

- `iothub-command-name`: nome del comando, ad esempio `UpdateFirmware` .
- `iothub-command-request-id`: ID richiesta generato sul lato server e inviato al dispositivo nella chiamata iniziale.
- `iothub-interface-id`: ID dell'interfaccia su cui è definito questo comando, ad esempio `urn:example:AssetTracker:1` .
 `iothub-interface-name`: nome dell'istanza di questa interfaccia, ad esempio `myAssetTracker` .
- `iothub-command-statuscode`: codice di stato restituito dal dispositivo, ad esempio `202` .

## <a name="cloud-properties"></a>Proprietà cloud

Le proprietà del cloud fanno parte del modello di dispositivo, ma non fanno parte di DCM. Le proprietà del cloud consentono allo sviluppatore di soluzioni di specificare i metadati del dispositivo da archiviare nell'applicazione IoT Central. Le proprietà del cloud non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il DCM.

Uno sviluppatore di soluzioni può aggiungere proprietà del cloud a dashboard e visualizzazioni insieme alle proprietà del dispositivo per consentire a un operatore di gestire i dispositivi connessi all'applicazione. Uno sviluppatore di soluzioni può anche usare le proprietà del cloud come parte di una definizione della regola per rendere un valore di soglia modificabile da un operatore.

## <a name="customizations"></a>Personalizzazioni

Le personalizzazioni fanno parte del modello di dispositivo, ma non fanno parte di DCM. Le personalizzazioni consentono allo sviluppatore della soluzione di migliorare o eseguire l'override di alcune delle definizioni in DCM. Uno sviluppatore di soluzioni può, ad esempio, modificare il nome visualizzato per una proprietà o un tipo di telemetria. Uno sviluppatore di soluzioni può inoltre utilizzare le personalizzazioni per aggiungere la convalida, ad esempio la lunghezza minima o massima per una proprietà del dispositivo stringa.

Le personalizzazioni possono influenzare il codice scritto da uno sviluppatore di dispositivi per implementare il DCM. Ad esempio, una personalizzazione potrebbe impostare lunghezze di stringa minime e massime o valori numerici minimi e massimi per la telemetria.

## <a name="views"></a>Viste

Uno sviluppatore di soluzioni crea visualizzazioni che consentono agli operatori di monitorare e gestire i dispositivi connessi. Le visualizzazioni fanno parte del modello di dispositivo, quindi una vista è associata a un tipo di dispositivo specifico. Una vista può includere:

- Grafici per tracciare i dati di telemetria.
- Riquadri per visualizzare le proprietà del dispositivo di sola lettura.
- Riquadri per consentire all'operatore di modificare le proprietà dei dispositivi scrivibili.
- Riquadri per consentire all'operatore di modificare le proprietà del cloud.
- Riquadri per consentire all'operatore di chiamare comandi, inclusi i comandi che prevedono un payload.
- Riquadri per visualizzare etichette, immagini o testo Markdown.

I dati di telemetria, le proprietà e i comandi che è possibile aggiungere a una vista sono determinati dalle proprietà DCM, cloud e customizations nel modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

In qualità di sviluppatore di dispositivi, ora che sono stati appresi i modelli dei dispositivi, i passaggi successivi suggeriti sono la lettura di dati di [telemetria, proprietà e payload di comandi](./concepts-telemetry-properties-commands.md) per ottenere altre informazioni sui dati scambiati con IOT Central.

In qualità di sviluppatore di soluzioni, un passaggio successivo suggerito consiste [nel leggere definire un nuovo tipo di dispositivo Internet all'interno dell'applicazione IoT Central di Azure](./howto-set-up-template.md) per altre informazioni su come creare un modello di dispositivo.
