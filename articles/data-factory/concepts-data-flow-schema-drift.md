---
title: Deriva dello schema nel mapping del flusso di datiSchema drift in mapping data flow
description: Compilare flussi di dati resilienti in Azure Data Factory con la deviazione dello schema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 6e361d23860ce8f40abba5c246242cf345bb974c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606105"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Deriva dello schema nel mapping del flusso di datiSchema drift in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La deriva dello schema è il caso in cui le origini spesso modificano i metadati. È possibile aggiungere, rimuovere o modificare campi, colonne e tipi al volo. Senza la gestione per la deriva dello schema, il flusso di dati diventa vulnerabile alle modifiche dell'origine dati a monte. I modelli ETL tipici hanno esito negativo quando le colonne e i campi in ingresso cambiano perché tendono ad essere legati a tali nomi di origine.

Per proteggersi dalla deriva dello schema, è importante disporre delle funzionalità in uno strumento del flusso di dati che consentano, in qualità di ingegnere dei dati, di:To protect against schema drift, it's important to have the facilities in a data flow tool to allow you, as a Data Engineer, to:

* Definire origini con nomi di campo, tipi di dati, valori e dimensioni modificabili
* Definire i parametri di trasformazione che possono funzionare con i modelli di dati invece di valori e campi impostati come hardcoded
* Definire le espressioni in grado di riconoscere i criteri per la corrispondenza dei campi in ingresso, invece di usare campi denominati

Azure Data Factory supporta in modo nativo schemi flessibili che cambiano dall'esecuzione all'esecuzione in modo che sia possibile creare una logica di trasformazione dei dati generica senza la necessità di ricompilare i flussi di dati.

Nel flusso di dati è necessario prendere una decisione dal punto di vista dell'architettura riguardo a se accettare la deviazione dello schema in tutto il flusso. In questo modo, è possibile proteggersi da modifiche dello schema rispetto alle origini. Tuttavia, si perderà l'associazione anticipata delle colonne e dei tipi in tutto il flusso di dati. Azure Data Factory considera i flussi di derapata dello schema come flussi di associazione tardiva, pertanto quando si compilano le trasformazioni, i nomi delle colonne con deriva non saranno disponibili nelle visualizzazioni dello schema in tutto il flusso.

Questo video fornisce un'introduzione ad alcune delle soluzioni complesse che è possibile compilare facilmente in ADF con la funzionalità di deriva dello schema del flusso di dati. In questo esempio vengono compilati modelli riutilizzabili basati su schemi di database flessibili:In this example, we build reusable patterns based on flexible database schemas:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Deriva dello schema nell'origineSchema drift in source

Le colonne che entrano nel flusso di dati dalla definizione di origine vengono definite come "drifted" quando non sono presenti nella proiezione di origine. È possibile visualizzare la proiezione di origine dalla scheda di proiezione nella trasformazione di origine. Quando si seleziona un set di dati per l'origine, ADF prende automaticamente lo schema dal set di dati e crea un progetto da tale definizione di schema del set di dati.

In una trasformazione di origine, la deriva dello schema è definita come colonne di lettura che non sono definite nello schema del set di dati. Per abilitare la deriva dello schema, selezionare **Consenti deriva dello schema** nella trasformazione di origine.

![Origine deriva dello schema](media/data-flow/schemadrift001.png "Origine deriva dello schema")

Quando la deriva dello schema è abilitata, tutti i campi in ingresso vengono letti dall'origine durante l'esecuzione e passati attraverso l'intero flusso al sink. Per impostazione predefinita, tutte le colonne appena rilevate, note come *colonne di deriva*, arrivano come tipo di dati stringa. Se si desidera che il flusso di dati deduci automaticamente i tipi di dati delle colonne di deriva, selezionare **Infer (Infer) i tipi di colonna derivia** nelle impostazioni di origine.

## <a name="schema-drift-in-sink"></a>deriva dello schema nel sinkSchema drift in sink

In una trasformazione sink, la deriva dello schema è quando si scrivono colonne aggiuntive sopra ciò che è definito nello schema dei dati sink. Per abilitare la deriva dello schema, **selezionare Consenti deriva dello schema** nella trasformazione sink.

![Sink di deriva dello schemaSchema drift sink](media/data-flow/schemadrift002.png "Sink di deriva dello schemaSchema drift sink")

Se la deriva dello schema è abilitata, assicurarsi che il dispositivo di scorrimento **Mapping automatico** nella scheda Mapping sia attivato. Con questo cursore attivato, tutte le colonne in arrivo vengono scritte nella destinazione. In caso contrario, è necessario utilizzare il mapping basato su regole per scrivere le colonne di deriva.

![Mapping automatico sink](media/data-flow/automap.png "Mapping automatico sink")

## <a name="transforming-drifted-columns"></a>Trasformazione delle colonne di deriva

Quando il flusso di dati contiene colonne di deriva, è possibile accedervi nelle trasformazioni con i metodi seguenti:When your data flow has drifted columns, you can access them in your transformations with the following methods:

* Utilizzare `byPosition` le `byName` espressioni e per fare riferimento in modo esplicito a una colonna in base al nome o al numero di posizione.
* Aggiungere un modello di colonna in una trasformazione Colonna derivata o Aggregazione in modo che corrisponda a qualsiasi combinazione di nome, flusso, posizione o tipoAdd a column pattern in a Derived Column or Aggregate transformation to match on any combination of name, stream, position, or type
* Aggiungere il mapping basato su regole in una trasformazione Seleziona o Sink per associare le colonne di deriva agli alias delle colonne tramite un modelloAdd rule-based mapping in a Select or Sink transformation to match drifted columns to columns aliases via a pattern

Per altre informazioni su come implementare modelli di colonna, vedere Modelli di colonna nel mapping del [flusso di dati.](concepts-data-flow-column-pattern.md)

### <a name="map-drifted-columns-quick-action"></a>Eseguire rapidamente la ricerca di colonne alla deriva

Per fare riferimento in modo esplicito alle colonne con deriva, è possibile generare rapidamente i mapping per queste colonne tramite un'azione rapida di anteprima dei dati. Quando la [modalità](concepts-data-flow-debug-mode.md) di debug è attiva, passare alla scheda Anteprima dati e fare clic su **Aggiorna** per recuperare un'anteprima dei dati. Se la data factory rileva l'esistenza di colonne di deriva, è possibile fare clic su **Mappa Drifted** e generare una colonna derivata che consente di fare riferimento a tutte le colonne di deriva nelle viste dello schema a valle.

![Mappa alla deriva](media/data-flow/mapdrifted1.png "Mappa alla deriva")

Nella trasformazione Colonna derivata generata, ogni colonna con deriva viene mappata al nome e al tipo di dati rilevati. Nell'anteprima dei dati precedente, la colonna 'movieId' viene rilevata come numero intero. Dopo aver fatto clic su **Map Drifted,** movieId viene definito nella colonna derivata come `toInteger(byName('movieId'))` e incluso nelle visualizzazioni dello schema nelle trasformazioni downstream.

![Mappa alla deriva](media/data-flow/mapdrifted2.png "Mappa alla deriva")

## <a name="next-steps"></a>Passaggi successivi
In [Data Flow Expression Language](data-flow-expression-functions.md)sono disponibili ulteriori funzionalità per i modelli di colonna e la deriva dello schema, tra cui "byName" e "byPosition".
