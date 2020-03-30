---
title: Opzioni di conversione di Progettazione visualizzazione monitoraggio di Azure e cartelle di lavoroAzure Monitor view designer to workbooks conversion options
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658711"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Opzioni di conversione di Progettazione visualizzazione monitoraggio di Azure e cartelle di lavoroAzure Monitor view designer to workbooks conversion options
[Progettazione viste](view-designer.md) è una funzionalità di Monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics, con grafici, elenchi e sequenze temporali. Essi vengono gradualmente eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo vengono confrontati i concetti fondamentali tra i due e le opzioni per la conversione di visualizzazioni in cartelle di lavoro.

## <a name="basic-workbook-designs"></a>Progettazione di cartelle di lavoro di base

Progettazione viste ha uno stile statico fisso di rappresentazione, mentre le cartelle di lavoro consentono la libertà di includere e modificare la modalità di rappresentazione dei dati. Le immagini seguenti illustrano due esempi di come è possibile disporre le cartelle di lavoro durante la conversione delle visualizzazioni.

[Cartella di lavoro verticale](view-designer-conversion-examples.md#vertical)
![Verticale](media/view-designer-conversion-options/view-designer-vertical.png)

[Cartella di lavoro](view-designer-conversion-examples.md#tabbed)
![a](media/view-designer-conversion-options/distribution-tab.png)
![schede Scheda di distribuzione dei tipi di dati Tipi di dati nella scheda Tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversione dei riquadri
Progettazione viste Usa la funzionalità di riquadro Panoramica per rappresentare e riepilogare lo stato complessivo. Questi sono rappresentati in sette riquadri, che vanno dai numeri ai grafici. Nelle cartelle di lavoro gli utenti possono creare visualizzazioni simili e aggiungerle in modo che siano simili allo stile originale dei riquadri di panoramica. 

![Gallery](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Visualizzare la conversione del dashboard
I riquadri della finestra di progettazione della visualizzazione sono in genere costituiti da due sezioni, una visualizzazione e un elenco che corrisponde ai dati della visualizzazione, ad esempio il riquadro **Elenco & ciambella.**

![Grafico ad anello](media/view-designer-conversion-options/donut-example.png)

Con le cartelle di lavoro, permettiamo all'utente di scegliere di eseguire una query su una o entrambe le sezioni della visualizzazione. La formulazione di query nelle cartelle di lavoro è un semplice processo in due passaggi. In primo luogo, i dati vengono generati dalla query e, in secondo luogo, i dati vengono visualizzati come visualizzazione.  Un esempio di come questa visualizzazione verrebbe ricreata nelle cartelle di lavoro è il seguente:An example of how this view would be recreated in workbooks is as follows:

![Conversione](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Passaggi successivi
- [Accesso alle cartelle di lavoro & alle autorizzazioni](view-designer-conversion-access.md)