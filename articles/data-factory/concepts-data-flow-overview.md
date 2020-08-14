---
title: Flussi di dati di mapping
description: Cenni preliminari sul mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/12/2020
ms.openlocfilehash: ad3fa9db5a15f68f0538b5de29d9a89858c472e9
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212056"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapping di flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

I flussi di dati di mapping sono le trasformazioni di dati progettate visivamente in Azure Data Factory. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di Azure Data Factory pipeline che usano cluster Apache Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere operative usando le funzionalità di pianificazione, controllo, flusso e monitoraggio Azure Data Factory esistenti.

I flussi di dati di mapping forniscono un'esperienza visiva completamente senza necessità di scrivere codice. I flussi di dati vengono eseguiti in cluster di esecuzione gestiti da ADF per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutta la conversione del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Introduzione

I flussi di dati vengono creati dal riquadro risorse Factory, ad esempio pipeline e set di dati. Per creare un flusso di dati, selezionare il segno più accanto a **risorse Factory**, quindi selezionare **flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/new-data-flow.png "nuovo flusso di dati")

Questa azione consente di passare all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione origine. Per ulteriori informazioni, vedere [trasformazione origine](data-flow-source.md).

## <a name="authoring-data-flows"></a>Creazione di flussi di dati

Il mapping di un flusso di dati dispone di un'area di disegno univoca progettata per semplificare la creazione della logica di trasformazione. L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafico

Il grafico mostra il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine**. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello configurazione Mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione del flusso di dati complessiva, è possibile modificare il nome e la descrizione nella scheda **generale** oppure aggiungere parametri tramite la scheda **parametri** . Per ulteriori informazioni, vedere [mapping dei parametri del flusso di dati](parameters-data-flow.md).

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda del riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche della trasformazione. Per ulteriori informazioni, vedere la pagina della documentazione relativa alla trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

#### <a name="optimize"></a>Ottimizzazione

La scheda **ottimizza** contiene le impostazioni per la configurazione degli schemi di partizionamento. Per ulteriori informazioni su come ottimizzare i flussi di dati, vedere la [Guida alle prestazioni del flusso di dati di mapping](concepts-data-flow-performance.md).

![Optimize](media/data-flow/optimize.png "Ottimizzazione") (Ottimizza)

#### <a name="inspect"></a>Controllare

La scheda **Controlla** fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti alle colonne. **Esaminare** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro di **controllo** .

![Controllare](media/data-flow/inspect1.png "Controllare")

Quando si modifica la forma dei dati tramite le trasformazioni, il flusso delle modifiche dei metadati verrà visualizzato nel riquadro **Controlla** . Se non è presente uno schema definito nella trasformazione origine, i metadati non saranno visibili nel riquadro **ispezione** . La mancanza di metadati è comune negli scenari di drifting dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attiva, la scheda **Anteprima dati** fornisce uno snapshot interattivo dei dati a ogni trasformazione. Per altre informazioni, vedere [anteprima dei dati in modalità debug](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che interessano l'intero flusso di dati, ad esempio il salvataggio e la convalida. È possibile visualizzare anche il codice JSON sottostante e lo script del flusso di dati della logica di trasformazione. Per ulteriori informazioni, vedere lo [script del flusso di dati](data-flow-script.md).

## <a name="available-transformations"></a>Trasformazioni disponibili

Per ottenere un elenco delle trasformazioni disponibili, vedere [Cenni preliminari sulla trasformazione del flusso di dati](data-flow-transformation-overview.md) .

## <a name="data-flow-activity"></a>Attività flusso di dati

Il mapping dei flussi di dati viene reso operativo all'interno delle pipeline di ADF utilizzando l' [attività flusso di dati](control-flow-execute-data-flow-activity.md). Tutti gli utenti devono specificare quale runtime di integrazione usare e passare i valori dei parametri. Per altre informazioni, vedere il [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Modalità di debug

La modalità di debug consente di visualizzare in modo interattivo i risultati di ogni passaggio di trasformazione durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia in quando si compila la logica del flusso di dati che nell'esecuzione del debug della pipeline con le attività del flusso di dati. Per ulteriori informazioni, vedere la [documentazione relativa alla modalità di debug](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitoraggio dei flussi di dati

Il mapping del flusso di dati si integra con le funzionalità di monitoraggio Azure Data Factory esistenti. Per informazioni su come comprendere l'output del monitoraggio del flusso di dati, vedere [monitoraggio dei flussi di dati del mapping](concepts-data-flow-monitoring.md).

Il team Azure Data Factory ha creato una [Guida all'ottimizzazione delle prestazioni](concepts-data-flow-performance.md) che consente di ottimizzare il tempo di esecuzione dei flussi di dati dopo aver compilato la logica di business.

## <a name="available-regions"></a>Aree disponibili

I flussi di dati di mapping sono disponibili nelle aree geografiche seguenti:

| Area di Azure | Flussi di dati in ADF | Flussi di dati in sinapsi Studio |
| ------------ | ----------------- | ---------------------------- |
|  Australia centrale | | |  
| Australia centrale 2 | | |
| Australia orientale | ✓ |  ✓ |
| Australia sud-orientale   | ✓ | ✓ |
| Brasile meridionale  | ✓ |  |
| Canada centrale | ✓ |  |
| Central India | ✓ |   ✓ |
| Stati Uniti centrali    | ✓ |   ✓ |
| Cina orientale |      | ✓ |
| Cina orientale 2  |   |    |
| Cina non a livello di area | | |
| Cina settentrionale |     | |
| Cina settentrionale 2 | |  |
| Asia orientale | ✓ | |
| Stati Uniti orientali   | ✓ | ✓ |
| Stati Uniti orientali 2 | ✓ | ✓ |
| Francia centrale | ✓ | ✓ |
| Francia meridionale  | | |
| Germania centrale (sovrano) | | |
| Germania (non a livello di area) (sovrano) | | |
| Germania settentrionale (pubblico) | | |
| Germania nord-orientale (sovrano) | | |
| Germania centro-occidentale (pubblico) |  | ✓ |
| Giappone orientale | ✓ |  |
| Giappone occidentale |  | |
| Corea centrale | ✓ |  |
| Corea meridionale | | |
| Stati Uniti centro-settentrionali  | ✓ | ✓ |
| Europa settentrionale  | ✓ |    |
| Norvegia orientale | | |
| Norvegia occidentale | | |
| Sudafrica settentrionale    | ✓ | |
| Sudafrica occidentale |  |    |
| Stati Uniti centro-meridionali  | | ✓ |
| India meridionale | | |
| Asia sud-orientale    | ✓ | ✓ |
| Svizzera settentrionale |   |  |
| Svizzera occidentale | | |
| Emirati Arabi Uniti centrali | | |
| Emirati Arabi Uniti settentrionali |  |    |
| Regno Unito meridionale  | ✓ |   | ✓ |
| Regno Unito occidentale |     | ✓ |
| US DoD (area centrale) | |  |
| US DoD (area orientale) | |  |
| US Gov Arizona |      |  |
| US Gov (non a livello di area) | |  |
| US Gov Texas | |  |
| US Gov Virginia |     |  |
| Stati Uniti centro-occidentali |     | ✓ |
| Europa occidentale   | ✓ |   ✓ |
| India occidentale | | |
| Stati Uniti occidentali   | ✓ |   |
| West US 2 | ✓ |   ✓ | 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una [trasformazione di origine](data-flow-source.md).
* Informazioni su come creare flussi di dati in [modalità debug](concepts-data-flow-debug-mode.md).
