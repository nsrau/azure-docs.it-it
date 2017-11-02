---
title: 'Esercitazione su Azure Analysis Services - Lezione 8: Creare prospettive | Microsoft Docs'
description: Descrive come creare prospettive nel progetto per l'esercitazione su Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a8bb8748d4fe39d664055549564d08524cdc80f3
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-8-create-perspectives"></a>Lezione 8: Creare prospettive

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione verrà creata una prospettiva Internet Sales. Una prospettiva definisce un subset visualizzabile di un modello che offre punti di vista mirati, specifici di un'azienda o specifici di un'applicazione. Quando un utente si connette a un modello usando una prospettiva, vengono visualizzati solo gli oggetti del modello (tabelle, colonne, misure, gerarchie e indicatori KPI) come campi definiti in tale prospettiva. Per altre informazioni, vedere [Perspectives](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular) (Prospettive).
  
La prospettiva Internet Sales che viene creata in questa lezione esclude l'oggetto tabella DimCustomer. Quando si crea una prospettiva che esclude determinati oggetti dalla visualizzazione, l'oggetto esiste ancora nel modello, ma non è visibile in un elenco di campi del client per la creazione di report. Le colonne e le misure calcolate, indipendentemente dal fatto che siano incluse o meno in una prospettiva, possono comunque essere calcolate in base ai dati degli oggetti esclusi.  
  
Lo scopo di questa lezione è descrivere come creare prospettive e acquisire familiarità con gli strumenti per la creazione di modelli tabulari. Se successivamente si espande questo modello per includere ulteriori tabelle, è possibile creare altre prospettive per definire punti di vista diversi del modello, ad esempio, inventario e vendite.  
  
Tempo previsto per il completamento della lezione: **cinque minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 7: Creare indicatori di prestazioni chiave](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Creare prospettive  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Per creare una prospettiva Internet Sales  
  
1.  Fare clic sul menu **Modello** > **Prospettive** > **Crea e gestisci**.  
  
2.  Nella finestra di dialogo **Prospettive** fare clic su **Nuova prospettiva**.  
  
3.  Fare doppio clic sull'intestazione di colonna **Nuova prospettiva** e quindi rinominarla **Internet Sales**.  
  
4.  Selezionare tutte le tabelle *tranne* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    In una lezione successiva si userà la funzionalità Analizza in Excel per testare questa prospettiva. L'elenco dei campi della tabella pivot di Excel include tutte le tabelle tranne DimCustomer.  

## <a name="whats-next"></a>Passaggi successivi
[Lezione 9: Creare gerarchie](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  
