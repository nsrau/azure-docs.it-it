---
title: Come usare le proprietà in una soluzione IoT Central di Azure
description: Come usare le proprietà di sola lettura e scrivibili nella soluzione IoT Central di Azure
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344298"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Come usare le proprietà in una soluzione IoT Central di Azure

Questo articolo illustra come usare le proprietà del dispositivo definite in un modello di dispositivo nell'applicazione IoT Central di Azure.

Le proprietà rappresentano valori temporizzati. Ad esempio, un dispositivo potrebbe usare una proprietà per segnalare la temperatura di destinazione che sta tentando di raggiungere. Le proprietà consentono inoltre di sincronizzare lo stato tra il dispositivo e l'applicazione IoT Central.  È possibile impostare le proprietà scrivibili da IoT Central.

È anche possibile definire le proprietà del cloud in un'applicazione IoT Central. I valori delle proprietà cloud non vengono mai scambiati con un dispositivo e non rientrano nell'ambito di questo articolo.

## <a name="define-your-properties"></a>Definire le proprietà

Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Usare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato di spegnimento di un dispositivo. Le proprietà possono anche rappresentare le proprietà di base dei dispositivi, ad esempio la versione software del dispositivo. È possibile dichiarare le proprietà in sola lettura o in scrittura.

Lo screenshot seguente mostra una definizione di proprietà nell'applicazione IoT Central di Azure

![Definisci proprietà](./media/howto-use-properties/property-definition.png)

La tabella seguente illustra le impostazioni di configurazione per una funzionalità della proprietà:

| Campo           | Descrizione                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome visualizzato    | Nome visualizzato per il valore della proprietà usato nei dashboard e nei moduli.                                                                                                                                                              |
| Nome            | Nome della proprietà. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. Questo campo deve essere alfanumerico.                                                 |
| Tipo di funzionalità | Proprietà.                                                                                                                                                                                                                          |
| Tipo semantico   | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili.                                                                       |
| SCHEMA          | Tipo di dati della proprietà, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento.                                               |
| Scrivibile       | Se la proprietà non è scrivibile, il dispositivo può segnalare i valori della proprietà a IoT Central. Se la proprietà è scrivibile, il dispositivo può segnalare i valori della proprietà a IoT Central e IoT Central può inviare gli aggiornamenti sulla proprietà al dispositivo. |
| Gravità        | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**.                                                                                                                         |
| Valori di stato    | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore.                                                                                   |
| Unità            | Unità per il valore della proprietà, ad esempio **mph**, **%** o ** &deg; C**.                                                                                                                                                              |
| Unità di visualizzazione    | Unità di visualizzazione da usare nei dashboard e nei moduli.                                                                                                                                                                                    |
| Comment         | Eventuali commenti sulla funzionalità della proprietà.                                                                                                                                                                                        |
| Descrizione     | Descrizione della funzionalità della proprietà.                                                                                                                                                                                          |

Le proprietà possono essere definite anche in un'interfaccia in un modello di dispositivo come riportato di seguito:

