---
title: Come creare un ticket di supporto per SQL Data Warehouse | Documentazione Microsoft
description: Come creare un ticket di supporto per SQL Data Warehouse di Azure.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.contentlocale: it-it
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Come creare un ticket di supporto per SQL Data Warehouse
In caso di problemi con SQL Data Warehouse, creare un ticket di supporto per ottenere assistenza dal team tecnico.

> [!NOTE] 
> In data 20/12/2016, il controllo dell'integrità delle risorse nel portale di Azure non è accurato. Microsoft sta lavorando attivamente per risolvere il problema. 


## <a name="create-a-support-ticket"></a>Creare un ticket di supporto
1. Aprire il [portale di Azure][Azure portal].
2. Nella schermata iniziale fare clic sul riquadro **Guida e supporto tecnico** .
   
    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. Nel pannello Guida e supporto tecnico fare clic su **Crea un ticket di supporto**.
   
    ![Nuova richiesta di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Selezionare il **tipo di richiesta**.
   
    ![tipo di richiesta](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una **Quota DTU** pari a 45.000. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota creando un ticket di supporto e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze in termini di DTU, moltiplicare le [DWU][DWU] totali necessarie per 7,5. Se, ad esempio, si vogliono ospitare due DW6000 in una istanza di SQL Server, è necessario richiedere una quota di DTU pari a 90.000.  È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. 
   > 
   > 
5. Selezionare la **sottoscrizione** che ospita il database con il problema segnalato.
   
    ![sottoscrizione](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Selezionare **SQL Data Warehouse** come risorsa.
   
    ![Risorsa](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Selezionare il proprio [piano di supporto di Azure][Azure support plan].
   
   * **fatturazione, quota e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto **in garanzia** viene fornito tramite il supporto tecnico [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] o [Premier][Premier]. I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * Servizi di **consulenza** e **mentoring per sviluppatori** sono disponibili solo per i livelli di supporto tecnico [Professional Direct][Professional Direct] e [Premier][Premier]. 
     
     Se si ha un piano di supporto tecnico Premier, è anche possibile segnalare problemi relativi a SQL Data Warehouse nel [portale Microsoft Premier online][Microsoft Premier online portal].  Per altre informazioni sui vari piani di supporto, ad esempio su ambito, tempi di risposta, prezzi e così via, vedere la pagina relativa ai [piani di supporto di Azure][Azure support plan].  Per domande frequenti sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure][Azure support FAQs].  
     
     ![Piano di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Selezionare **Tipo di problema** e **Categoria**. In questo esempio è stato scelto "Strumenti" come tipo di problema e "Strumenti client" come categoria. 
   
    ![Categoria del tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Descrivere il problema e scegliere il livello di impatto aziendale.
   
    ![Descrizione del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Le **informazioni di contatto** per il ticket di supporto saranno precompilate. Aggiornare le informazioni, se necessario.
    
    ![Informazioni di contatto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Fare clic su **Crea** per inviare la richiesta di supporto.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto
Dopo avere inviato la richiesta di supporto, si verrà contattati dal team di supporto di Azure. Per controllare i dettagli e lo stato della richiesta, fare clic su **Gestisci richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Altre risorse
È anche possibile connettersi alla community di SQL Data Warehouse in [Stack Overflow][Stack Overflow] o nel [forum MSDN su Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

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


