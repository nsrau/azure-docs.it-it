---
title: Come creare un ticket di supporto
description: Come creare un ticket di supporto in Azure sinapsi Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195688"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Come creare un ticket di supporto per Azure sinapsi Analytics
Se si verificano problemi con l'analisi delle sinapsi di Azure, creare un ticket di supporto in modo che il team di supporto tecnico possa fornire assistenza.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto
1. Aprire il [portale di Azure](https://portal.azure.com/).
1. Nella schermata iniziale fare clic sulla scheda **Guida e supporto**.
   
    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Esaminare il [piano di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto per la **correzione delle interruzioni** viene fornito tramite il supporto tecnico [Developer](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)o [Premier](https://azure.microsoft.com/support/plans/premier/) . I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * I servizi di **mentoring** e di **consulenza** per sviluppatori sono disponibili ai livelli di supporto [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Se si dispone di un piano di supporto tecnico Premier, è anche possibile segnalare SQL Data Warehouse problemi correlati nel [portale di Microsoft Premier Online](https://premier.microsoft.com/). Vedere i [piani di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) per altre informazioni sui vari piani di supporto, inclusi ambito, tempi di risposta, prezzi e così via.  Per domande frequenti sul supporto tecnico di Azure, vedere [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
1. Nella pagina **Guida e supporto** selezionare **nuova richiesta di supporto**. Selezionare un tipo di problema dal menu a discesa. Quindi continuare a compilare le informazioni nella scheda **nozioni di base** . Immettere un **Riepilogo** del problema e quindi selezionare un **tipo di problema** dal menu e selezionare Salva.

    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una **quota DTU** pari a 45.000. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota creando un ticket di supporto e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze di DTU, moltiplicare 7,5 per il totale [DWU](sql-data-warehouse-overview-what-is.md) necessario. Se, ad esempio, si vogliono ospitare due DW6000 in una istanza di SQL Server, è necessario richiedere una quota di DTU pari a 90.000.  È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. 
   > 

1. È possibile che vengano visualizzate soluzioni che consentono di risolvere il problema. Se le soluzioni presentate non risolvono il problema, fare clic su **Avanti: dettagli**. Inviare i dettagli del problema e le informazioni di contatto. Selezionare **Avanti: esaminare + creare**
dettagli ![](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Esaminare le informazioni e selezionare **Crea** nella parte inferiore del modulo per inviare la richiesta di supporto.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto
Dopo aver inviato la richiesta di supporto, il team di supporto di Azure contatterà l'utente. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Altre risorse
È anche possibile connettersi con la community di SQL Data Warehouse in [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) o tramite il [Forum di Azure SQL data warehouse MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
