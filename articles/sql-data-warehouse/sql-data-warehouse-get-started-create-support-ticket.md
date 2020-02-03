---
title: Come creare un ticket di supporto
description: Come creare un ticket di supporto per SQL Data Warehouse di Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717832"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Come creare un ticket di supporto per SQL Data Warehouse
Se si verificano problemi con la SQL Data Warehouse, creare un ticket di supporto in modo che il team di supporto tecnico possa fornire assistenza.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto
1. Aprire il [portale di Azure](https://portal.azure.com/).
2. Nella schermata iniziale fare clic sulla scheda **Guida e supporto**.
   
    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Nel pannello Guida e supporto fare clic su **Nuova richiesta di supporto** e compilare il pannello **Informazioni di base**.

   Selezionare il proprio [piano di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto per la **correzione delle interruzioni** viene fornito tramite il supporto tecnico [Developer](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)o [Premier](https://azure.microsoft.com/support/plans/premier/) . I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * I servizi di **mentoring** e di **consulenza** per sviluppatori sono disponibili ai livelli di supporto [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Se si dispone di un piano di supporto tecnico Premier, è anche possibile segnalare SQL Data Warehouse problemi correlati nel [portale di Microsoft Premier Online](https://premier.microsoft.com/). Vedere i [piani di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) per altre informazioni sui vari piani di supporto, inclusi ambito, tempi di risposta, prezzi e così via.  Per domande frequenti sul supporto tecnico di Azure, vedere [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).  
        
     ![Pannello Informazioni di base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Pannello Informazioni di base 1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Compilare il pannello **Problema**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una **quota DTU** pari a 45.000. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota creando un ticket di supporto e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze di DTU, moltiplicare 7,5 per il totale [DWU](sql-data-warehouse-overview-what-is.md) necessario. Se, ad esempio, si vogliono ospitare due DW6000 in una istanza di SQL Server, è necessario richiedere una quota di DTU pari a 90.000.  È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. 
   > 
   > 
   
5. Inserire le **informazioni di contatto**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Fare clic su **Crea** per inviare la richiesta di supporto.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto
Dopo aver inviato la richiesta di supporto, il team di supporto di Azure contatterà l'utente. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Altre risorse
È anche possibile connettersi con la community di SQL Data Warehouse in [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) o tramite il [Forum di Azure SQL data warehouse MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
