---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66120591"
---
Ora che sono presenti dati in Power BI Desktop, è possibile creare report basati su tali dati. Si creerà un report semplice con un istogramma che mostra i danni alle colture per stato.

1. Sul lato sinistro della finestra principale di Power BI selezionare la visualizzazione report.

    ![Visualizzazione report](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. Nel riquadro**VISUALIZZAZIONI** selezionare l'istogramma a colonne di cluster.

    ![Aggiungere un istogramma](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    Un grafico vuoto viene aggiunto al pannello Canvas.

    ![Grafico vuoto](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. Nell'elenco **CAMPI** selezionare **DamageCrops** e **State**.

    ![Selezionare i campi](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Si ha ora un grafico che mostra i danni alle colture per le prime 1000 righe della tabella.

    ![Danni alle colture per stato](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Salvare il report.