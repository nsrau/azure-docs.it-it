---
title: Trasformazione nuovo ramo flusso di dati mapping
description: Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930305"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory

![Opzioni ramo](media/data-flow/menu.png "menu")

Con la creazione di un ramo, il flusso di dati corrente viene replicato in un altro flusso. Usare la trasformazione Nuovo ramo per eseguire più set di operazioni e trasformazioni sullo stesso flusso di dati.

Esempio: il flusso di dati dispone di una trasformazione di origine con un set selezionato di colonne e conversioni di tipi di dati. È quindi possibile posizionare una colonna derivata subito dopo tale origine. Nell'elenco Derived Column (Colonna derivata) viene creato un nuovo campo che combina nome e cognome per creare un nuovo campo "Nome completo".

È possibile intervenire sul nuovo flusso con un set di trasformazioni e un sink su una riga, quindi usare la trasformazione Nuovo ramo per creare una copia del flusso in cui è possibile trasformare gli stessi dati con un set diverso di trasformazioni. Trasformando i dati copiati in un ramo separato, è possibile successivamente effettuare il sink di tali dati in un percorso distinto.

> [!NOTE]
> "New Branch" (Nuovo ramo) comparirà come azione nel menu Transformation (Trasformazione) solo in presenza di una trasformazione successiva dopo la posizione corrente in cui si tenta di creare un ramo. Questo significa che non sarà disponibile un'opzione "New Branch" (Nuovo ramo) alla fine, fino a quando non si aggiunge un'altra trasformazione dopo la trasformazione di selezione.

![Ramo](media/data-flow/branch2.png "Ramo 2")
