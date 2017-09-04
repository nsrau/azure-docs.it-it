---
title: 'Esercitazione su Azure Analysis Services - Lezione 3: Contrassegnare come tabella data | Microsoft Docs'
description: Descrive come contrassegnare una tabella data nel progetto per l'esercitazione su Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: c62f2726fef5219155a08b70c61162c914600d1d
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-3-mark-as-date-table"></a>Lezione 3: Contrassegnare come tabella data

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nella lezione 2: Ottenere i dati è stata importata una tabella delle dimensioni denominata DimDate. Anche se nel modello questa tabella è denominata DimDate, è anche nota come *tabella data*, perché contiene dati di data e ora.  
  
Quando si usano funzioni di Business Intelligence per le gerarchie temporali DAX, è necessario specificare alcune proprietà, tra cui una *tabella data* e una *colonna data* che funge da identificatore univoco nella tabella.
  
In questa lezione la tabella DimDate verrà contrassegnata come *tabella data* e la colonna Date (nella tabella Date) come *colonna data* (identificatore univoco).  

Prima di contrassegnare la tabella data e la colonna data, è il momento di eseguire alcuni piccoli aggiustamenti per rendere il modello più facile da comprendere. Nella tabella DimDate è presente un colonna denominata **FullDateAlternateKey**, che contiene una riga per ogni giorno di ogni anno di calendario incluso nella tabella. Questa colonna viene usata molto spesso nelle formule per le misure e nei report, ma FullDateAlternateKey non è in realtà un buon identificatore per questa colonna. La colonna viene rinominata **Date** per facilitarne l'identificazione e l'uso nelle formule. Quando possibile, è consigliabile rinominare gli oggetti, come le tabelle e le colonne, in modo che siano più facilmente identificabili in SSDT e in applicazioni client per la creazione di report, come Power BI ed Excel. 
  
Tempo previsto per il completamento della lezione: **tre minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 2: Ottenere i dati](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Per rinominare la colonna FullDateAlternateKey

1.  Nella finestra di progettazione dei modelli fare clic sula tabella **DimDate**.

2.  Fare doppio clic sull'intestazione della colonna **FullDateAlternateKey** e quindi rinominarla **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Per impostare Contrassegna come tabella data  
  
1.  Selezionare la colonna **Date** e quindi nella finestra **Proprietà**, in **Tipo di dati**, assicurarsi che sia selezionato il tipo **Data**.  
  
2.  Fare clic sul menu **Tabella**, quindi su **Data** e infine su **Contrassegna come tabella data**.  
  
3.  Nella finestra di dialogo **Contrassegna come tabella data** selezionare la colonna **Date** come identificatore univoco nella casella di riepilogo **Data**. In genere, è selezionata per impostazione predefinita. Fare clic su **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Passaggi successivi
[Lezione 4: Creare relazioni](../tutorials/aas-lesson-4-create-relationships.md).
  

