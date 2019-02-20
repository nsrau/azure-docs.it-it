---
title: Spostare i nodi in Flusso di dati di Azure Data Factory
description: Come spostare i nodi in un diagramma di Flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212537"
---
#<a name="data-factory-data-flow-move-nodes"></a>Spostare i nodi in Flusso di dati di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opzioni della trasformazione Aggregazione](media/data-flow/agghead.png "intestazione aggregatore")

L'area di progettazione di Flusso di dati di Azure Data Factory offre una superficie in cui creare flussi di dati dall'alto verso il basso e da sinistra verso destra. A ogni trasformazione contrassegnata da un segno più (+) è associata una casella degli strumenti. È possibile concentrarsi sulla logica di business invece di collegare i nodi tramite le estremità in un ambiente DAG in formato libero.

Senza un paradigma di trascinamento della selezione, per spostare il nodo di una trasformazione è necessario modificare il codice relativo al flusso in ingresso. Con questa funzionalità si possono invece spostare le trasformazioni nell'area di progettazione modificando il "flusso in ingresso".

In Flusso di dati di Azure Data Factory lo spostamento delle trasformazioni viene rappresentato tramite flussi. Nel riquadro delle impostazioni della trasformazione si noterà un campo "Incoming stream" (Flusso in ingresso). Nel campo è indicato il flusso di dati in ingresso destinato a tale trasformazione. È possibile modificare la posizione fisica del nodo della trasformazione nel grafico facendo clic sul nome del flusso in ingresso e selezionando un altro flusso di dati. La trasformazione corrente su tale flusso, insieme a tutte le trasformazioni successive, verranno così spostate nella nuova posizione.

Se si sposta una trasformazione con una o più trasformazioni successive, la nuova posizione nel flusso di dati verrà unita tramite un nuovo ramo.

Se dopo il nodo selezionato non sono presenti trasformazioni successive, nella nuova posizione verrà spostata solo la trasformazione associata a tale nodo.
