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
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692732"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Come creare un ticket di supporto per SQL Data Warehouse
In caso di problemi con SQL Data Warehouse, creare un ticket di supporto per ottenere assistenza dal team tecnico.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto
1. Aprire il [portale di Azure][Azure portal].
2. Nella schermata iniziale fare clic sulla scheda **Guida e supporto**.
   
    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Nel pannello Guida e supporto fare clic su **Nuova richiesta di supporto** e compilare il pannello **Informazioni di base**.

   Selezionare il proprio [piano di supporto di Azure][Azure support plan].
   
   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto per la **correzione delle interruzioni** viene fornito tramite il supporto tecnico [Developer][Developer], [standard][Standard], [Professional Direct][Professional Direct]o [Premier][Premier] . I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * I servizi di **mentoring** e di **consulenza** per sviluppatori sono disponibili ai livelli di supporto [Professional Direct][Professional Direct] e [Premier][Premier] . 
     
     Se si dispone di un piano di supporto tecnico Premier, è anche possibile segnalare SQL Data Warehouse problemi correlati nel [portale di Microsoft Premier Online][Microsoft Premier online portal].  Vedere i [piani di supporto di Azure][Azure support plan] per altre informazioni sui vari piani di supporto, inclusi ambito, tempi di risposta, prezzi e così via.  Per domande frequenti sul supporto tecnico di Azure, vedere [domande frequenti sul supporto di Azure][Azure support FAQs].  
        
     ![Pannello Informazioni di base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Pannello Informazioni di base 1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Compilare il pannello **Problema**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una **quota DTU** pari a 45.000. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota creando un ticket di supporto e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze di DTU, moltiplicare 7,5 per il totale [DWU][DWU] necessario. Se, ad esempio, si vogliono ospitare due DW6000 in una istanza di SQL Server, è necessario richiedere una quota di DTU pari a 90.000.  È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. 
   > 
   > 
   
5. Inserire le **informazioni di contatto**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Fare clic su **Crea** per inviare la richiesta di supporto.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto
Dopo avere inviato la richiesta di supporto, si verrà contattati dal team di supporto di Azure. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Altre risorse:
Inoltre, è possibile connettersi con la community di SQL Data Warehouse in [stack overflow][Stack Overflow] o nel [Forum di Azure SQL data warehouse MSDN][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

