---
title: 'Esercitazione su Azure Analysis Services - Lezione 13: Distribuire | Microsoft Docs'
description: Descrive come distribuire il progetto per l&quot;esercitazione in Azure Analysis Services.
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
ms.openlocfilehash: ba601ffa74582cf2f3cee660095884566f73eba9
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-13-deploy"></a>Lezione 13: Distribuire

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione verranno configurate le proprietà per la distribuzione, ovvero verranno specificati il server di Analysis Services in Azure o il server di Analysis Services di SQL Server vNext e un nome per il modello. Verrà quindi descritta la procedura per distribuire il modello in tale istanza. Dopo aver distribuito il modello, gli utenti possono connettersi a esso tramite un'applicazione client per la creazione di report. Per altre informazioni, vedere [Distribuire in Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Tempo previsto per il completamento della lezione: **5 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 12: Analizzare in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

**Importante:** se il database di esempio AdventureWorksDW2014 è stato installato in un'istanza di SQL Server locale e si distribuisce il modello in un server Azure Analysis Services, è richiesto un [gateway dati locale](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Distribuire il modello  
  
#### <a name="to-configure-deployment-properties"></a>Per configurare le proprietà di distribuzione  

  
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Proprietà**.  
  
2.  Nella finestra di dialogo **Pagine delle proprietà di AW Internet Sales**, in **Server di distribuzione**, immettere il nome di un server Analysis Services in Azure o locale nella proprietà **Server**.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
 
    > [!IMPORTANT]  
    > È necessario disporre delle autorizzazioni di amministratore nell'istanza remota di Analysis Services per eseguire la distribuzione.  
  
3.  Nella proprietà **Database** digitare **Adventure Works Internet Sales**.  
  
4.  Nella proprietà **Nome modello** digitare **Adventure Works Internet Sales Model**.  
  
5.  Verificare le selezioni e quindi fare clic su **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Per distribuire il modello Adventure Works Internet Sales
  
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Compila**.  

2.  Fare clic con il pulsante destro del mouse sul progetto **AW Internet Sales** e quindi scegliere **Distribuisci**.

    Durante la distribuzione in Azure Analysis Services potrebbe essere richiesto di immettere le proprie credenziali. Immettere nome e password dell'account dell'organizzazione, ad esempio nancy@adventureworks.com. Questo account deve essere incluso nel gruppo Admins nell'istanza del server.
  
    Verrà visualizzata la finestra di dialogo Distribuisci con indicazione dello stato della distribuzione dei metadati e di ogni tabella inclusa nel modello.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Dopo aver completato correttamente la distribuzione, procedere e fare clic su **Chiudi**.  
  
## <a name="conclusion"></a>Conclusione  
Congratulazioni. Sono state completate le attività di creazione e distribuzione del primo modello tabulare di Analysis Services. In questa esercitazione sono state descritte le procedure per eseguire in modo guidato la maggior parte delle attività più comuni per la creazione di un modello tabulare. Dopo aver distribuito il modello Adventure Works Internet Sales, è possibile usare SQL Server Management Studio per gestire il modello, creare script di elaborazione e un piano di backup. Ora gli utenti possono anche connettersi al modello usando un'applicazione client per la creazione di report, come Microsoft Excel o Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Passaggi successivi
*  [Lezione supplementare: Sicurezza dinamica](../tutorials/aas-supplemental-lesson-dynamic-security.md)

*  [Lezione supplementare: Righe di dettaglio](../tutorials/aas-supplemental-lesson-detail-rows.md)

*  [Lezione supplementare: Gerarchie incomplete](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)

