---
title: Richiedere un aumento della quota e ottenere supporto
description: Creare una richiesta di supporto nel portale di Azure per Azure Synapse Analytics. Richiedere un aumento della quota e ottenere supporto per la risoluzione di problemi.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835462"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Richiedere un aumento della quota e ottenere supporto per Azure Synapse Analytics

Questo articolo descrive come inviare un ticket di supporto nel portale di Azure per Azure Synapse Analytics. Questa procedura consente di richiedere un aumento della quota o di inviare una richiesta di supporto tecnico al team di progettazione.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto

Seguire questi passaggi per creare una nuova richiesta di supporto dal portale di Azure per Azure Synapse Analytics.

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Guida e supporto**.

   ![Collegamento Guida e supporto](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. In **Guida e supporto** selezionare **Nuova richiesta di supporto**.

    ![Creare una nuova richiesta di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Rivedere il [piano di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto **in garanzia** viene offerto tramite il supporto tecnico [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) o [Premier](https://azure.microsoft.com/support/plans/premier/). I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * I servizi di **mentoring per sviluppatori** e **consulenza** sono disponibili solo per i livelli di supporto tecnico [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se si dispone di un piano di supporto tecnico Premier, è anche possibile segnalare problemi relativi ad Azure Synapse Analytics nel [portale Microsoft Premier online](https://premier.microsoft.com/). Per altre informazioni sui vari piani di supporto, ad esempio su ambito, tempi di risposta, prezzi e così via, vedere la pagina relativa ai [piani di supporto di Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).  Per domande frequenti sul supporto tecnico di Azure, vedere [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

1. Per **Tipo di problema** selezionare il tipo di problema appropriato. Per risolvere problemi in garanzia, selezionare **Tecnico**. Per le richieste di aumento della quota selezionare **Limiti del servizio e della sottoscrizione (quote)** .

   ![Selezionare un tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Il resto di questo articolo è incentrato sulle richieste di aumento della quota. È anche possibile selezionare **Tecnico** per richieste di supporto per la risoluzione di problemi. Se si seleziona **Tecnico** è necessario fornire un riepilogo e quindi identificare un tipo di problema scegliendo **Selezionare il tipo di problema**. È possibile che vengano visualizzate soluzioni per risolvere il problema. Se le soluzioni presentate non consentono di risolvere il problema, selezionare **Avanti: Dettagli** e completare il modulo per inviare il ticket di supporto.

1. Per le richieste di aumento della quota selezionare **Azure Synapse Analytics** per il **Tipo di quota**. Selezionare quindi **Next: Soluzioni >>** .

   ![Selezionare un tipo di quota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Nella finestra **Dettagli** selezionare **Specifica dettagli** per immettere altre informazioni.

   ![Collegamento "Specifica dettagli"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipi di richiesta di quota

Se si fa clic su **Specifica dettagli** viene visualizzata la finestra **Dettagli quota** che consente di aggiungere altre informazioni. Le sezioni seguenti descrivono le diverse richieste di quota disponibili per Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unità Data Warehouse (DWU) per server

Seguire questi passaggi per richiedere un aumento di DWU per server.

1. Selezionare il tipo di quota **Unità Data Warehouse (DWU) per server**.

1. Nell'elenco **Risorse** selezionare la risorsa di destinazione.

1. Nel campo **Richiedi quota** immettere il nuovo limite DWU richiesto.

   ![Dettagli quota DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Seguire i passaggi seguenti per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione**.

1. Nell'elenco **Posizione** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **Nuova quota** immettere la richiesta per il numero massimo di server in tale area.

   ![Dettagli quota server](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Abilitare l'accesso alla sottoscrizione a un'area

Alcuni tipi di offerte non sono disponibili in ogni area. È possibile che venga visualizzato un errore simile al seguente:

`This location is not available for subscription`

Se la sottoscrizione richiede l'accesso in una determinata area, usare l'opzione **Altra richiesta di quota** per richiedere l'accesso. Nella richiesta specificare i dettagli dell'offerta e dello SKU che si vuole abilitare per l'area. Per esplorare le offerte e le opzioni di SKU, vedere [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Dettagli altra quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di supporto del database SQL. Selezionare quindi **Next: Rivedi e crea>>** e dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto

Dopo avere inviato la richiesta di supporto, si verrà contattati dal team di supporto tecnico di Azure. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Altre risorse

È anche possibile connettersi con la community di Azure Synapse Analytics in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) o tramite la [Pagina delle domande di Domande e risposte Microsoft per Azure SQL Data Warehouse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

