<properties
	pageTitle="Query gemelle di Gestione dei dispositivi dell'hub IoT | Microsoft Azure"
	description="Esercitazione relativa alla funzionalità Gestione dei dispositivi dell'hub IoT di Azure che descrive come eseguire query per trovare dispositivi gemelli."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Esercitazione: Come trovare dispositivi gemelli tramite query con C# (anteprima)

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

Per avviare l'esempio, è necessario eseguire il processo **Query.exe**. In questo modo verranno eseguite alcune query diverse. Per avviare l'esempio, seguire questa procedura:

1.  Lasciare i dispositivi simulati in esecuzione per almeno 1 minuto. Questo assicura che le proprietà del dispositivo gemello siano sincronizzate con il dispositivo fisico. Per altre informazioni sulla sincronizzazione, vedere [Esercitazione: Come usare il dispositivo gemello][lnk-twin-tutorial].

2.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla cartella **azure-iot-sdks\\csharp\\service\\samples\\bin**.

3.  Eseguire `Query.exe <IoT Hub Connection String>`

L'output nella finestra della riga di comando mostra il risultato delle query per gli oggetti dispositivo che usano tag, le proprietà del servizio e le proprietà del dispositivo.

## Struttura di query (JSON)

Le query sulla proprietà del dispositivo e del servizio vengono eseguite con una stringa JSON per rappresentare la query stessa. La stringa JSON è composta da 4 parti. Di seguito è fornita una spiegazione di ogni parte e l'oggetto C# che può essere serializzato nella stringa JSON corretta.

- **Project**: espressione che designa i campi dall'oggetto dispositivo da includere nel set di risultati della query (equivalente a SELECT in SQL):

  ```
	  var query = JsonConvert.SerializeObject(
		  new
		  {
			  project = new
			  {
				  all = false,
				  properties = new[]
				  {
					  new
					  {
					  name = "CustomerId",
					  type = "service"
					  },
					  new
					  {
					  name = "Weight",
					  type = "service"
					  }
				  }
			  }
		  }
	  );
	```

- **Filter**: espressione che limita gli oggetti dispositivo inclusi nel set di risultati della query (equivalente a WHERE in SQL):

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```

- **Aggregate**: espressione che determina come raggruppare il set di risultati della query (equivalente a GROUPBY in SQL):

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```

- **Sort**: espressione che definisce quale proprietà deve essere usata per ordinare il set di risultati della query (equivalente a ORDER BY in SQL). Se l'ordinamento non è specificato, per impostazione predefinita viene usato **deviceID**:

  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### Limitazioni

Esistono alcune limitazioni nell'implementazione di query in anteprima pubblica.

-   Non esiste una convalida sull'espressione di query.

-   Le query fanno distinzione tra maiuscole e minuscole.

-   Vengono restituiti solo 100 dispositivi quando si usano le espressioni di query per eseguire query in base alle proprietà del dispositivo o del servizio. Un esempio di come implementare il paging è disponibile nella [libreria di query][lnk-query-samples].

Per informazioni più dettagliate sulla sintassi e sui campi disponibili per JSON, vedere [questo articolo][lnk-query-expression-guide]. Query di esempio sono disponibili anche nella [libreria di espressioni di query][lnk-query-samples].

### Eseguire query in base alle proprietà del dispositivo e del servizio

Dopo aver creato l'espressione di query JSON, è possibile eseguire query relative ai dispositivi gemelli. Chiamare **QueryDeviceJsonAsync** e verificare il campo **AggregateResult** per le query di aggregazione e il campo **Result** per tutte le altre query. **Result** contiene un elenco degli oggetti dispositivo, che rappresentano i dispositivi gemelli corrispondenti alla query. **AggregateResult** contiene una matrice di dizionari, ognuno contenente la riga risultante.

Queste query vengono usate in **Program.cs** del progetto **Query**.

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### Eseguire query in base ai tag

L'esecuzione di query in base ai tag consente di trovare gli oggetti dispositivo senza usare un'espressione di query JSON. Se viene passato più di un tag, verranno restituiti solo gli oggetti dispositivo che contengono tutti i tag. Il secondo parametro è **maxCount**, il numero massimo di dispositivi da restituire. Il valore massimo per **maxCount** è 1000.

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
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

<!---HONumber=AcomDC_0518_2016-->