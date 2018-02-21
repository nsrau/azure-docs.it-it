---
title: 'Esercitazione su Azure Analysis Services - Lezione 1: Creare un nuovo progetto di modello tabulare | Microsoft Docs'
description: Descrive come creare un nuovo progetto per un'esercitazione su Azure Analysis Services.
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
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: fbe0784ae133a0b9a54c94b4ba3db317c14b3766
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="create-a-tabular-model-project"></a>Creare un progetto di modello tabulare

In questa lezione si usa Visual Studio con SQL Server Data Tools (SSDT) per creare un nuovo progetto di modello tabulare al livello di compatibilità 1400. Dopo aver creato il nuovo progetto, è possibile iniziare ad aggiungere i dati e a lavorare al modello. Questa lezione offre anche una breve introduzione all'ambiente per la creazione dei modelli tabulari in Visual Studio.  
  
Tempo previsto per il completamento della lezione: **10 minuti**  
  
## <a name="prerequisites"></a>prerequisiti  
Questo argomento è la prima lezione dell'esercitazione per la creazione di un modello tabulare. Per completare questa lezione, esistono alcuni prerequisiti che è necessario soddisfare. Per altre informazioni, vedere [Azure Analysis Services: esercitazione su Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Creare un nuovo progetto di modello tabulare  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Per creare un nuovo progetto di modello tabulare  
  
1.  Nel menu **File** in Visual Studio scegliere **Nuovo** > **Progetto**.  
  
2.  Nella finestra di dialogo **Nuovo progetto** espandere **Installati** > **Business Intelligence** > **Analysis Services** e quindi fare clic su **Progetto tabulare di Analysis Services**.  
  
3.  Nella casella **Nome** digitare **AW Internet Sales** e quindi specificare una posizione per i file di progetto.  
  
    Per impostazione predefinita, in **Nome soluzione** è indicato lo stesso nome del progetto, ma è possibile digitare un nome diverso per la soluzione.  
  
4.  Fare clic su **OK**.  
  
5.  Nella finestra di dialogo **Progettazione modelli tabulari** selezionare **Area di lavoro integrata**.  
  
    L'area di lavoro ospita un database del modello tabulare con lo stesso nome del progetto durante la creazione del modello. Area di lavoro integrata significa che Visual Studio usa un'istanza predefinita, eliminando la necessità di installare un'istanza del server Analysis Services separata solo per la creazione del modello.
      
6.  In **Livello di compatibilità** selezionare **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Se la casella di riepilogo Livello di compatibilità non include SQL Server 2017 / Azure Analysis Services (1400), ciò indica che non è disponibile la versione più recente di SQL Server Data Tools. Per ottenere la versione più recente, vedere [Download SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) (Scaricare SQL Server Data Tools (SSDT)).  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>Informazioni sull'ambiente di creazione di modelli tabulari di SSDT  
Dopo aver creato un nuovo progetto di modello tabulare è possibile iniziare a esplorare l'ambiente di Visual Studio per la creazione di modelli tabulari.  
  
Il progetto creato viene aperto in Visual Studio. Sul lato destro in **Esplora modelli tabulari** è disponibile una visualizzazione struttura ad albero degli oggetti nel modello. Le cartelle sono vuote perché non sono stati ancora importati i dati. È possibile fare clic con il pulsante destro del mouse su una cartella di oggetti per eseguire varie azioni, in modo simile alla barra dei menu. Durante l'esecuzione di questa esercitazione si usa Esplora modelli tabulari per spostarsi tra i diversi oggetti nel progetto del modello.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Fare clic sulla scheda **Esplora soluzione**. In questa scheda è possibile visualizzare il file **Model.bim**. Se la finestra di progettazione a sinistra non è visibile (la finestra vuota con la scheda Model.bim), in **Esplora soluzioni** fare doppio clic sul file **Model.bim** nel progetto **AW Internet Sales**. Il file Model.bim contiene i metadati per il progetto del modello. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Fare clic su **Model.bim**. Nella finestra **Proprietà** sono visualizzate le proprietà del modello, la più importante delle quali è la proprietà **Modalità DirectQuery**. Questa proprietà specifica se il modello viene distribuito in modalità In-Memory (Disattivata) o in modalità DirectQuery (Attivata). Per questa esercitazione, il modello viene creato e distribuito in modalità In-Memory.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Quando si crea un progetto di modello, determinate proprietà del modello vengono impostate automaticamente in base alle impostazioni di Modellazione dati che possono essere specificate nella finestra di dialogo **Opzioni** accessibile dal menu **Strumenti**. Le proprietà Backup dei dati, Memorizzazione area di lavoro e Server dell'area di lavoro specificano come e dove viene eseguito il backup, viene mantenuto in memoria e viene creato il database dell'area di lavoro, ovvero il database per la creazione del modello. È possibile modificare queste impostazioni in un secondo momento, se necessario, ma per ora, lasciarle come sono.  

In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **AW Internet Sales** (progetto) e quindi scegliere **Proprietà**. Verrà visualizzata la finestra di dialogo **Pagine delle proprietà di AW Internet Sales**. Alcune di queste proprietà verranno impostate in un secondo momento, durante la distribuzione del modello.  
  
Con l'installazione di SSDT vengono aggiunte varie voci di menu nuove all'ambiente di Visual Studio. Fare clic sul menu **Modello**. Da questo menu è possibile importare dati, aggiornare i dati dell'area di lavoro, esplorare il modello in Excel, creare prospettive e ruoli, selezionare la vista modelli e impostare le opzioni di calcolo. Fare clic sul menu **Tabella**. Da questo menu è possibile creare e gestire relazioni, specificare le impostazioni della tabella data, creare partizioni e modificare le proprietà della tabella. Dal menu **Colonna** è possibile aggiungere ed eliminare colonne in una tabella, bloccare le colonne e specificare l'ordinamento. SSDT aggiunge anche alcuni pulsanti alla barra. Il più utile è quello della funzionalità Somma automatica che consente di creare una misura di aggregazione standard per una colonna selezionata. Con gli altri pulsanti della barra degli strumenti è possibile accedere rapidamente ai comandi e alle funzioni usati di frequente.  
  
Esplorare alcune delle finestre di dialogo e individuare le posizioni delle varie funzionalità specifiche per la creazione di modelli tabulari. Anche se alcune voci di menu o opzioni non sono ancora attive, è così possibile acquisire familiarità con l'ambiente per la creazione di modelli tabulari.  
  

## <a name="whats-next"></a>Passaggi successivi
[Lezione 2: Ottenere i dati](../tutorials/aas-lesson-2-get-data.md).

  
  
  
