---
title: Gestire i budget Cloudyn in Azure | Microsoft Docs
description: Questo articolo consente di creare rapidamente i budget e di iniziare a gestirli in Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 44062670283a15f678da45e13a831e73a7738c39
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075489"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Gestire i budget di Azure con Cloudyn

L'impostazione dei budget e gli avvisi basati sui budget aiutano a migliorare la rendicontazione e la governance nel cloud. Questo articolo consente di creare rapidamente i budget e di iniziare a gestirli in Cloudyn.

Quando si dispone di un conto aziendale o MSP, è possibile usare la struttura delle entità costo gerarchica per assegnare quote di budget mensili a differenti Business Unit, reparti o qualsiasi altra entità costo. Se si dispone dell'account Premium, è possibile usare la funzionalità di gestione del budget, che viene applicata all'intera spesa nel cloud. Tutti i budget vengono assegnati manualmente.

In base ai budget assegnati, è possibile impostare avvisi di soglia basati sulla percentuale del budget che viene consumata e definire la gravità di ogni soglia.

I report dei budget mostrano il budget assegnato. Gli utenti possono visualizzare quando la spesa è sopra, sotto o pari al valore nominale del consumo nel corso del tempo. Quando si seleziona **Mostra/Nascondi campi** nella parte superiore di un report di budget, è possibile visualizzare il costo, il preventivo, il costo accumulato o il budget totale.

Gestione costi di Azure offre funzionalità simili a quelle di Cloudyn. Gestione costi di Azure è una soluzione di gestione costi nativa di Azure. È un modo per analizzare i costi, creare e gestire budget, esportare i dati, esaminare e implementare gli elementi consigliati per l'ottimizzazione e di conseguenza risparmiare. Per altre informazioni sui budget in Gestione costi, vedere [Creare e gestire i budget](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Creare i budget

Quando si crea un budget, lo si imposta per l'anno fiscale e lo sia applica a un'entità specifica.

Per creare un budget e assegnarlo a un'entità:

1. Passare a **Costi** &gt; **Gestione costi** &gt; **Budget**.
2. Nella pagina di gestione del budget, in **Entità** selezionare l'entità per cui si vuole creare il budget.
3. Nell'anno del budget selezionare l'anno per cui si vuole creare il budget.
4. Impostare un valore di budget per ogni mese. Al termine, fare clic su **Salva**.
In questo esempio, il budget mensile per giugno 2018 è impostato su USD 135.000. Il budget totale per l'anno è di USD 1.615.000,00.
![Creare una pagina di budget in cui è impostato un budget per ogni mese](./media/manage-budgets/set-budget.png)


Per importare un file per il budget annuo:

1. In **Azioni** selezionare **Esporta** per scaricare un modello CSV vuoto da usare come base per il budget.
2. Compilare il file CSV con i movimenti del budget e salvarlo in locale.
3. In **Azioni** selezionare **Importa**.
4. Selezionare il file salvato e quindi fare clic su **OK**.

Per esportare il budget completato come file CSV, in **Azioni** selezionare **Esporta** per scaricare il file.

## <a name="view-budget-in-reports"></a>Visualizzare il budget nei report

Dopo che è stato completato, il budget viene mostrato nella maggior parte dei report sui costi in **Costi** &gt; **Analisi dei costi** e nel report di confronto tra costo e budget nel tempo. È inoltre possibile pianificare i report basati sulle soglie di budget usando **Azioni**.

Ecco un esempio del report Analisi dei costi. Questo report mostra il budget e il costo totali in base al carico di lavoro e ai tipi di utilizzo partendo dall'inizio dell'anno.

![Esempio di report Analisi dei costi con budget](./media/manage-budgets/cost-analysis-budget-example.png)

In questo esempio si presuppone che la data corrente sia il 22 giugno. Il costo per giugno 2018 è di USD 71.611,28 in confronto al budget mensile di USD 135.000. Il costo è molto inferiore al budget mensile perché ci sono ancora otto giorni di spesa prima che termini il mese.

Un altro modo per visualizzare il report consiste nell'esaminare il costo accumulato rispetto al budget. Per vedere i costi accumulati, in **Mostra/Nascondi campi** selezionare **Costo accumulato** e **Budget totale**. Ecco un esempio che mostra il costo accumulato dall'inizio dell'anno.

![Esempio di costo accumulato e di budget totale visualizzato nel report Costi e budget nel tempo](./media/manage-budgets/accumulated-budget.png)

È possibile che a un certo punto il costo accumulato superi il budget. Questa situazione è più facile da vedere se si imposta la visualizzazione del grafico sul tipo _a linee_.

![Budget illustrato in un grafico a linee nel report Cost by Months (Costi per mese)](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Creare avvisi del budget per un filtro

Nell'esempio precedente si nota che il costo accumulato si è avvicinato al valore del budget. È possibile creare avvisi di budget automatici che notifichino il momento in cui la spesa si avvicina o supera il valore del budget. L'avviso è fondamentalmente un report programmato con una soglia. Le metriche di soglia degli avvisi di budget includono:

- Costo rimanente rispetto al budget: per specificare una soglia di valore di valuta
- Percentuale dei costi rispetto al budget: per specificare una soglia di valore percentuale

Ecco un esempio.

Nel report di confronto tra costo e budget nel tempo fare clic su **Azioni** e quindi selezionare **Pianifica report**. Nella scheda Soglia selezionare una metrica di soglia, ad esempio **Cost percentage vs budget** (Percentuale costo rispetto a budget). Selezionare un tipo di avviso e immettere un valore di percentuale del budget. Se si desidera ricevere la notifica una sola volta, selezionare **Number of consecutive alerts** (Numero di avvisi consecutivi) e quindi digitare _1_. Fare clic su **Save**.

![Creazione di un avviso del budget nella finestra Save or Schedule this report (Salva o pianifica questo report)](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Passaggi successivi

- Se non è stata ancora completata la prima esercitazione di Cloudyn, leggere le informazioni riportate in [Esaminare l'utilizzo e i costi](tutorial-review-usage.md).
- Altre informazioni sui [report disponibili in Cloudyn](use-reports.md).
