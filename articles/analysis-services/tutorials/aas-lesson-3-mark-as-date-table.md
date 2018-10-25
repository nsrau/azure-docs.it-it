---
title: 'Esercitazione su Azure Analysis Services - Lezione 3: Contrassegnare come tabella data | Microsoft Docs'
description: Descrive come contrassegnare una tabella data nel progetto per l'esercitazione su Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9cbbf8c5ea05915293c785028bdd0a47ba081036
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426023"
---
# <a name="mark-as-date-table"></a>Contrassegna come tabella data

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
  
