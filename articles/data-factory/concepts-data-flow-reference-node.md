---
title: Nodo di riferimento in Mapping Data Flow di Azure Data Factory
description: Flusso di dati di Data Factory aggiunge automaticamente un nodo di riferimento per i join, le ricerche e le unioni
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030503"
---
# <a name="mapping-data-flow-reference-node"></a>Nodo di riferimento di Flusso di dati di mapping



![Nodo di riferimento](media/data-flow/referencenode.png "Nodo di riferimento")

Nell'area di disegno viene aggiunto automaticamente un nodo di riferimento per indicare che il nodo cui è associato fa riferimento a un altro nodo esistente nell'area. È possibile considerare un nodo di riferimento come un puntatore a un'altra trasformazione del flusso di dati.

Esempio: quando si esegue il join o l'unione di più flussi di dati, è possibile che l'area di disegno di Flusso di dati aggiunga un nodo di riferimento che riflette il nome e le impostazioni del flusso in ingresso non primario.

Il nodo di riferimento non può essere né spostato né eliminato. È tuttavia possibile fare clic su di esso per modificare le impostazioni della trasformazione di origine.

Le regole dell'interfaccia utente che determinano quando Flusso di dati deve aggiungere il nodo di riferimento si basano sullo spazio disponibile e sulla spaziatura verticale tra le righe.
