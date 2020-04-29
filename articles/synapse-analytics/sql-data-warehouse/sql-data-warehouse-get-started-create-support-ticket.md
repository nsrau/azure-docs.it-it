---
title: Aumenta la quota di richieste e Ottieni supporto
description: Come creare una richiesta di supporto nel portale di Azure di Azure sinapsi Analytics. La quota di richieste aumenta o ottiene il supporto per la risoluzione dei problemi.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350888"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Aumentare la quota di richieste e ottenere supporto per analisi sinapsi di Azure

Questo articolo descrive come inviare un ticket di supporto nel portale di Azure di Azure sinapsi Analytics. Questo processo consente di richiedere un aumento della quota o di inviare una richiesta di supporto tecnico per il team di supporto tecnico.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto

Usare la procedura seguente per creare una nuova richiesta di supporto dal portale di Azure per l'analisi delle sinapsi di Azure.

1. Scegliere **Guida e supporto**dal menu [portale di Azure](https://portal.azure.com) .

   ![Il collegamento alla guida e al supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. In **Guida e supporto**selezionare **nuova richiesta di supporto**.

    ![Crea una nuova richiesta di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Esaminare il [piano di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto per la **correzione delle interruzioni** viene fornito tramite il supporto tecnico [Developer](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)o [Premier](https://azure.microsoft.com/support/plans/premier/) . I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * Servizi di **consulenza** e **mentoring per sviluppatori** sono disponibili solo per i livelli di supporto tecnico [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se si dispone di un piano di supporto tecnico Premier, è anche possibile segnalare problemi di analisi sinapsi di Azure nel [portale di Microsoft Premier Online](https://premier.microsoft.com/). Vedere i [piani di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) per altre informazioni sui vari piani di supporto, inclusi ambito, tempi di risposta, prezzi e così via.  Per domande frequenti sul supporto tecnico di Azure, vedere [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

1. Per **tipo di problema**selezionare il tipo di problema appropriato. Per risolvere i problemi relativi alle interruzioni, selezionare **tecnico**. Per le richieste di aumento della quota, selezionare i **limiti di servizio e sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Il resto di questo articolo è incentrato sulle richieste di aumento della quota. Tuttavia, è anche possibile selezionare **tecnico** qui per richieste di supporto per la risoluzione dei problemi. Se si seleziona **tecnico**, viene richiesto di fornire un riepilogo e quindi di identificare un tipo di problema selezionando **Seleziona tipo di problema**. È possibile che vengano visualizzate soluzioni che consentono di risolvere il problema. Se le soluzioni presentate non consentono di risolvere il problema, fare clic su **Avanti: dettagli** e completare il modulo per inviare il ticket di supporto.

1. Per le richieste di aumento della quota, selezionare **Azure sinapsi Analytics** per il **tipo di quota**. Quindi selezionare **Avanti: soluzioni >>**.

   ![Selezionare un tipo di quota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Nella finestra dei **Dettagli** selezionare **specificare i dettagli** per immettere informazioni aggiuntive.

   ![Collegamento "fornire i dettagli"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipi di richiesta di quota

Se si fa clic su **Fornisci dettagli** , viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. Le sezioni seguenti descrivono le diverse richieste di quota disponibili per analisi sinapsi di Azure.

### <a name="data-warehouse-units-dwus-per-server"></a>Unità di data warehouse (DWU) per server

Usare la procedura seguente per richiedere un aumento di DWU per server.

1. Selezionare le **unità di data warehouse (DTU) per** tipo di quota server.

1. Nell'elenco di **risorse** selezionare la risorsa di destinazione.

1. Nel campo **Richiedi quota** immettere il nuovo limite DWU che si sta richiedendo.

   ![Dettagli quota DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Usare la procedura seguente per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione** .

1. Nell'elenco **location (percorso** ) selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **nuova quota** immettere la richiesta per il numero massimo di server in tale area.

   ![Dettagli quota server](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Abilitare l'accesso alla sottoscrizione a un'area

Alcuni tipi di offerte non sono disponibili in ogni area. Potrebbe essere visualizzato un errore simile al seguente:

`This location is not available for subscription`

Se la sottoscrizione richiede l'accesso in una determinata area, usare l'opzione **altra richiesta di quota** per richiedere l'accesso. Nella richiesta specificare i dettagli dell'offerta e dello SKU che si vuole abilitare per l'area. Per esplorare le opzioni offerte e SKU, vedere [prezzi di Azure sinapsi Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Altri dettagli quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di supporto del database SQL. Quindi selezionare **Avanti: rivedere + crea>>** e, dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto

Dopo aver inviato la richiesta di supporto, il team di supporto di Azure contatterà l'utente. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Altre risorse

È anche possibile connettersi con la community di Azure sinapsi Analytics in [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) o tramite il [forum MSDN di Azure SQL data warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

