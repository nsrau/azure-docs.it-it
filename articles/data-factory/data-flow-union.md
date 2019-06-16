---
title: Trasformazione Nuovo ramo per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Nuovo ramo per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348286"
---
# <a name="mapping-data-flow-union-transformation"></a>Trasformazione unione input del flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Unione consente di unire più flussi di dati in uno solo, con un'operazione Union SQL di tali flussi come nuovo output dalla trasformazione Unione. Tutti dello schema di ogni flusso di input vengono combinati all'interno di flusso di dati, senza la necessità di disporre di una chiave di join.

È possibile combinare n: numero di flussi nella tabella impostazioni selezionando l'icona "+" accanto a ogni riga configurato, inclusi i dati di origine sia, nonché i flussi dalle trasformazioni esistente nel flusso di dati.

![Trasformazione unione](media/data-flow/union.png "Union")

In questo caso, puoi combinare diversi metadati provenienti da più origini (in questo esempio, tre file di origine diversa) e combinarli in un singolo flusso:

![Cenni preliminari sulla trasformazione unione](media/data-flow/union111.png "unione 1")

A tale scopo, aggiungere le righe aggiuntive nelle impostazioni dell'unione includendo tutto il codice sorgente che si desidera aggiungere. Non è necessario per una chiave di ricerca o di join comune:

![Le impostazioni di trasformazione unione](media/data-flow/unionsettings.png "impostazioni Union")

Se si imposta una trasformazione seleziona dopo l'unione, sarà possibile rinominare i campi o campi che non erano denominati origini creata sovrapposte. Fare clic su "Controlla" per visualizzare i metadati di combinare con 132 totale colonne in questo esempio da tre origini diverse:

![Trasformazione unione finale](media/data-flow/union333.png "3 Union")

## <a name="name-and-position"></a>Nome e posizione

Quando si sceglie "union in base al nome", ogni valore della colonna eliminerà nella corrispondente colonna da ogni origine, con un nuovo schema di metadati concatenati.

Se si sceglie "union in base alla posizione", ogni valore della colonna eliminerà nella posizione originale da ogni origine corrispondente, risultante in un nuovo flusso di combinato dei dati in cui i dati da ogni origine vengono aggiunti allo stesso flusso:

![Unione output](media/data-flow/unionoutput.png "unione Output")

## <a name="next-steps"></a>Passaggi successivi

Esplorare trasformazioni analoghe compresi [partecipare](data-flow-join.md) e [Exists](data-flow-exists.md).
