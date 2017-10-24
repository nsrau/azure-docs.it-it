---
title: "Prestazioni e scalabilità in Funzioni permanenti - Azure"
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestazioni e scalabilità in Funzioni permanenti (Funzioni di Azure)

Per ottimizzare le prestazioni e la scalabilità, è importante comprendere le caratteristiche univoche di scalabilità di [Funzioni permanenti](durable-functions-overview.md).

Per comprendere il comportamento della scalabilità, è necessario comprendere alcuni dettagli del provider di Archiviazione di Azure sottostante usato da Funzioni permanenti.

## <a name="history-table"></a>Tabella di cronologia

La tabella di cronologia è una tabella di Archiviazione di Azure che contiene gli eventi di cronologia per tutte le istanze di orchestrazione. Durante l'esecuzione di istanze vengono aggiunte nuove righe alla tabella. La chiave di partizione della tabella deriva dall'ID di istanza dell'orchestrazione. Nella maggior parte dei casi questi valori sono casuali garantendo la distribuzione ottimale delle partizioni interne in Archiviazione di Azure.

## <a name="internal-queue-triggers"></a>Trigger di code interne

Le funzioni di orchestrazione e le funzioni di attività vengono attivate da code interne nell'account di archiviazione predefinito dell'app per le funzioni. In Funzioni permanenti esistono due tipi di code: la **coda di controllo** e la **coda di elementi di lavoro**.

### <a name="the-work-item-queue"></a>La coda di elementi di lavoro

Esiste una coda di elementi di lavoro per ogni hub attività in Funzioni permanenti. È una coda di base che funziona in modo analogo a qualsiasi altra coda `queueTrigger` in Funzioni di Azure. Viene usata per attivare le *funzioni di attività* senza stato. Quando viene eseguita la scalabilità orizzontale di un'applicazione di Funzioni permanenti in più macchine virtuali, tutte queste macchine virtuali competono per acquisire lavoro dalla coda di elementi di lavoro.

### <a name="control-queues"></a>Code di controllo

La *coda di controllo* è più complessa della coda di elementi di lavoro. Viene usata per attivare le funzioni di orchestrazione con stato. Poiché le istanze della funzione dell'agente di orchestrazione sono di tipo singleton con stato, non è possibile usare un modello di consumer concorrenti per distribuire il carico tra le macchine virtuali. I messaggi di orchestrazione funzionano invece con bilanciamento del carico tra più code di controllo. Altri dettagli sono disponibili nelle sezioni successive.

Le code di controllo contengono messaggi di diverso tipo relativi al ciclo di vita di orchestrazione. Gli esempi includono i [messaggi di controllo dell'agente di orchestrazione](durable-functions-instance-management.md), i messaggi di *risposta* delle funzioni di attività e i messaggi del timer.

## <a name="orchestrator-scale-out"></a>Scalabilità orizzontale dell'agente di orchestrazione

Le funzioni di attività sono senza stato e vengono scalate orizzontalmente in modo automatico aggiungendo macchine virtuali. D'altra parte le funzioni di orchestrazione vengono *partizionate* tra una o più code di controllo. Il numero di code di controllo è fisso e non può essere modificato dopo l'inizio della creazione del carico.

Quando si esegue la scalabilità orizzontale in più istanze dell'host di funzioni, in genere su diverse macchine virtuali, ogni istanza acquisisce un blocco su una delle code di controllo. Questo blocco assicura che un'istanza di orchestrazione venga eseguita solo in una singola macchina virtuale alla volta. Se quindi un hub attività viene configurato con tre code di controllo, il bilanciamento del carico delle istanze di orchestrazione può essere eseguito tra un massimo di tre macchine virtuali. È possibile aggiungere altre macchine virtuali per aumentare la capacità per l'esecuzione della funzione di attività.  Le risorse aggiuntive non verranno tuttavia usate per eseguire le funzioni di orchestrazione.

Il diagramma seguente illustra l'interazione tra l'host di Funzioni di Azure e le entità di archiviazione in un ambiente con scalabilità orizzontale.

![Diagramma di scalabilità](media/durable-functions-perf-and-scale/scale-diagram.png)

Come si può notare, tutte le macchine virtuali possono competere per i messaggi nella coda di elementi di lavoro. Tuttavia, solo tre macchine virtuali possono acquisire i messaggi dalle code di controllo e ogni macchina virtuale blocca una singola coda di controllo.

Le istanze di orchestrazione vengono distribuite tra più istanze delle code di controllo tramite l'esecuzione di una funzione hash interna rispetto all'ID istanza di orchestrazione. Gli ID istanza vengono generati automaticamente e in modo casuale per impostazione predefinita garantendo che le istanze vengano bilanciate tra tutte le code di controllo disponibili. Il numero predefinito corrente di partizioni di code di controllo supportate è **4**.

> [!NOTE]
> Non è attualmente possibile configurare il numero di partizioni in Funzioni di Azure. [L'attività per supportare questa opzione di configurazione è corso di rilevamento](https://github.com/Azure/azure-functions-durable-extension/issues/73).

In generale, le funzioni di orchestrazione sono progettate per essere semplici e non richiedono elevata potenza di calcolo. Per questo motivo non è necessario creare un numero elevato di partizioni delle code di controllo per ottenere una velocità effettiva ottimale. La maggior parte del lavoro intenso viene invece eseguito nelle funzioni di attività senza stato, che possono essere scalate orizzontalmente all'infinito.

## <a name="auto-scale"></a>Scalabilità automatica

Come con tutte le Funzioni di Azure in esecuzione nel piano a consumo, le Funzioni permanenti supportano la scalabilità automatica tramite il [controller di scalabilità di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). Il controller di scalabilità monitora la lunghezza della coda di elementi di lavoro e di ogni coda di controllo, aggiungendo e rimuovendo le risorse di macchina virtuale di conseguenza. Se le lunghezze delle code di controllo aumentano nel tempo, il controller di scalabilità continuerà ad aggiungere istanze fino a raggiungere il numero di partizioni di code di controllo. Se le lunghezze delle code di elementi di lavoro aumentano nel tempo, il controller di scalabilità continuerà ad aggiungere risorse di macchine virtuali a seconda del carico, indipendentemente dal numero di partizioni di code di controllo.

## <a name="thread-usage"></a>Utilizzo di thread

Le funzioni di orchestrazione vengono eseguite su un thread singolo. Questa condizione è necessaria per garantire che l'esecuzione della funzione dell'agente di orchestrazione sia di tipo deterministico. A tal fine è importante non mantenere occupato inutilmente il thread della funzione dell'agente di orchestrazione con attività, ad esempio con operazioni di I/O (non consentite per una serie di motivi), di blocco o di rotazione. Tutto il lavoro che richiede thread di I/O, di blocco o più thread deve essere spostato nelle funzioni di attività.

Le funzioni di attività hanno gli stessi comportamenti delle normali funzioni attivate da coda. Ciò significa che è possibile eseguire in modo sicuro operazioni di I/O, con utilizzo intensivo della CPU e usare più thread. Poiché i trigger di attività sono senza stato, è possibile eseguire la scalabilità orizzontale a un numero illimitato di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare l'estensione Funzioni permanenti e i relativi esempi](durable-functions-install.md)
