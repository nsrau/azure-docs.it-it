---
title: Presentare un notebook di Jupyter come una presentazione in Azure
description: Come configurare le celle per la modalità presentazione in un notebook di Jupyter e quindi eseguire la presentazione usando l'estensione RISE.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 405fe71676de311ed7e59ea72798ff4fd2db0f62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237272"
---
# <a name="run-a-notebook-slideshow"></a>Eseguire la presentazione di un notebook

Azure Notebooks è preconfigurato con Jupyter/IPython Slideshow Extension (RISE), che consente di presentare un notebook direttamente come una presentazione. In una presentazione, le celle vengono visualizzate in genere una per volta usando una dimensione del carattere appropriata per la presentazione su schermi di grandi dimensioni ed è comunque possibile eseguire il codice anziché usare un computer separato per le demo.

L'immagine seguente mostra la visualizzazione notebook standard, in cui è possibile visualizzare insieme il testo Markdown e le celle di codice:

![Un notebook nella visualizzazione standard](media/slideshow/slideshow-notebook-view.png)

Quando si avvia una presentazione, la prima cella viene ingrandita in base alla finestra del browser, in cui la **X** in alto a sinistra chiude la presentazione, il **?** in basso a sinistra visualizza i tasti di scelta rapida e le frecce in basso a sinistra consentono di passare da una diapositiva all'altra:

![Un notebook in modalità presentazione](media/slideshow/slideshow-slide-view.png)

La preparazione di un notebook per una presentazione prevede due attività principali:

1. Poiché le celle di Markdown vengono visualizzate con caratteri di grandi dimensioni, è possibile che alcuni contenuti non siano visibili nella presentazione. È per questo motivo che, in genere, si limita la quantità di testo nella cella: un'intestazione con quattro/sei righe costituisce in genere la scelta migliore. Se si ha più testo da inserire, suddividere le informazioni in più celle.

2. Configurare il comportamento di ogni cella nella presentazione usando la barra degli strumenti della cella Presentazione. I tipi di cella determinano il comportamento dei pulsanti di navigazione.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomia di una presentazione

Se si prende un notebook casuale e lo si usa per una presentazione, si vedrà che tutte le celle sono posizionate in disordine e che la maggior parte del contenuto è nascosto sotto la finestra del browser. Per creare una presentazione efficace, quindi, è necessario assegnare un tipo di presentazione a ogni cella usando la barra degli strumenti Presentazione della cella:

1. Nel menu **Visualizza** selezionare **Cell Toolbar** > **Slideshow** (Barra degli strumenti cella > Presentazione):

    ![Attivazione della barra degli strumenti della cella Presentazione](media/slideshow/slideshow-view-cell-toolbar.png)

1. Nell'angolo superiore destro di ogni cella del notebook viene visualizzato il menu a discesa **Slide Type** (Tipo di diapositiva):

    ![Barra degli strumenti della cella Presentazione](media/slideshow/slideshow-cell-toolbar.png)

1. Per ogni cella, selezionare uno dei cinque tipi disponibili:

    ![Tipi di presentazione della cella](media/slideshow/slideshow-cell-slide-types.png)

    | Tipo di diapositiva | Comportamento |
    | --- | --- |
    | - (non impostato) | La cella viene visualizzata con la cella precedente, creando un effetto spesso non desiderato in una presentazione. |
    | Diapositiva | La cella è una diapositiva primaria, a cui è possibile accedere usando le frecce DESTRA e SINISTRA del controllo di navigazione. |
    | Diapositiva secondaria | La cella si trova "sotto" una diapositiva primaria a cui è possibile accedere usando la freccia GIÙ del controllo di navigazione. La freccia SU consente di tornare alla diapositiva primaria. Le diapositive secondarie vengono usate per il materiale secondario che è possibile ignorare nel percorso principale di una presentazione ma che, se necessario, è immediatamente disponibile. |
    | Frammento | Il contenuto della cella viene visualizzato nel contesto della diapositiva o della diapositiva secondaria precedente quando si usa la freccia di navigazione GIÙ (viene rimosso un frammento quando si usa la freccia SU). È possibile usare un frammento con una cella di codice per far apparire il codice all'interno di una diapositiva oppure è possibile usare più frammenti per far apparire i punti di elenco uno per volta (vedere l'esempio nella sezione successiva). Poiché i frammenti vengono disposti nella diapositiva corrente, quelli in eccesso non saranno visibili oltre la fine della finestra del browser. |
    | Skip | La cella non viene visualizzata nella presentazione. |
    | Note | La cella contiene le note del relatore, che non vengono visualizzate nella presentazione. |

