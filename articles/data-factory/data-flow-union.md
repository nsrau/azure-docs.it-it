---
title: Trasformazione Nuovo ramo per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Nuovo ramo per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029912"
---
# <a name="mapping-data-flow-union-transformation"></a>Trasformazione Unione flusso di dati mapping



La trasformazione Unione consente di unire più flussi di dati in uno solo, con un'operazione Union SQL di tali flussi come nuovo output dalla trasformazione Unione. Tutti gli schemi di ogni flusso di input verranno combinati all'interno del flusso di dati, senza che sia necessario disporre di una chiave di join.

È possibile combinare n-Number di flussi nella tabella Settings selezionando l'icona "+" accanto a ogni riga configurata, inclusi sia i dati di origine che i flussi delle trasformazioni esistenti nel flusso di dati.

![](media/data-flow/union.png "Unione") trasformazione Unione

In questo caso, è possibile combinare metadati diversi da più origini (in questo esempio, tre diversi file di origine) e combinarli in un singolo flusso:

![Panoramica di Union Transformation]-(media/data-flow/union111.png "Unione 1")

A tale scopo, aggiungere altre righe nelle impostazioni di Unione includendo tutte le origini che si desidera aggiungere. Non è necessaria una chiave di join o di ricerca comune:

(media/data-flow/unionsettings.png "Impostazioni Unione") ![Impostazioni trasformazione Unione]

Se si imposta una trasformazione seleziona dopo l'Unione, sarà possibile rinominare campi o campi sovrapposti non denominati da origini senza intestazione. Fare clic su "controlla" per visualizzare le colonne combina metadati con 132 totali in questo esempio da tre origini diverse:

Unione ![finale trasformazione Unione](media/data-flow/union333.png "3")

## <a name="name-and-position"></a>Nome e posizione

Quando si sceglie "Unione per nome", ogni valore di colonna viene inserito nella colonna corrispondente da ogni origine, con un nuovo schema di metadati concatenato.

Se si sceglie "Union by position", ogni valore della colonna verrà inserito nella posizione originale da ogni origine corrispondente, ottenendo un nuovo flusso combinato di dati in cui i dati di ogni origine vengono aggiunti allo stesso flusso:

(media/data-flow/unionoutput.png "Output Unione") di ![output Unione]

## <a name="next-steps"></a>Passaggi successivi

Esplora le trasformazioni simili che includono [join](data-flow-join.md) ed [Exists](data-flow-exists.md).
