---
title: Ispezione e anteprima dei dati in Mapping Data Flow di Azure Data Factory
description: Mapping Data Flow di Azure Data Factory include il riquadro Inspect (Ispeziona), che mostra i metadati del flusso di dati, e il riquadro Data Preview (Anteprima dati), che consente di visualizzare l'anteprima dei dati in modalità Debug
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212538"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Riquadro per l'ispezione delle trasformazioni in Mapping Data Flow di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Riquadro di ispezione](media/data-flow/agg3.png "Riquadro di ispezione")

Il riquadro Inspect (Ispeziona) offre una visualizzazione dei metadati del flusso di dati che è in corso di trasformazione. È possibile visualizzare il numero di colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordinamento delle colonne e i riferimenti di colonna. Il riquadro presenta una visualizzazione di sola lettura dei metadati. Per visualizzare i metadati nel riquadro Inspect (Ispeziona) non è necessario che la modalità Debug sia attivata.

Quando si cambia la forma dei dati tramite trasformazioni, la modifica si riflette nel flusso dei metadati all'interno del riquadro Inspect (Ispeziona). Se nella trasformazione di origine non è definito uno schema, i metadati non saranno visibili nel riquadro. L'assenza di metadati è comune negli scenari di deviazione dello schema.

![Anteprima dati](media/data-flow/datapreview.png "Anteprima dati")

Data Preview (Anteprima dati) è un riquadro che presenta una visualizzazione di sola lettura dei dati in corso di trasformazione. È possibile visualizzare l'output della trasformazione e le espressioni per convalidare il flusso di dati. Per visualizzare le anteprime dei dati è necessario che la modalità Debug sia attivata. Quando si fa clic sulle colonne nella griglia di anteprima dei dati, a destra viene visualizzato un pannello a comparsa che mostra le statistiche del profilo su ogni colonna selezionata.

![Grafico di anteprima dei dati](media/data-flow/chart.png "Grafico di anteprima dei dati")