1. Inizialmente, è consigliabile scegliere l'opzione **Presentazione** per ogni cella. È quindi possibile eseguire la presentazione e apportare le modifiche necessarie.

### <a name="example-fragment-cells-for-bullet-items"></a>Esempio: celle frammento per punti elenco

Per far apparire i punti elenco uno per volta, inserire l'intestazione della diapositiva in una cella di Markdown con il tipo **Diapositiva** e quindi posizionare ogni punto elenco in una cella di Markdown separata con il tipo **Frammento**:

![Esempio di creazione di più celle di Markdown per i punti elenco](media/slideshow/slideshow-fragments.png)

Poiché i frammenti vengono visualizzati con una spaziatura verticale maggiore rispetto alla scelta di inserire tutti i punti elenco in una stessa cella, potrebbe non essere possibile usare un numero altrettanto elevato di punti elenco.

## <a name="run-the-slideshow"></a>Eseguire la presentazione

1. Dopo aver modificato le celle di Markdown, assicurarsi di eseguirne il rendering in formato HTML: in caso contrario verranno visualizzate *come* Markdown nella presentazione.

1. Dopo aver configurato il **tipo di diapositiva** per ogni cella, scegliere la cella con cui inizierà la presentazione e quindi selezionare il pulsante **Enter/Exit RISE Slideshow** (Avvia/Interrompi presentazione RISE) sulla barra degli strumenti principale:

    ![Pulsante Enter/Exit RISE Slideshow (Avvia/Interrompi presentazione RISE) sulla barra degli strumenti principale](media/slideshow/slideshow-start.png)

1. Per spostarsi tra diapositive o frammenti, usare le frecce DESTRA e SINISTRA nel controllo di navigazione. Il testo nel controllo visualizza un numero che rappresenta *slide.sub-slide*.

    ![Controllo di navigazione della presentazione](media/slideshow/slideshow-navigation-control.png)

1. Per spostarsi tra diapositive e diapositive secondarie o tra frammenti, usare le frecce SU e GIÙ, se abilitate:

    ![Controlli di navigazione della presentazione per diapositive secondarie](media/slideshow/slideshow-navigation-control-subslide.png)

1. In una cella di codice usare il pulsante di riproduzione per eseguire il codice; nella diapositiva viene visualizzato l'output seguente:

    ![Pulsante di riproduzione per l'esecuzione di una cella di codice](media/slideshow/slideshow-run-code-cell.png)

    ![Output della cella di codice visualizzato nella presentazione](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > L'output della cella è considerato parte della cella in una presentazione. Se si esegue una cella nella visualizzazione notebook o presentazione, l'output viene visualizzato anche nell'altra visualizzazione. Per cancellare l'output, usare il comando **Cell** > **Current Outputs** > **Clear**(Cella > Output correnti > Cancella) (per la cella corrente) o **Cell** > **All Outputs** > **Clear** (Cella > Tutti gli output > Cancella) (per tutte le celle).

1. Dopo aver eseguito tutte le operazioni necessarie nella presentazione, usare il pulsante **X** per tornare alla visualizzazione notebook.

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare e gestire i progetti](configure-manage-azure-notebooks-projects.md)
- [Procedura: Installare pacchetti da un notebook](install-packages-jupyter-notebook.md)
- [Procedura: Usare i file di dati](work-with-project-data-files.md)
- [Procedura: Accedere alle risorse dati](access-data-resources-jupyter-notebooks.md)
