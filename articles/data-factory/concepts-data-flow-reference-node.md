---
title: Nodo di riferimento in Mapping Data Flow di Azure Data Factory
description: Flusso di dati di Data Factory aggiunge automaticamente un nodo di riferimento per i join, le ricerche e le unioni
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212593"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Nodo di riferimento in Mapping Data Flow di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nodo di riferimento](media/data-flow/referencenode.png "Nodo di riferimento")

Nell'area di disegno viene aggiunto automaticamente un nodo di riferimento per indicare che il nodo cui è associato fa riferimento a un altro nodo esistente nell'area. È possibile considerare un nodo di riferimento come un puntatore a un'altra trasformazione del flusso di dati.

Ad esempio:  quando si esegue il join o l'unione di più flussi di dati, è possibile che l'area di disegno di Flusso di dati aggiunga un nodo di riferimento che riflette il nome e le impostazioni del flusso in ingresso non primario.

Il nodo di riferimento non può essere né spostato né eliminato. È tuttavia possibile fare clic su di esso per modificare le impostazioni della trasformazione di origine.

Le regole dell'interfaccia utente che determinano quando Flusso di dati deve aggiungere il nodo di riferimento si basano sullo spazio disponibile e sulla spaziatura verticale tra le righe.
