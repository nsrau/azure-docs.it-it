---
title: 'Esercitazione su Azure Analysis Services - Lezione 13: Distribuire | Microsoft Docs'
description: Descrive come distribuire il progetto per l'esercitazione in Azure Analysis Services.
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
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 9594582419c716a37a1010e2410e7b2b199df04c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-13-deploy"></a>Lezione 13: Distribuire

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione vengono configurate le proprietà della distribuzione, specificando un server di Azure Analysis Services come destinazione per la distribuzione e un nome per il modello. Verrà quindi descritta la procedura per distribuire il modello in tale istanza. Dopo aver distribuito il modello, gli utenti possono connettersi a esso tramite un'applicazione client per la creazione di report. Per altre informazioni, vedere [Distribuire in Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Tempo previsto per il completamento della lezione: **5 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 12: Analizzare in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> È necessario avere [autorizzazioni di amministratore](../analysis-services-server-admins.md) per l'istanza remota del server Analysis Services per eseguire la distribuzione.  

> [!IMPORTANT]  
> Se il database di esempio AdventureWorksDW2014 è stato installato in un'istanza di SQL Server locale e si distribuisce il modello in un server Azure Analysis Services, è richiesto un [gateway dati locale](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Distribuire il modello  
  
#### <a name="to-configure-deployment-properties"></a>Per configurare le proprietà di distribuzione  

  
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Proprietà**.  
  
2.  Nella finestra di dialogo **Pagine delle proprietà di AW Internet Sales** in **Server di distribuzione** nella proprietà **Server** immettere il server completo.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Nella proprietà **Database** digitare **Adventure Works Internet Sales**.  
  
4.  Nella proprietà **Nome modello** digitare **Adventure Works Internet Sales Model**.  
  
5.  Verificare le selezioni e quindi fare clic su **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Per distribuire il modello Adventure Works Internet Sales
  
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Compila**.  

2.  Fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Distribuisci**.

    Durante la distribuzione in Azure Analysis Services potrebbe essere richiesto di immettere le proprie credenziali. Immettere nome e password dell'account dell'organizzazione, ad esempio nancy@adventureworks.com. Questo account deve essere incluso nel gruppo Admins del server.
  
    Verrà visualizzata la finestra di dialogo Distribuisci con indicazione dello stato della distribuzione dei metadati e di ogni tabella inclusa nel modello.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Dopo aver completato correttamente la distribuzione, procedere e fare clic su **Chiudi**.  
  
## <a name="conclusion"></a>Conclusione  
Congratulazioni. Sono state completate le attività di creazione e distribuzione del primo modello tabulare di Analysis Services. In questa esercitazione sono state descritte le procedure per eseguire in modo guidato la maggior parte delle attività più comuni per la creazione di un modello tabulare. Dopo aver distribuito il modello Adventure Works Internet Sales, è possibile usare SQL Server Management Studio per gestire il modello, creare script di elaborazione e un piano di backup. Ora gli utenti possono anche connettersi al modello usando un'applicazione client per la creazione di report, come Microsoft Excel o Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Passaggi successivi
[Connettersi con Power BI Desktop](../analysis-services-connect-pbi.md)   
[Lezione supplementare: Sicurezza dinamica](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lezione supplementare: Righe di dettaglio](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Lezione supplementare: Gerarchie incomplete](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