``` json
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
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Questo esempio mostra cinque proprietà, che possono essere correlate alla definizione di proprietà nell'interfaccia utente come riportato di seguito:

* `@type` per specificare il tipo di funzionalità: `Property`
* `name` per il valore della proprietà.
* `schema` consente di specificare il tipo di dati per la proprietà. Questo valore può essere un tipo primitivo, ad esempio Double, Integer, Boolean o String. Sono supportati anche tipi di oggetti complessi, matrici e mappe.
* `writable` Per impostazione predefinita, le proprietà sono di sola lettura. È possibile contrassegnare una proprietà come scrivibile, usando questo campo

I campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

Quando si crea una proprietà, è possibile specificare i tipi di **schema** complessi, ad esempio oggetto, enumerazione e così via.

![Aggiungere una funzionalità](./media/howto-use-properties/property.png)

Quando si seleziona lo schema complesso, ad esempio **oggetto**, è necessario definire anche l'oggetto.

![Definisci oggetto](./media/howto-use-properties/object.png)

Nel codice seguente viene illustrata la definizione di un tipo di proprietà dell'oggetto. Questo oggetto dispone di due campi con tipi stringa e Integer:

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementare proprietà di sola lettura

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura indicano che il dispositivo segnala gli aggiornamenti del valore della proprietà all'applicazione IoT Central. L'applicazione IoT Central non può impostare il valore di una proprietà di sola lettura.

IoT Central usa dispositivi gemelli per sincronizzare i valori delle proprietà tra il dispositivo e l'applicazione IoT Central. I valori delle proprietà del dispositivo usano le proprietà segnalate del dispositivo gemello. Per altre informazioni, vedere [dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

Il frammento di codice seguente da un modello di funzionalità del dispositivo Mostra la definizione di un tipo di proprietà di sola lettura:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Le proprietà di sola lettura vengono inviate dal dispositivo a IoT Central. Le proprietà vengono inviate come payload JSON. per altre informazioni, vedere [payloads](./concepts-telemetry-properties-commands.md).

È possibile usare l'SDK per dispositivi Azure per inviare un aggiornamento della proprietà all'applicazione IoT Central.

Le proprietà dei dispositivi gemelli possono essere inviate all'applicazione IoT Central di Azure usando la funzione seguente:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Questo articolo usa Node.js per semplicità. per informazioni complete sugli esempi di applicazioni per dispositivi, vedere l'articolo [creare e connettere un'applicazione client all'applicazione azure IOT Central (Node.js)](tutorial-connect-device-nodejs.md) e [creare e connettere un'applicazione client per le esercitazioni di Azure IOT Central Application (Python)](tutorial-connect-device-python.md) .

La visualizzazione seguente nell'applicazione IoT Central di Azure Mostra le proprietà. è possibile vedere che la visualizzazione rende automaticamente la proprietà del modello di dispositivo una _proprietà di sola lettura del dispositivo_.

![Visualizzazione della proprietà di sola lettura](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementare proprietà scrivibili

Le proprietà scrivibili vengono impostate da un operatore nell'applicazione IoT Central in un modulo. IoT Central Invia la proprietà al dispositivo. IoT Central prevede un riconoscimento dal dispositivo. 

Il frammento di codice seguente da un modello di funzionalità del dispositivo Mostra la definizione di un tipo di proprietà scrivibile:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

``` json
{ "Brightness Level": 2 }
```

Per definire e gestire le proprietà scrivibili a cui risponde il dispositivo, è possibile usare il codice seguente.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Il messaggio di risposta deve includere `ac` i `av` campi e. Il campo `ad` è facoltativo. Per esempi, vedere i frammenti di codice seguenti.

* `ac` è un campo numerico che usa i valori riportati nella tabella seguente:

* `av` numero di versione inviato al dispositivo.

* `ad` Descrizione della stringa di opzioni.

| Valore | Etichetta | Descrizione |
| ----- | ----- | ----------- |
| `'ac': 200` | Completi | L'operazione di modifica della proprietà è stata completata correttamente. |
| `'ac': 202`  o `'ac': 201` | Pending | L'operazione di modifica della proprietà è in sospeso o in corso |
| `'ac': 4xx` | Errore | La modifica della proprietà richiesta non è valida o si è verificato un errore |
| `'ac': 5xx` | Errore | Si è verificato un errore imprevisto nel dispositivo durante l'elaborazione della modifica richiesta. |


Per altre informazioni, vedere [dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Quando l'operatore imposta una proprietà scrivibile nell'applicazione IoT Central, l'applicazione usa una proprietà desiderata del dispositivo gemello per inviare il valore al dispositivo. Il dispositivo risponde quindi usando una proprietà segnalata dal dispositivo gemello. Quando IoT Central riceve il valore della proprietà segnalata, aggiorna la visualizzazione delle proprietà con lo stato **accettato**.

Nella visualizzazione seguente sono illustrate le proprietà scrivibili. Quando si immette il valore e si **Salva**, lo stato iniziale è **in sospeso**, quando il dispositivo accetta la modifica, lo stato diventa **accettato**.

![Stato in sospeso](./media/howto-use-properties/status-pending.png)

![Proprietà accettata](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare le proprietà nell'applicazione IoT Central di Azure, è possibile visualizzare i [payload](concepts-telemetry-properties-commands.md) e [creare e connettere un'applicazione client all'applicazione azure IOT Central (Node.js)](tutorial-connect-device-nodejs.md).
