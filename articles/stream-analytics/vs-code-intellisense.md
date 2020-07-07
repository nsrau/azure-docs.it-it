---
title: IntelliSense negli strumenti di analisi di flusso di Azure per Visual Studio Code
description: Questo articolo descrive come usare le funzionalità di IntelliSense negli strumenti di analisi di flusso di Azure per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81394397"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense negli strumenti di analisi di flusso di Azure per Visual Studio Code

IntelliSense è disponibile per il [linguaggio di query di analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) negli [strumenti di analisi di flusso di Azure per vs code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense è lo strumenti di completamento del codice che include numerose funzionalità: Elenca membri, Informazioni sul parametro, Informazioni rapide e Completa parola. Le funzionalità di IntelliSense vengono talvolta chiamate da altri nomi, ad esempio "completamento del codice", "supporto contenuto" e "hint di codice".

![Demo di IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense, funzionalità

Le funzionalità di IntelliSense negli strumenti di analisi di flusso per VS Code sono basate su un servizio di linguaggio. Un servizio di linguaggio analizza il codice sorgente e fornisce completamenti di codice intelligenti basati sulla semantica del linguaggio. Se un servizio di linguaggio conosce i possibili completamenti, i suggerimenti di IntelliSense vengono visualizzati durante la digitazione. Se si continua a digitare, un elenco di membri, ad esempio variabili e metodi, viene filtrato in modo da includere solo i membri che contengono i caratteri digitati. Quando si preme il `Tab` `Enter` tasto o, IntelliSense inserisce il membro selezionato.

È possibile attivare IntelliSense in qualsiasi finestra dell'editor digitando un carattere del trigger, ad esempio il carattere punto `.` .

![completamento automatico IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Il widget suggerimenti supporta il filtro CamelCase. È possibile digitare le lettere maiuscole in un nome di metodo per limitare i suggerimenti. "CRA", ad esempio, consente di visualizzare rapidamente "createApplication".

### <a name="types-of-completions"></a>Tipi di completamenti

Strumenti di analisi di flusso per VS Code IntelliSense offre diversi tipi di completamenti, tra cui suggerimenti per i server di linguaggio, frammenti di codice e semplici completamenti testuali basati su parole.

|       |         |       |
| ----- | ------- | ----- |
| Parole chiave | `keyword`
| Funzioni | `build-in function`, `user defined function`  |
| Nome del set di dati| `input`, `output`, `intermediate result set`|
| Nome della colonna del set di dati|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Completamento del nome

A parte il completamento automatico delle parole chiave, gli strumenti di analisi di flusso per VS Code leggono l'elenco dei nomi di input e output del processo, nonché i nomi delle colonne nelle origini dati quando sono configurati. L'estensione memorizza queste informazioni per fornire funzionalità di completamento del nome utili per l'immissione di istruzioni con poche sequenze di tasti:

Durante la codifica, non è necessario lasciare l'editor per eseguire ricerche in nomi di input del processo, nome di output e nomi di colonna. È possibile gestire il contesto, trovare le informazioni necessarie, inserire elementi direttamente nel codice e fare in modo che IntelliSense completi la digitazione.

Si noti che è necessario configurare l'input locale o l'input Live e salvare il file di configurazione per poter usare il completamento del nome.

![completamento del nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informazioni sul parametro

L'opzione **informazioni sul parametro** di IntelliSense consente di aprire un elenco di parametri che fornisce informazioni sul numero, i nomi e i tipi dei parametri richiesti da una funzione. Il parametro in grassetto indica il parametro successivo richiesto durante la digitazione di una funzione.

L'elenco dei parametri è visualizzato anche per le funzioni nidificate. Se si digita una funzione come parametro per un'altra funzione, nell'elenco dei parametri verranno visualizzati i parametri per la funzione interna. Quando l'elenco dei parametri della funzione interna è completo, verranno quindi visualizzati i parametri della funzione esterna.

![informazioni sul parametro](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informazioni rapide

Come fornito dal servizio di linguaggio, è possibile visualizzare le **informazioni rapide** per ogni identificatore nel codice. Alcuni esempi di identificatori sono input, output, un set di risultati intermedio o una funzione. Quando si sposta il puntatore del mouse su un identificatore, la relativa dichiarazione viene visualizzata in una finestra popup. Vengono visualizzate le proprietà e gli schemi di dati per gli input, se configurati e i set di dati intermedi.

![informazioni rapide](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Risolvere i problemi di IntelliSense

Questo problema è causato dalla mancanza di configurazione di input che fornisce dati. È possibile verificare se un input [locale](visual-studio-code-local-run.md#define-a-local-input) o un [input Live](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) è stato configurato correttamente.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-vs-code.md)
* [Eseguire test locali delle query di Analisi di flusso con dati di esempio con Visual Studio Code](visual-studio-code-local-run.md)
* [Testare le query di analisi di flusso in locale rispetto all'input del flusso Live usando Visual Studio Code](visual-studio-code-local-run-live-input.md)
