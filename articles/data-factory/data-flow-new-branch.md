---
title: Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory
description: Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387212"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Trasformazione nuovo ramo flusso di dati mapping di Azure Data Factory



![Opzioni ramo](media/data-flow/menu.png "menu")

Con la creazione di un ramo, il flusso di dati corrente viene replicato in un altro flusso. Usare la trasformazione Nuovo ramo per eseguire più set di operazioni e trasformazioni sullo stesso flusso di dati.

Esempio: il flusso di dati dispone di una trasformazione di origine con un set selezionato di colonne e conversioni di tipi di dati. È quindi possibile posizionare una colonna derivata subito dopo tale origine. Nell'elenco Derived Column (Colonna derivata) viene creato un nuovo campo che combina nome e cognome per creare un nuovo campo "Nome completo".

È possibile intervenire sul nuovo flusso con un set di trasformazioni e un sink su una riga, quindi usare la trasformazione Nuovo ramo per creare una copia del flusso in cui è possibile trasformare gli stessi dati con un set diverso di trasformazioni. Trasformando i dati copiati in un ramo separato, è possibile successivamente effettuare il sink di tali dati in un percorso distinto.

> [!NOTE]
> "New Branch" (Nuovo ramo) comparirà come azione nel menu Transformation (Trasformazione) solo in presenza di una trasformazione successiva dopo la posizione corrente in cui si tenta di creare un ramo. Questo significa che non sarà disponibile un'opzione "New Branch" (Nuovo ramo) alla fine, fino a quando non si aggiunge un'altra trasformazione dopo la trasformazione di selezione.

![Ramo](media/data-flow/branch2.png "Ramo 2")
