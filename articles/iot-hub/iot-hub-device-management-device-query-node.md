<properties
	pageTitle="Query gemelle di Gestione dei dispositivi dell'hub IoT | Microsoft Azure"
	description="Esercitazione relativa alla funzionalità Gestione dei dispositivi dell'hub IoT di Azure che descrive come eseguire query per trovare dispositivi gemelli."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Esercitazione: Come trovare dispositivi gemelli tramite query con node.js (anteprima)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Gestione dei dispositivi IoT di Azure consente di trovare dispositivi gemelli, la rappresentazione di un dispositivo fisico in forma di servizio, con le query. È possibile eseguire query in base alle proprietà del dispositivo, alle proprietà del servizio e ai tag nel dispositivo gemello. È possibile eseguire query usando tag e proprietà:

-   Per eseguire una query che restituisca i dispositivi gemelli usando i tag, è necessario passare una matrice di stringhe. La query restituirà il set di dispositivi taggati con tutte le stringhe passate.

-   Per eseguire una query che restituisca i dispositivi gemelli usando le proprietà del servizio o le proprietà del dispositivo, è necessario usare un'espressione di query JSON.

Per altre informazioni sul dispositivo gemello e sulle query, vedere [Panoramica di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-overview].

## Esecuzione dell'esempio di query di dispositivo

L'esempio seguente estende la funzionalità dell'esercitazione [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. Una volta che i vari dispositivi simulati saranno in esecuzione, verrà usata una query per trovare dispositivi specifici.

### Prerequisiti 

Prima di eseguire questo esempio, è necessario aver completato le procedure illustrate in [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. I dispositivi simulati devono quindi essere in esecuzione. Se il processo è già stato completato in precedenza, riavviare i dispositivi simulati adesso.

### Avvio dell'esempio

Per avviare l'esempio, è necessario eseguire `registry_queryDevices.js`. In questo modo verranno eseguite alcune query diverse. Per avviare l'esempio, seguire questa procedura:

1.  Lasciare i dispositivi simulati in esecuzione per almeno 1 minuto. Questo assicura che le proprietà del dispositivo gemello siano sincronizzate con il dispositivo fisico. Per altre informazioni sulla sincronizzazione, vedere [Esercitazione: Come usare il dispositivo gemello][lnk-twin-tutorial].

2.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla cartella **azure-iot-sdks/node/service/samples**.

3.  Aprire **registry\_queryDevices.js** e sostituire il segnaposto con la stringa di connessione dell'hub IoT.

4.  Eseguire `node registry_queryDevices.js`.

L'output nella finestra della riga di comando mostra il risultato delle query per gli oggetti dispositivo che usano tag, le proprietà del servizio e le proprietà del dispositivo.

## Struttura di query (JSON)

Le query sulla proprietà del dispositivo e del servizio vengono eseguite con una stringa JSON per rappresentare la query stessa. La stringa JSON è composta da 4 parti. Di seguito è fornita una spiegazione di ogni parte e l'oggetto C# che può essere serializzato nella stringa JSON corretta.

- **Project**: espressione che designa i campi dall'oggetto dispositivo da includere nel set di risultati della query (equivalente a SELECT in SQL):

	```
	var projectQuery = {
	    "project": {
	        "all": false,
	        "properties": [{
	            "name": "CustomerId",
	            "type": "service"
	        }, {
	            "name": "Weight",
	            "type": "service"
	        }]
	    }
	}
	```

- **Filter**: espressione che limita gli oggetti dispositivo inclusi nel set di risultati della query (equivalente a WHERE in SQL):

	```
	var filterQuery = {
	  filter: {
	    property: {
	      name: "CustomerId",
	      type: "service"
	    },
	    value: "123456",
	    comparisonOperator: "eq",
	    type: "comparison"
	  },
	  project: null,
	  aggregate: null,
	  sort: null
	};
	```

- **Aggregate**: espressione che determina come raggruppare il set di risultati della query (equivalente a GROUPBY in SQL):

	```
	var aggregateQuery = {
	filter: null,
	project: null,
	aggregate: {
	keys: [
	  {
	    name: "CustomerId",
	    type: "service"
	  }
	],
	properties: [
	  {
	    operator: "sum",
	    property: {
	      name: "Weight",
	      type: "service"
	    },
	    columnName: "TotalWeight"
	  }
	]
	},
	sort: null
	};
	```

- **Sort**: espressione che definisce quale proprietà deve essere usata per ordinare il set di risultati della query (equivalente a ORDER BY in SQL). Se l'ordinamento non è specificato, per impostazione predefinita viene usato **deviceID**:

```
  var sortQuery = {
  filter: null,
  project: null,
  aggregate: null,
  sort: [
      {
          order: "asc",
          property: {
              name: "QoS",
              type: "service"
          }
      }
  ]
  };
```

### Limitazioni

Esistono alcune limitazioni nell'implementazione di query in anteprima pubblica.

-   Non esiste una convalida sull'espressione di query.

-   Le query fanno distinzione tra maiuscole e minuscole.

-   Vengono restituiti solo 100 dispositivi quando si usano le espressioni di query per eseguire query in base alle proprietà del dispositivo o del servizio. Un esempio di come implementare il paging è disponibile nella [libreria di query][lnk-query-samples].

Per informazioni più dettagliate sulla sintassi e sui campi disponibili per JSON, vedere [questo articolo][lnk-query-expression-guide]. Query di esempio sono disponibili anche nella [libreria di espressioni di query][lnk-query-samples].

### Eseguire query in base alle proprietà del dispositivo e del servizio

Dopo aver creato l'espressione di query JSON, è possibile eseguire query relative ai dispositivi gemelli. Chiamare **queryDevices** e controllare l'oggetto **result** per le query di aggregazione e l'oggetto **devices** per tutte le altre query.

```
registry.queryDevices(query, done)
```

### Eseguire query in base ai tag

L'esecuzione di query in base ai tag consente di trovare gli oggetti dispositivo senza usare un'espressione di query JSON. Se viene passato più di un tag, verranno restituiti solo gli oggetti dispositivo che contengono tutti i tag. Il secondo parametro è **maxCount**, il numero massimo di dispositivi da restituire. Il valore massimo per **maxCount** è 1000.

```
registry.queryDevicesByTags(['bacon'], 100, done)
```

### Implementazione nel dispositivo

La query è abilitata dalla libreria client di Gestione dei dispositivi dell'hub IoT di Azure. A condizione che le proprietà del dispositivo siano sincronizzate (come descritto in [Esercitazione: Come usare il dispositivo gemello][lnk-twin-tutorial]), è possibile eseguire query su di esse. Le proprietà del dispositivo sono disponibili solo dopo che il dispositivo fisico si è connesso all'hub IoT e ha fornito i valori iniziali.

## Passaggi successivi

Per altre informazioni sulle funzionalità di Gestione dei dispositivi dell'hub IoT di Azure, vedere le esercitazioni seguenti:

- [Come usare il dispositivo gemello][lnk-twin-tutorial]
- [Come usare processi del dispositivo per aggiornare il firmware del dispositivo][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0622_2016-->