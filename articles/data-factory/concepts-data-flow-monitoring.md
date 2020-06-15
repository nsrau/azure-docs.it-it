---
title: Monitoraggio visivo del flusso di dati di mapping
description: Informazioni su come monitorare visivamente i flussi di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 9594a2ddfaa0103e171618925ba6974bf9ad7f00
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833983"
---
# <a name="monitor-data-flows"></a>Monitorare i flussi di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Al termine della compilazione e del debug del flusso di dati è necessario definire l'esecuzione del flusso di dati in base a una pianificazione nel contesto di una pipeline. È possibile pianificare la pipeline da Azure Data Factory tramite trigger. In alternativa, è possibile usare l'opzione Trigger Now (Attiva ora) nel generatore di pipeline di Azure Data Factory per attivare una singola esecuzione in modo da testare il flusso di dati nel contesto della pipeline.

Quando si esegue la pipeline, è possibile monitorarla insieme a tutte le attività che contiene, inclusa l'attività Flusso di dati. Fare clic sull'icona di monitoraggio nel pannello dell'interfaccia utente di Azure Data Factory. Verrà visualizzata una schermata simile alla seguente. Le icone evidenziate consentono di esaminare più in dettaglio le attività nella pipeline, tra cui l'attività Flusso di dati.

![Monitoraggio dei flussi di dati](media/data-flow/mon001.png "Monitoraggio dei flussi di dati")

A questo livello vengono visualizzate anche le statistiche, insieme alle tempistiche e allo stato di esecuzione. L'ID esecuzione a livello di attività è diverso da quello a livello di pipeline. L'ID esecuzione al livello precedente è per la pipeline. La selezione del simbolo a forma di occhiali consente la visualizzazione di ulteriori dettagli sull'esecuzione del flusso di dati.

![Monitoraggio dei flussi di dati](media/data-flow/mon002.png "Monitoraggio dei flussi di dati")

Durante la visualizzazione grafica di monitoraggio dei nodi, è possibile osservare una versione semplificata di tipo solo visualizzazione del grafico del flusso di dati.

![Monitoraggio dei flussi di dati](media/data-flow/mon003.png "Monitoraggio dei flussi di dati")

Ecco un video informativo sul monitoraggio delle prestazioni dei flussi di dati dalla schermata di monitoraggio di ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Visualizzare i piani di esecuzione del flusso di dati

Quando il Flusso di dati viene eseguito in Spark, Azure Data Factory determina i percorsi del codice ottimali in base all'interezza del flusso di dati. Inoltre, i percorsi di esecuzione possono verificarsi in nodi con scalabilità orizzontale e partizioni di dati diversi. Di conseguenza, il grafico di monitoraggio rappresenta la progettazione del flusso, tenendo conto del percorso di esecuzione delle trasformazioni. La selezione di singoli nodi genera la visualizzazione di "raggruppamenti" che rappresentano il codice che è stato eseguito contemporaneamente nel cluster. Le tempistiche e i conteggi visualizzati rappresentano i gruppi invece dei singoli passaggi nella progettazione.

![Monitoraggio dei flussi di dati](media/data-flow/mon004.png "Monitoraggio dei flussi di dati")

* Quando si seleziona l'area aperta nella finestra di monitoraggio, le statistiche nel riquadro inferiore mostrano le tempistiche e i numeri di righe per ogni sink e le trasformazioni che hanno generato i dati sink per la derivazione della trasformazione.

* Quando si selezionano singole trasformazioni, si riceve ulteriore feedback nel pannello a destra che mostra le statistiche sulle partizioni, i numeri di colonne, l'asimmetria (quanto uniformemente sono distribuiti i dati tra le partizioni) e la curtosi (quanti picchi presentano i dati).

* Quando si seleziona il sink nella visualizzazione del nodo, viene visualizzata la derivazione della colonna. Esistono tre metodi diversi in cui vengono accumulate le colonne lungo il flusso di dati per raggiungere il sink. ovvero:

  * Calcolato: si usa la colonna per l'elaborazione condizionale o all'interno di un'espressione nel flusso di dati, ma questa non viene immessa nel sink
  * Derivato: la colonna è una nuova colonna generata nel flusso, ovvero non è presente nell'origine
  * Mappato: la colonna è derivata dall'origine e ne viene eseguito il mapping a un campo sink
  * Stato del flusso di dati: lo stato attuale dell'esecuzione
  * Tempo di avvio del cluster: periodo di tempo per l'acquisizione dell'ambiente di calcolo di Spark JIT per l'esecuzione del flusso di dati
  * Numero delle trasformazioni: il numero di passaggi di trasformazione eseguiti nel flusso
  
![Monitoraggio dei flussi di dati](media/data-flow/monitornew.png "Monitoraggio dei flussi di dati nuovo")  
  
## <a name="monitor-icons"></a>Icona di monitoraggio

Questa icona indica che i dati della trasformazione sono già stati memorizzati nella cache del cluster e di conseguenza le tempistiche e il percorso di esecuzione sono già stati presi in considerazione:

![Monitoraggio dei flussi di dati](media/data-flow/mon004.png "Monitoraggio dei flussi di dati")

Nella trasformazione vengono anche visualizzate alcune icone a forma di cerchio verde. Questa rappresentano un conteggio del numero di sink in cui vengono trasmessi i dati.
