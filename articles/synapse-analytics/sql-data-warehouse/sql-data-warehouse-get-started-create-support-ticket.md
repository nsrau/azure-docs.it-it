---
title: Richiedere un aumento della quota e ottenere supporto
description: Creare una richiesta di supporto nel portale di Azure per Azure Synapse Analytics. Richiedere un aumento della quota e ottenere supporto per la risoluzione di problemi.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: de45e338b0b863dc2364af399a6991f56658b0e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212276"
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

1. Nella finestra dei **Dettagli** selezionare **immettere i dettagli** per immettere informazioni aggiuntive.

   ![Collegamento "Specifica dettagli"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipi di richiesta di quota

Selezionando **invio dettagli** viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. Le sezioni seguenti descrivono le diverse richieste di quota disponibili per Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Unità di data warehouse del pool SQL di sinapsi (DWU) per server

Seguire questi passaggi per richiedere un aumento di DWU per server.

1. Selezionare il tipo di quota **DWU del pool SQL sinapsi per server** .

1. Selezionare la **risorsa** a cui si vuole applicare l'aumento della quota usando l'elenco a discesa.

1. Immettere la nuova quota nella sezione **quota richiesta** .

1. Fai clic su **Salva e continua**.

   ![Dettagli quota DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Server per sottoscrizione

Per richiedere un aumento del numero di server per sottoscrizione, è necessario completare i passaggi seguenti:

1. Selezionare i **server SQL per sottoscrizione** come tipo di quota.

1. Nell'elenco **Posizione** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **Richiedi quota** immettere la richiesta per il numero massimo di server in tale area.

   ![Dettagli quota server](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Fai clic su **Salva e continua**.

Alcuni tipi di offerte non sono disponibili in ogni area. Può essere visualizzato l'errore seguente:

![Errore di accesso all'area](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Abilitare l'accesso alla sottoscrizione a un'area

Per abilitare l'accesso all'area per una sottoscrizione, è necessario completare i passaggi seguenti:  

1. Selezionare il tipo di quota di **accesso all'area del pool SQL di sinapsi (data warehouse)** .

1. Selezionare l'area scegliendo un **percorso** dall'elenco a discesa.

1. Indicare il requisito per le prestazioni di DWU nella sezione **DWU required** .

1. Immettere la **Descrizione dei requisiti aziendali**. 

1. Fai clic su **Salva e continua**.

![Accesso all'area](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Per altre richieste di quota

Selezionare **altra richiesta di quota** dal menu a discesa tipo di quota per altri tipi di richiesta di quota:

![Dettagli altra quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di supporto del database SQL. Quindi selezionare **Avanti: verifica + crea>>**.

![Esaminare i dettagli di creazione](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Dopo aver esaminato i dettagli della richiesta, selezionare **Crea** per inviare la richiesta.

![Crea ticket](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto

Dopo avere inviato la richiesta di supporto, si verrà contattati dal team di supporto tecnico di Azure. Per controllare lo stato e i dettagli della richiesta, selezionare **tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Altre risorse

È anche possibile connettersi con la community di analisi delle sinapsi di Azure in [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) o tramite la [pagina Microsoft Q&una domanda per l'analisi delle sinapsi di Azure](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

