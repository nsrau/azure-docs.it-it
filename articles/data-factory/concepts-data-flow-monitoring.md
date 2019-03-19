---
title: Monitoraggio visivo dei flussi di dati di mapping di Azure Data Factory
description: Informazioni su come monitorare visivamente i flussi di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737859"
---
# <a name="monitor-data-flows"></a>Monitorare i flussi di dati

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Al termine della compilazione e del debug del flusso di dati, è necessario definire l'esecuzione del flusso di dati in base a una pianificazione nel contesto di una pipeline. È possibile pianificare la pipeline da Azure Data Factory tramite trigger. In alternativa, è possibile usare l'opzione Trigger Now (Attiva ora) nel generatore di pipeline di Azure Data Factory per attivare una singola esecuzione in modo da testare il flusso di dati nel contesto della pipeline.

Quando si esegue la pipeline, è possibile monitorarla insieme a tutte le attività che contiene, inclusa l'attività del flusso di dati. Fare clic sull'icona di monitoraggio nel pannello dell'interfaccia utente di Azure Data Factory. Verrà visualizzata una schermata simile alla seguente. Le icone evidenziate permettono di esaminare più in dettaglio le attività nella pipeline, tra cui l'attività del flusso di dati.

<img src="media/data-flow/mon001.png" width="800">

A questo livello verranno visualizzate le statistiche, insieme alle tempistiche e allo stato di esecuzione. L'ID esecuzione a livello di attività è diverso da quello a livello di pipeline. L'ID esecuzione al livello precedente è per la pipeline. Se si fa clic sul simbolo di occhiali, vengono visualizzati ulteriori dettagli sull'esecuzione del flusso di dati.

<img src="media/data-flow/mon002.png" width="800">

Quando ci si trova nella visualizzazione grafica di monitoraggio dei nodi, è possibile osservare una versione semplificata di tipo solo visualizzazione del grafico del flusso di dati.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Visualizzare i piani di esecuzione del flusso di dati

Quando il flusso di dati viene eseguito in Databricks, Azure Data Factory determina i percorsi del codice ottimali in base all'interezza del flusso di dati. Inoltre, i percorsi di esecuzione possono verificarsi in nodi con scalabilità orizzontale e partizioni di dati diversi. Di conseguenza, il grafico di monitoraggio rappresenta la progettazione del flusso, tenendo conto del percorso di esecuzione delle trasformazioni. Quando si fa clic sui singoli nodi, vengono visualizzati "raggruppamenti" che rappresentano il codice che è stato eseguito contemporaneamente nel cluster. Le tempistiche e i conteggi visualizzati rappresentano i gruppi invece dei singoli passaggi nella progettazione.

<img src="media/data-flow/mon004.png" width="800"> 

* Quando si fa clic sull'area aperta nella finestra di monitoraggio, le statistiche nel riquadro inferiore mostrano le tempistiche e i numeri di righe per ogni sink e le trasformazioni che hanno generato i dati sink per la derivazione della trasformazione.

* Quando si selezionano singole trasformazioni, si riceve ulteriore feedback nel pannello a destra che mostra le statistiche sulle partizioni, i numeri di colonne, l'asimmetria (quanto uniformemente sono distribuiti i dati tra le partizioni) e la curtosi (quanti picchi presentano i dati).

* Quando si fa clic sul sink nella visualizzazione del nodo, viene visualizzata la derivazione della colonna. Esistono tre metodi diversi in cui vengono accumulate le colonne lungo il flusso di dati per raggiungere il sink. Sono:

  * Calcolato: si usa la colonna per l'elaborazione condizionale o all'interno di un'espressione nel flusso di dati, ma questa non viene immessa nel sink
  * Derivato: la colonna è una nuova colonna generata nel flusso, ovvero non è presente nell'origine
  * Mappato: la colonna è derivata dall'origine e ne viene eseguito il mapping a un campo sink
  
## <a name="monitor-icons"></a>Icona di monitoraggio

Questa icona indica che i dati della trasformazione sono già stati memorizzati nella cache del cluster e di conseguenza le tempistiche e il percorso di esecuzione sono già stati presi in considerazione:

<img src="media/data-flow/mon005.png" width="800"> 

Nella trasformazione verranno anche visualizzate alcune icone a forma di cerchio verde. Questa rappresentano un conteggio del numero di sink in cui vengono trasmessi i dati.
