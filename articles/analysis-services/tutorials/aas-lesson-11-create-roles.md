---
title: 'Esercitazione su Azure Analysis Services - Lezione 11: Creare ruoli | Microsoft Docs'
description: Descrive come creare ruoli nel progetto per l'esercitazione su Azure Analysis Services.
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
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 085a36edd2a0e80123ac8754b438bceadfa6c0e9
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-11-create-roles"></a>Lezione 11: Creare ruoli

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione verranno creati ruoli. I ruoli consentono di proteggere i dati e gli oggetti del database del modello, limitando l'accesso solo agli utenti membri del ruolo. Ogni ruolo è definito con una sola autorizzazione: Nessuna, Lettura, Lettura ed elaborazione, Elaborazione o Amministratore. I ruoli possono essere definiti durante la creazione dei modelli tramite Gestione ruoli. Dopo avere distribuito un modello, è possibile gestire i ruoli con SQL Server Management Studio (SSMS). Per altre informazioni, vedere [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular) (Ruoli).
  
> [!NOTE]  
> La creazione di ruoli non è necessaria per completare questa esercitazione. Per impostazione predefinita, l'account con cui si esegue l'accesso ha i privilegi di Amministratore per il modello. Per consentire anche ad altri utenti dell'organizzazione di esplorare il modello usando un client per la creazione di report, tuttavia, è necessario creare almeno un ruolo con autorizzazioni Lettura e aggiungere tali utenti come membri.  
  
Vengono creati tre ruoli:  
  
-   **Sales Manager** - Questo ruolo può includere gli utenti dell'organizzazione che si vuole dispongano dell'autorizzazione Lettura per tutti gli oggetti e i dati del modello.  
  
-   **Sales Analyst US** - Questo ruolo può includere gli utenti dell'organizzazione che si vuole possano esplorare solo i dati relativi alle vendite negli Stati Uniti. Per questo ruolo si usa una formula DAX per definire un *filtro di riga*, che consente ai membri di visualizzare solo i dati per gli Stati Uniti.  
  
-   **Administrator** - Questo ruolo può includere gli utenti a cui si vuole concedere l'autorizzazione Amministratore, che consente l'accesso illimitato e concede le autorizzazioni per eseguire attività amministrative sul database del modello.  
  
Dato che gli account utente e di gruppo di Windows nell'organizzazione sono univoci, è possibile aggiungere gli account dell'organizzazione ai membri. Tuttavia, per questa esercitazione, è anche possibile non specificare i membri. È possibile testare l'effetto di ogni ruolo in un secondo momento nella lezione 12: Analizzare in Excel.  
  
Tempo previsto per il completamento della lezione: **15 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 10: Creare partizioni](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Creare ruoli  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Per creare un ruolo utente Sales Manager  
  
1.  In Esplora modelli tabulari fare clic con il pulsante destro del mouse su **Ruoli** > **Ruoli**.  
  
2.  In Gestione ruoli fare clic su **Nuovo**.  
  
3.  Fare clic sul nuovo ruolo e quindi nella colonna **Nome** rinominare il ruolo in **Sales Manager**.  
  
4.  Nella colonna **Autorizzazioni** fare clic nell'elenco a discesa e quindi selezionare l'autorizzazione **Lettura**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Facoltativo: fare clic sulla scheda **Membri** e quindi fare clic su **Aggiungi**. Nella finestra di dialogo **Seleziona utenti o gruppi** immettere gli utenti o i gruppi di Windows dell'organizzazione che si vuole includere nel ruolo.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Per creare un ruolo utente Sales Analyst US  
  
1.  In Gestione ruoli fare clic su **Nuovo**.    
  
2.  Rinominare il ruolo **Sales Analyst US**.  
  
3.  Assegnare a questo ruolo l'autorizzazione **Lettura**.  
  
4.  Fare clic sulla scheda Filtri di riga e quindi, solo per la per tabella **DimGeography**, digitare la formula seguente nella colonna Filtro DAX:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Una formula per il filtro di riga deve restituire un valore booleano (TRUE/FALSE). Con questa formula si specifica che devono essere visibili per l'utente solo le righe con il valore "US" per Country Region Code.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Facoltativo: fare clic sulla scheda **Membri** e quindi fare clic su **Aggiungi**. Nella finestra di dialogo **Seleziona utenti o gruppi** immettere gli utenti o i gruppi di Windows dell'organizzazione che si vuole includere nel ruolo.  
  
#### <a name="to-create-an-administrator-user-role"></a>Per creare un ruolo utente Administrator  
  
1.  Fare clic su **Nuovo**.  
  
2.  Rinominare il ruolo **Administrator**.  
  
3.  Assegnare a questo ruolo l'autorizzazione **Amministratore**.  
  
4.  Facoltativo: fare clic sulla scheda **Membri** e quindi fare clic su **Aggiungi**. Nella finestra di dialogo **Seleziona utenti o gruppi** immettere gli utenti o i gruppi di Windows dell'organizzazione che si vuole includere nel ruolo. 
  
  
## <a name="whats-next"></a>Passaggi successivi
[Lezione 12: Analizzare in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  

