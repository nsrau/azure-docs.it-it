---
title: Visualizzazioni di testo della cartella di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni di testo della cartella di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664378"
---
# <a name="text-visualizations"></a>Visualizzazioni di testo

Le cartelle di lavoro consentono agli autori di includere blocchi di testo nelle cartelle di lavoro di. Il testo può essere un'analisi umana dei dati di telemetria, informazioni che consentono agli utenti di interpretare i dati, le intestazioni di sezione e così via.

[![Screenshot della tabella di testo Apdex](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Il testo viene aggiunto tramite un controllo Markdown, che fornisce il controllo completo della formattazione. Sono inclusi stili di intestazione e caratteri diversi, collegamenti ipertestuali, tabelle e così via.

Modalità di modifica:

![Screenshot di un passaggio di testo in modalità di modifica.](./media/workbooks-text-visualizations/text-edit-mode.png)

Modalità anteprima:

![Screenshot di un passaggio di testo in modalità di modifica nella scheda Anteprima.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Aggiungere un controllo di testo

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce **Modifica** sulla barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi testo** per aggiungere un controllo di testo alla cartella di lavoro.
3. Aggiungere Markdown nel campo Editor.
4. Usare l'opzione *stile testo* per passare da un Markdown normale a un Markdown e viceversa con lo stile di informazioni standard/avviso/successo/errore di portale di Azure.
5. Usare la scheda **Anteprima** per visualizzare l'aspetto del contenuto. Durante la modifica, l'anteprima mostrerà il contenuto all'interno di un'area della barra di scorrimento per limitarne le dimensioni. Tuttavia, in fase di esecuzione il contenuto di Markdown si espanderà per riempire lo spazio necessario, senza barre di scorrimento.
6. Selezionare il pulsante **modifica completato** per completare la modifica del passaggio.

> [!TIP]
> Usare questo [foglio](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) informativo di Markdown per ottenere informazioni sulle diverse opzioni di formattazione.

## <a name="text-styles"></a>Stili del testo

Per il passaggio di testo sono disponibili gli stili di testo seguenti:

| Stile     | Spiegazione                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Non viene applicata alcuna formattazione aggiuntiva.                                                      |
| `info`    | Stile "info" del portale, con un'  `ℹ` icona o un'icona simile e uno sfondo generalmente blu.      |
| `error`   | Lo stile "errore" del portale, con un' `❌` icona o un'icona simile e generalmente uno sfondo rosso.     |
| `success` | Lo stile "operazione riuscita" del portale, con un' `✔` icona o un'icona simile e in generale lo sfondo verde.  |
| `upsell`  | Lo stile "upsell" del portale, con un' `🚀` icona o un'icona simile e in generale sfondo viola. |
| `warning` | Lo stile "avviso" del portale, con un' `⚠` icona o un'icona simile e in generale lo sfondo blu.   |

Anziché selezionare uno stile specifico, è anche possibile scegliere un parametro di testo come origine dello stile. Il valore del parametro deve essere uno dei valori di testo precedenti. L'assenza di un valore o di qualsiasi valore non riconosciuto verrà considerato come `plain` uno stile.

Esempio di stile di informazioni:

![Screenshot dell'aspetto dello stile delle informazioni.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Esempio di stile di avviso:

![Screenshot dello stile di avviso.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare un [grafico nelle cartelle di lavoro](workbooks-chart-visualizations.md)di.
* Informazioni su come creare una [griglia nelle cartelle di lavoro di](workbooks-grid-visualizations.md).
