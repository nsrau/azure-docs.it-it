---
title: 'Esercitazione su Azure Analysis Services - Lezione 3: Contrassegnare come tabella data | Microsoft Docs'
description: Descrive come contrassegnare una tabella data nel progetto per l&quot;esercitazione su Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 80c15b2115afca7342771d3f53d1742fc347ae1c
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-3-mark-as-date-table"></a>Lezione 3: Contrassegnare come tabella data

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nella lezione 2: Ottenere i dati è stata importata una tabella delle dimensioni denominata DimDate. Anche se nel modello questa tabella è denominata DimDate, è anche nota come *tabella data*, perché contiene dati di data e ora.  
  
Quando si usano funzioni di Business Intelligence per le gerarchie temporali DAX nei calcoli, come avverrà per la creazione delle misure più avanti, è necessario specificare le proprietà della tabella data, che includono una *tabella data* e una *colonna data* che funge da identificatore univoco in tale tabella.
  
In questa lezione la tabella DimDate verrà contrassegnata come *tabella data* e la colonna Date (nella tabella Date) come *colonna data* (identificatore univoco).  

Prima di contrassegnare la tabella data e la colonna data, è il momento di eseguire alcuni piccoli aggiustamenti per rendere il modello più facile da comprendere. Si noti che la tabella DimDate contiene una colonna denominata **FullDateAlternateKey** che include una riga per ogni giorno dell'anno di calendario incluso nella tabella. Questa colonna verrà usata molto nelle formule per le misure e nei report, ma FullDateAlternateKey non è in realtà un buon identificatore per questa colonna. La colonna verrà rinominata **Date** per facilitarne l'identificazione e l'uso nelle formule. Quando possibile, è consigliabile rinominare gli oggetti, come le tabelle e le colonne, in modo che siano più facilmente identificabili in SSDT e in applicazioni client per la creazione di report, come Power BI ed Excel. 
  
Tempo previsto per il completamento della lezione: **3 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 2: Ottenere i dati](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Per rinominare la colonna FullDateAlternateKey

1.  Nella finestra di progettazione dei modelli fare clic sula tabella **DimDate**.

2.  Fare doppio clic sull'intestazione della colonna **FullDateAlternateKey** e quindi rinominarla **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Per impostare Contrassegna come tabella data  
  
1.  Selezionare la colonna **Date** e quindi nella finestra **Proprietà**, in **Tipo di dati**, assicurarsi che sia selezionato il tipo **Data**.  
  
2.  Fare clic sul menu **Tabella**, quindi su **Data** e infine su **Contrassegna come tabella data**.  
  
3.  Nella finestra di dialogo **Contrassegna come tabella data** selezionare la colonna **Date** come identificatore univoco nella casella di riepilogo **Data**. In genere, sarà selezionata per impostazione predefinita. Fare clic su **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Passaggi successivi
[Lezione 4: Creare relazioni](../tutorials/aas-lesson-4-create-relationships.md).
  

