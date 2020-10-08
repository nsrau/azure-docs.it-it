---
title: Trasformazione Unione nel flusso di dati di mapping
description: Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e10418c18e6f8e0089a893e9d33ee1081f3131f1
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823635"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Trasformazione Unione nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione Unione consente di unire più flussi di dati in uno solo, con un'operazione Union SQL di tali flussi come nuovo output dalla trasformazione Unione. Tutti gli schemi di ogni flusso di input verranno combinati all'interno del flusso di dati, senza che sia necessario disporre di una chiave di join.

È possibile combinare n-Number di flussi nella tabella Settings selezionando l'icona "+" accanto a ogni riga configurata, inclusi sia i dati di origine che i flussi delle trasformazioni esistenti nel flusso di dati.

Ecco una breve descrizione dettagliata della trasformazione Unione nel flusso di dati di mapping di ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Trasformazione Unione](media/data-flow/union.png "Union")

In questo caso, è possibile combinare metadati diversi da più origini (in questo esempio, tre diversi file di origine) e combinarli in un singolo flusso:

![Cenni preliminari sulla trasformazione Unione](media/data-flow/union111.png "Unione 1")

A tale scopo, aggiungere altre righe nelle impostazioni di Unione includendo tutte le origini che si desidera aggiungere. Non è necessaria una chiave di join o di ricerca comune:

![Impostazioni di trasformazione Unione](media/data-flow/unionsettings.png "Impostazioni Unione")

Se si imposta una trasformazione seleziona dopo l'Unione, sarà possibile rinominare campi o campi sovrapposti non denominati da origini senza intestazione. Fare clic su "controlla" per visualizzare le colonne combina metadati con 132 totali in questo esempio da tre origini diverse:

![Unione finale trasformazione](media/data-flow/union333.png "Unione 3")

## <a name="name-and-position"></a>Nome e posizione

Quando si sceglie "Unione per nome", ogni valore di colonna viene inserito nella colonna corrispondente da ogni origine, con un nuovo schema di metadati concatenato.

Se si sceglie "Union by position", ogni valore della colonna verrà inserito nella posizione originale da ogni origine corrispondente, ottenendo un nuovo flusso combinato di dati in cui i dati di ogni origine vengono aggiunti allo stesso flusso:

![Output Unione](media/data-flow/unionoutput.png "Output Unione")

## <a name="next-steps"></a>Passaggi successivi

Esplora le trasformazioni simili che includono [join](data-flow-join.md) ed [Exists](data-flow-exists.md).
