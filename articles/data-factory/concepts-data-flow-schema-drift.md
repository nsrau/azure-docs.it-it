---
title: Spostamento dello schema nel flusso di dati del mapping | Azure Data Factory
description: Compilare flussi di dati resilienti in Azure Data Factory con la deviazione dello schema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8c2764535515d0aeb1eb65a1621148fa58317cac
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553689"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Spostamento dello schema nel flusso di dati del mapping

La deriva dello schema è il caso in cui le origini spesso cambiano i metadati. I campi, le colonne e i tipi possono essere aggiunti, rimossi o modificati in tempo reale. Senza gestire la deriva dello schema, il flusso di dati diventa vulnerabile alle modifiche dell'origine dati upstream. I modelli ETL tipici hanno esito negativo quando le colonne e i campi in arrivo cambiano perché tendono a essere associati a questi nomi di origine.

Per proteggersi dalla deriva dello schema, è importante che le funzionalità di uno strumento flusso di dati consentano di:

* Definire le origini con nomi di campi, tipi di dati, valori e dimensioni modificabili
* Definire i parametri di trasformazione che possono funzionare con i modelli di dati invece di valori e campi impostati come hardcoded
* Definire le espressioni in grado di riconoscere i criteri per la corrispondenza dei campi in ingresso, invece di usare campi denominati

Azure Data Factory supporta in modo nativo schemi flessibili che passano dall'esecuzione all'esecuzione, in modo da poter compilare la logica di trasformazione dei dati generici senza la necessità di ricompilare i flussi di dati.

Nel flusso di dati è necessario prendere una decisione dal punto di vista dell'architettura riguardo a se accettare la deviazione dello schema in tutto il flusso. In questo modo, è possibile proteggersi da modifiche dello schema rispetto alle origini. Tuttavia, si perderanno le prime associazioni delle colonne e dei tipi nel flusso di dati. Azure Data Factory considera i flussi di derivazione dello schema come flussi di associazione tardiva. Pertanto, quando si compilano le trasformazioni, i nomi delle colonne non saranno disponibili nelle viste dello schema in tutto il flusso.

## <a name="schema-drift-in-source"></a>Spostamento dello schema nell'origine

Le colonne che entrano nel flusso di dati dalla definizione di origine sono definite come "spostate" quando non sono presenti nella proiezione di origine. È possibile visualizzare la proiezione di origine dalla scheda proiezione nella trasformazione origine. Quando si seleziona un set di dati per l'origine, ADF prenderà automaticamente lo schema dal set di dati e creerà un progetto da tale definizione dello schema del set di dati.

In una trasformazione origine la deriva dello schema è definita come la lettura di colonne che non sono definite nello schema del set di dati. Per abilitare la deriva dello schema, selezionare **Consenti Drift schema** nella trasformazione origine.

![Origine della deriva dello schema](media/data-flow/schemadrift001.png "Origine della deriva dello schema")

Quando lo spostamento dello schema è abilitato, tutti i campi in ingresso vengono letti dall'origine durante l'esecuzione e passati attraverso l'intero flusso al sink. Per impostazione predefinita, tutte le colonne rilevate di recente, note come *colonne*trascinate, arrivano come tipo di dati stringa. Se si desidera che il flusso di dati deduca automaticamente i tipi di dati delle colonne di cui è stato possibile eseguire il drifting, controllare **dedurre i tipi di colonna** in base alle impostazioni di origine

## <a name="schema-drift-in-sink"></a>Spostamento dello schema nel sink

In una trasformazione sink la deriva dello schema è quando si scrivono colonne aggiuntive in base a quanto definito nello schema dei dati sink. Per abilitare la deriva dello schema, selezionare **Consenti la deriva dello schema** nella trasformazione del sink.

![Sink di Drift dello schema](media/data-flow/schemadrift002.png "Sink di Drift dello schema")

Se la deriva dello schema è abilitata, assicurarsi che il dispositivo di scorrimento **mapping automatico** nella scheda mapping sia attivato. Con questo dispositivo di scorrimento, tutte le colonne in ingresso vengono scritte nella destinazione. In caso contrario, è necessario utilizzare il mapping basato su regole per scrivere colonne trascinate.

![Mapping automatico sink](media/data-flow/automap.png "Mapping automatico sink")

## <a name="transforming-drifted-columns"></a>Trasformazione di colonne derivate

Quando nel flusso di dati sono presenti colonne spostate, è possibile accedervi nelle trasformazioni con i metodi seguenti:

* Utilizzare le espressioni `byPosition` e `byName` per fare riferimento in modo esplicito a una colonna in base al nome o al numero di posizione.
* Aggiungere un modello di colonna in una colonna derivata o una trasformazione aggregazione in modo che corrisponda a qualsiasi combinazione di nome, flusso, posizione o tipo
* Aggiunta di un mapping basato su regole in una trasformazione SELECT o sink per la corrispondenza tra colonne spostate e alias di colonne tramite un modello

Per ulteriori informazioni su come implementare i modelli di colonna, vedere [modelli di colonna nel flusso di dati di mapping](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Azione rapida per le colonne con drifting

Per fare riferimento in modo esplicito alle colonne derivate, è possibile generare rapidamente i mapping per queste colonne tramite un'azione rapida di anteprima dei dati. Una volta impostata la [modalità di debug](concepts-data-flow-debug-mode.md) , passare alla scheda Anteprima dati e fare clic su **Aggiorna** per recuperare un'anteprima dei dati. Se data factory rileva che sono presenti colonne spostate, è possibile fare clic su **mappa** e generare una colonna derivata che consente di fare riferimento a tutte le colonne spostate nelle viste dello schema downstream.

![Mappa con deviazione](media/data-flow/mapdrifted1.png "Mappa con deviazione")

Nella trasformazione colonna derivata generata, viene eseguito il mapping di ogni colonna spostata al nome e al tipo di dati rilevati. Nell'anteprima dei dati sopra riportata, la colonna "movieId" viene rilevata come numero intero. Dopo aver fatto clic su **mapping** , movieId viene definito nella colonna derivata come `toInteger(byName('movieId'))` e incluso nelle viste dello schema nelle trasformazioni downstream.

![Mappa con deviazione](media/data-flow/mapdrifted2.png "Mappa con deviazione")

## <a name="next-steps"></a>Passaggi successivi
Nel [linguaggio delle espressioni del flusso di dati](data-flow-expression-functions.md)sono disponibili ulteriori funzionalità per i modelli di colonna e la deriva dello schema, tra cui "byName" e "byPosition".
