---
title: Procedure consigliate per la scelta di un ID serie temporali - Approfondimenti sulle serie temporali di Azure Documenti Microsoft
description: Informazioni sulle procedure consigliate per la scelta di un ID serie temporale in Azure Time Series Insights Preview.Learn about best practices when choosing a Time Series ID in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083521"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Procedure consigliate per la scelta di un ID serie temporale

Questo articolo riepiloga l'importanza dell'ID serie temporale per l'ambiente Azure Time Series Insights Preview e le procedure consigliate per la scelta di uno.

## <a name="choose-a-time-series-id"></a>Scegliere un ID serie temporale

La selezione di un ID Time Series appropriato è fondamentale. Scegliere un ID serie temporale è come scegliere una chiave di partizione per un database. È necessario quando si crea un ambiente Time Series Insights Preview. 

> [!IMPORTANT]
> Gli URL delle serie temporali sono:
> * Una proprietà *con distinzione tra maiuscole e minuscole:* maiuscole/minuscole di lettere e caratteri vengono utilizzate nelle ricerche, nei confronti, negli aggiornamenti e durante il partizionamento.
> * Una proprietà *non modificabile:* una volta creata non può essere modificata.

> [!TIP]
> Se l'origine eventi è un hub IoT, è probabile che l'ID Time Series sia ***iothub-connection-device-id***.

Le principali best practice da seguire includono:

* Scegliere una chiave di partizione con molti valori distinti (ad esempio, centinaia o migliaia). In molti casi, potrebbe trattarsi dell'ID dispositivo, dell'ID sensore o dell'ID tag nel codice JSON.
* L'ID serie temporale deve essere univoco a livello di nodo foglia del [modello serie temporale](./time-series-insights-update-tsm.md).
* Il limite di caratteri per la stringa del nome della proprietà dell'ID time series è 128. Per il valore della proprietà ID serie temporali, il limite di caratteri è 1.024.
* Se manca un valore di proprietà univoco per l'ID serie temporale, viene considerato come un valore null e segue la stessa regola del vincolo di unicità.
* È inoltre possibile selezionare fino a *tre* proprietà chiave come ID serie temporali. La loro combinazione sarà una chiave composita che rappresenta l'ID time series.  
  > [!NOTE]
  > Le tre proprietà chiave devono essere stringhe.
  > È necessario eseguire una query su questa chiave composita anziché su una proprietà alla volta.

## <a name="select-more-than-one-key-property"></a>Selezionare più di una proprietà chiave

Gli scenari seguenti descrivono la selezione di più proprietà chiave come ID time series.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Esempio 1: ID serie temporali con una chiave univocaExample 1: Time Series ID with a unique key

* Hai flotte di beni legacy. Ognuna ha una chiave univoca.
* Un fleet è identificato in modo univoco dalla proprietà **deviceId**. Per un altro parco, la proprietà univoca è **objectId**. Nessuna delle due flotte contiene la proprietà unica dell'altra flotta. In questo esempio, è necessario selezionare due chiavi, **deviceId** e **objectId**, come chiavi univoche.
* Accettiamo valori null e la mancanza di presenza di una proprietà nell'evento payload viene conteggiato come valore null. Questo è anche il modo appropriato per gestire l'invio di dati a due origini eventi in cui i dati in ogni origine evento hanno un ID Time Series univoco.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Esempio 2: ID serie temporale con una chiave compositaExample 2: Time Series ID with a composite key

* È necessario che più proprietà siano univoche nella stessa flotta di asset. 
* Sei un produttore di edifici intelligenti e dispari sensori in ogni stanza. In ogni stanza, in genere si dispone degli stessi valori per **sensorId**. Esempi sono **sensor1**, **sensor2**e **sensor3**.
* L'edificio ha numeri di pavimento e stanza sovrapposti tra i siti nella proprietà **flrRm**. Questi numeri hanno valori come **1a**, **2b**e **3a**.
* Si dispone di una proprietà, **location**, che contiene valori quali **Redmond**, **Barcellona**e **Tokyo**. Per creare l'univocità, è necessario designare le tre proprietà seguenti come chiavi ID Time Series: **sensorId**, **flrRm**e **location**.

Esempio di evento non elaborato:Example raw event:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Nel portale di Azure è quindi possibile immettere la chiave composita come segue:In the Azure portal, you can then enter the composite key as follows: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulla [modellazione dei dati](./time-series-insights-update-tsm.md).

* Pianificare l'ambiente Azure [Time Series Insights Preview.](./time-series-insights-update-plan.md)