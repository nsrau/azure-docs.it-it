---
title: IntelliSense negli strumenti di Analisi di flusso di Azure per il codice di Visual Studio
description: Questo articolo descrive come usare le funzionalità di IntelliSense negli strumenti di Analisi di flusso di Azure per il codice di Visual Studio.This article describes how to use IntelliSense features in Azure Stream Analytics tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394397"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense negli strumenti di Analisi di flusso di Azure per il codice di Visual Studio

IntelliSense è disponibile per [il linguaggio](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) di query di Analisi di flusso negli strumenti di Analisi di flusso di [Azure per il codice VS.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) IntelliSense è lo strumenti di completamento del codice che include numerose funzionalità: Elenca membri, Informazioni sul parametro, Informazioni rapide e Completa parola. Le funzionalità IntelliSense vengono talvolta chiamate da altri nomi, ad esempio "completamento del codice", "content assist" e "code hinting".

![Demo di IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense, funzionalità

Le funzionalità di IntelliSense negli strumenti di Analisi di flusso per il codice VS sono basate su un servizio di linguaggio. Un servizio di linguaggio analizza il codice sorgente e fornisce completamenti intelligenti del codice in base alla semantica del linguaggio. Se un servizio di linguaggio conosce possibili completamenti, i suggerimenti IntelliSense vengono visualizzati durante la digitazione. Se si continua a digitare, viene filtrato un elenco di membri, ad esempio variabili e metodi, in modo da includere solo i membri che contengono i caratteri digitati. Quando si `Tab` premono i tasti o `Enter` , IntelliSense inserisce il membro selezionato.

È possibile attivare IntelliSense in qualsiasi finestra dell'editor `.`digitando un carattere trigger, ad esempio il carattere punto .

![completamento automatico intellisense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Il widget Suggerimenti supporta il filtro CamelCase. È possibile digitare le lettere in maiuscolo nel nome di un metodo per limitare i suggerimenti. Ad esempio, "cra" farà apparire rapidamente "createApplication".

### <a name="types-of-completions"></a>Tipi di completamenti

Strumenti di Analisi di flusso per IntelliSense di codice VS offre diversi tipi di completamenti, tra cui suggerimenti sul server di linguaggio, frammenti e semplici completamenti testuali basati su parole.

|       |         |       |
| ----- | ------- | ----- |
| Parole chiave | `keyword`
| Funzioni | `build-in function`, `user defined function`  |
| Nome set di dati| `input`, `output`, `intermediate result set`|
| Nome colonna set di dati|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Completamento del nome

Oltre al completamento automatico delle parole chiave, gli strumenti di Analisi di flusso per il codice VS legge l'elenco dei nomi di input e output dei processi, nonché i nomi delle colonne nelle origini dati quando vengono configurate. L'estensione memorizza queste informazioni per fornire funzionalità di completamento del nome che sono utili per l'immissione di istruzioni con poche sequenze di tasti:The extension remembers this information to provide name completion capabilities that are useful for entering statements with few keystrokes:

Durante la codifica, non è necessario uscire dall'editor per eseguire ricerche sui nomi di input del processo, sul nome di output e sui nomi delle colonne. È possibile mantenere il contesto, trovare le informazioni necessarie, inserire elementi direttamente nel codice e fare in modo che IntelliSense completi automaticamente la digitazione.

Si noti che è necessario configurare l'input locale o l'input in tempo reale e salvare il file di configurazione per poter utilizzare il completamento del nome.

![completamento del nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informazioni sul parametro

L'opzione **Informazioni parametro** IntelliSense consente di aprire un elenco di parametri che fornisce informazioni sul numero, i nomi e i tipi dei parametri richiesti da una funzione. Il parametro in grassetto indica il parametro successivo richiesto durante la digitazione di una funzione.

L'elenco dei parametri è visualizzato anche per le funzioni nidificate. Se si digita una funzione come parametro per un'altra funzione, nell'elenco dei parametri verranno visualizzati i parametri per la funzione interna. Quando l'elenco dei parametri della funzione interna è completo, verranno quindi visualizzati i parametri della funzione esterna.

![informazioni sui parametri](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informazioni rapide

Come fornito dal servizio di linguaggio, è possibile visualizzare **informazioni rapide** per ogni identificatore nel codice. Alcuni esempi di identificatori sono input, output, un set di risultati intermedio o una funzione. Quando si sposta il puntatore del mouse su un identificatore, la relativa dichiarazione viene visualizzata in una finestra popup. Vengono visualizzate le proprietà e gli schemi di dati per gli input, se configurati, e il set di dati intermedi.

![informazioni rapide](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Risolvere i problemi di IntelliSense

Questo problema è causato dalla configurazione di input mancante che fornisce dati. È possibile verificare se un [input locale](visual-studio-code-local-run.md#define-a-local-input) o [un input live](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) è stato configurato correttamente.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure nel codice di Visual StudioQuickstart: Create an Azure Stream Analytics job in Visual Studio Code](quick-create-vs-code.md)
* [TestStream Analytics esegue query localmente con dati di esempio usando il codice di Visual StudioTest Stream Analytics queries locally with sample data using Visual Studio Code](visual-studio-code-local-run.md)
* [Testare le query di Analisi di flusso in locale sull'input del flusso live usando il codice di Visual StudioTest Stream Analytics queries locally against live stream input by using Visual Studio Code](visual-studio-code-local-run-live-input.md)
