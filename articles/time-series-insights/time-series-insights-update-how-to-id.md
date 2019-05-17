---
title: Procedure consigliate per scegliere un ID serie temporale in Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle procedure consigliate per scegliere un ID serie temporale in Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 4b2f538831ee9410eaf1a2d272f01fd30a9236e6
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519444"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Procedure consigliate per la scelta di un ID serie temporale

Questo articolo illustra la chiave di partizione di Anteprima di Azure Time Series Insights, ovvero l'ID serie temporale, e le procedure consigliate per sceglierne uno.

## <a name="choose-a-time-series-id"></a>Scegliere un ID serie temporale

Scegliere un ID serie temporale è come scegliere una chiave di partizione per un database. È una decisione importare da prendere in fase di progettazione. Non è possibile aggiornare un ambiente Anteprima di Time Series Insights esistente per usare un ID serie temporale differente. In altre parole, quando viene creato un ambiente con un ID serie temporale, il criterio è una proprietà immutabile che non può essere modificata.

> [!IMPORTANT]
> L'ID serie temporale fa distinzione tra maiuscole e minuscole ed è immutabile, ovvero non può essere modificato dopo essere stato impostato.

Tenendo questo a mente, la selezione dell'ID serie temporale appropriato è un fattore di importanza critica. Quando si seleziona un ID serie temporale, tenere in considerazione le procedure consigliate seguenti:

* Scegliere un nome proprietà che contenga un'ampia gamma di valori e abbia anche modelli di accesso. È consigliabile avere una chiave di partizione con molti valori distinti (ad esempio centinaia o migliaia). Per molti clienti, questa chiave sarà simile al valore DeviceID o SensorID in JSON.
* L'ID serie temporale deve essere univoco a livello di nodo foglia del [modello serie temporale](./time-series-insights-update-tsm.md).
* Una stringa di caratteri del nome della proprietà ID serie temporale può contenere fino a 128 caratteri e i valori della proprietà ID serie temporale possono contenere fino a 1024 caratteri.
* Se non sono definiti alcuni valori della proprietà ID serie temporale univoci, questi vengono considerati come valori Null facenti parte del vincolo di univocità.

È anche possibile selezionare fino a *tre* (3) proprietà chiave come ID serie temporale.

  > [!NOTE]
  > Le *tre* (3) proprietà chiave devono essere stringhe.

Gli scenari seguenti descrivono la selezione di più di una proprietà chiave come ID serie temporale:  

### <a name="scenario-one"></a>Scenario 1

* Sono presenti flotte di asset legacy, ognuna con una chiave univoca.
* Ad esempio, una flotta viene identificata in modo univoco dalla proprietà *deviceId* e un'altra dalla proprietà univoca *objectId*. Nessuna delle due flotte contiene la proprietà univoca dell'altra flotta. In questo esempio si selezionerebbero due chiavi, deviceId e objectId, come chiavi univoche.
* I valori Null vengono accettati e la mancanza di una proprietà nel payload dell'evento viene è considerata come valore `null`. Questo è anche il modo appropriato di gestire l'invio di dati a due diverse origini evento dove i dati in ogni origine evento hanno un ID serie temporale univoco.

### <a name="scenario-two"></a>Scenario 2

* È necessario che più proprietà siano univoche nella stessa flotta di asset. 
* Si pensi, ad esempio, a un costruttore di edifici intelligenti che installa sensori in ogni stanza. In ogni stanza si hanno generalmente gli stessi valori per *sensorId*, ad esempio *sensor1*, *sensor2* e *sensor3*.
* L'edificio ha inoltre numeri di piano e di stanza che si sovrappongono nei siti della proprietà *flrRm*, con valori come *1a*, *2b*, *3a* e così via.
* È infine presente una proprietà, *location*, contenente valori come *Redmond*, *Barcelona* e *Tokyo*. Per creare univocità, è necessario designare le tre proprietà seguenti come chiavi ID serie temporale: *sensorId*, *flrRm* e *location*.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [modellazione di dati](./time-series-insights-update-tsm.md).

* Pianificare l'[ambiente Azure Time Series Insights (anteprima)](./time-series-insights-update-plan.md).