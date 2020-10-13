---
title: Procedure consigliate per la scelta di un ID di serie temporali-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle procedure consigliate per la scelta di un ID di serie temporale in Azure Time Series Insights Gen2.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: fb409673e028375812551ec146b43c27e3755d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595522"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Procedure consigliate per la scelta di un ID serie temporale

Questo articolo riepiloga l'importanza dell'ID della serie temporale per l'ambiente di Azure Time Series Insights Gen2 e le procedure consigliate per sceglierne una.

## <a name="choose-a-time-series-id"></a>Scegliere un ID serie temporale

La selezione di un ID Time Series appropriato è fondamentale. Scegliere un ID serie temporale è come scegliere una chiave di partizione per un database. È obbligatorio quando si crea un ambiente di Azure Time Series Insights Gen2.

> [!IMPORTANT]
> Gli ID delle serie temporali sono:
>
> * Una proprietà con *distinzione tra maiuscole* e minuscole: le lettere e i caratteri vengono usati in ricerche, confronti, aggiornamenti e durante il partizionamento.
> * Una proprietà non *modificabile* : una volta creata, non può essere modificata.

> [!TIP]
> Se l'origine evento è un hub Internet delle cose, l'ID della serie temporale sarà probabilmente ***iothub-Connection-Device-ID***.

Di seguito sono riportate le principali procedure consigliate:

* Selezionare una chiave di partizione con molti valori distinti (ad esempio, centinaia o migliaia). In molti casi, potrebbe trattarsi dell'ID del dispositivo, dell'ID del sensore o dell'ID tag in JSON.
* L'ID serie temporale deve essere univoco a livello di nodo foglia del [modello serie temporale](./concepts-model-overview.md).
* Il limite di caratteri per la stringa del nome di proprietà dell'ID della serie temporale è 128. Per il valore della proprietà dell'ID della serie temporale, il limite di caratteri è 1.024.
* Se manca un valore di proprietà univoco per l'ID della serie temporale, questo viene considerato come un valore null e segue la stessa regola del vincolo di univocità.
* Se l'ID della serie temporale è annidato all'interno di un oggetto JSON complesso, assicurarsi di seguire le [regole di Flat](./concepts-json-flattening-escaping-rules.md) in ingresso quando si specifica il nome della proprietà. Vedere l'esempio [B](concepts-json-flattening-escaping-rules.md#example-b).
* È anche possibile selezionare fino a *tre* proprietà chiave come ID della serie temporale. La combinazione sarà una chiave composta che rappresenta l'ID della serie temporale.  
  > [!NOTE]
  > Le tre proprietà chiave devono essere stringhe.
  > È necessario eseguire una query su questa chiave composta anziché su una proprietà alla volta.

## <a name="select-more-than-one-key-property"></a>Selezionare più di una proprietà chiave

Negli scenari seguenti viene descritto come selezionare più di una proprietà chiave come ID della serie temporale.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Esempio 1: ID Time Series con una chiave univoca

* Sono presenti flotte legacy di asset. Ogni ha una chiave univoca.
* Una flotta viene identificata in modo univoco dalla proprietà **DeviceID**. Per un'altra flotta, la proprietà Unique è **ObjectID**. Nessuna delle due flotte contiene la proprietà univoca dell'altra flotta. In questo esempio si selezionano due chiavi, **DeviceID** e **ObjectID**, come chiavi univoche.
* Accettiamo valori null e la mancanza della presenza di una proprietà nel payload dell'evento viene conteggiata come valore null. Questo è anche il modo appropriato per gestire l'invio di dati a due origini eventi in cui i dati in ogni origine evento hanno un ID di serie temporale univoco.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Esempio 2: ID Time Series con una chiave composta

* È necessario che più proprietà siano univoche nella stessa flotta di asset.
* Sei un produttore di Smart Build e Distribuisci sensori in ogni chat room. In ogni stanza, in genere si hanno gli stessi valori per **sensorId**. Esempi sono **sensor1**, **SENSOR2**e **Sensor3**.
* La compilazione presenta numeri di piano e spazio sovrapposti tra i siti nella proprietà **flrRm**. Questi numeri hanno valori quali **1a**, **2B**e **3A**.
* Si dispone di una proprietà, **location**, che contiene valori quali **Redmond**, **Barcelona**e **Tokyo**. Per creare l'univocità, è necessario definire le tre proprietà seguenti come chiavi ID della serie temporale: **sensorId**, **flrRm**e **location**.

Evento RAW di esempio:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Nella portale di Azure è quindi possibile immettere la chiave composta come indicato di seguito:

[![Configure l'ID serie temporale per l'ambiente.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > In portale di Azure non immettere nomi delimitati da virgole di proprietà in un TextBox, in caso contrario verrà considerato come un nome di proprietà singolo contenente virgole.
  > Immettere il nome di ogni proprietà nel proprio TextBox.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [regole di escape e Flat JSON](./concepts-json-flattening-escaping-rules.md) per comprendere come verranno archiviati gli eventi.

* Pianificare l' [ambiente di Azure Time Series Insights Gen2](./time-series-insights-update-plan.md).
