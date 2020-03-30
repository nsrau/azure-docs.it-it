---
title: Mapping della trasformazione dell'unione del flusso di dati
description: Azure Data Factory mapping data flow New Branch Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: adba1eb61676dbebcb356490b14b279ebe69c644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930156"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Trasformazione dell'unione del flusso di dati di mapping di Azure Data FactoryAzure Data Factory mapping data union transformation

La trasformazione Unione consente di unire più flussi di dati in uno solo, con un'operazione Union SQL di tali flussi come nuovo output dalla trasformazione Unione. Tutto lo schema di ogni flusso di input verrà combinato all'interno del flusso di dati, senza che sia necessario disporre di una chiave di join.

È possibile combinare n-numero di flussi nella tabella delle impostazioni selezionando l'icona "" accanto a ogni riga configurata, inclusi i dati di origine e i flussi delle trasformazioni esistenti nel flusso di dati.

![Trasformazione Unione](media/data-flow/union.png "Union")

In questo caso, è possibile combinare metadati disparati da più origini (in questo esempio, tre file di origine diversi) e combinarli in un unico flusso:In this case, you can combine disparate metadata from multiple sources (in this example, three different source files) and combine them into a single stream:

![Panoramica della trasformazione dell'unione](media/data-flow/union111.png "Unione 1")

A tale scopo, aggiungere altre righe nelle impostazioni dell'unione includendo tutte le origini che si desidera aggiungere. Non è necessaria una chiave di ricerca o join comune:There is no need for a common lookup or join key:

![Impostazioni di trasformazione dell'unione](media/data-flow/unionsettings.png "Impostazioni dell'unione")

Se si imposta una trasformazione Seleziona dopo l'unione, sarà possibile rinominare i campi sovrapposti o i campi che non sono stati denominati da origini senza intestazione. Fare clic su "Inspect" per visualizzare la combinazione di metadati con 132 colonne totali in questo esempio da tre origini diverse:

![Finale di trasformazione dell'Unione](media/data-flow/union333.png "Unione 3")

## <a name="name-and-position"></a>Nome e posizione

Quando si sceglie "Unione per nome", ogni valore di colonna verrà inserito nella colonna corrispondente da ogni origine, con un nuovo schema di metadati concatenato.

Se si sceglie "Unione per posizione", ogni valore di colonna cadrà nella posizione originale da ogni origine corrispondente, generando un nuovo flusso combinato di dati in cui i dati di ogni origine vengono aggiunti allo stesso flusso:

![Produzione dell'Unione](media/data-flow/unionoutput.png "Produzione dell'Unione")

## <a name="next-steps"></a>Passaggi successivi

Esplora trasformazioni simili, tra cui [Join](data-flow-join.md) ed [Exists](data-flow-exists.md).
