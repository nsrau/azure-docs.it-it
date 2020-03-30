---
title: Richiedi aumenti di quota e ottieni supporto
description: Come creare una richiesta di supporto nel portale di Azure per Azure Synapse Analytics.How to create a support request in the Azure portal for Azure Synapse Analytics. Le quote delle richieste aumentano o ottengono supporto per la risoluzione dei problemi.
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350888"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Richiesta di aumenti delle quote e supporto per Azure Synapse Analytics

Questo articolo descrive come inviare un ticket di supporto nel portale di Azure per Analisi synapse di Azure.This article describes how to submit a support ticket in the Azure portal for Azure Synapse Analytics. Questo processo consente di richiedere un aumento della quota o di inviare una richiesta di supporto tecnico per il team di supporto tecnico.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto

Usare la procedura seguente per creare una nuova richiesta di supporto dal portale di Azure per Azure Synapse Analytics.Use the following steps to create a new support request from the Azure portal for Azure Synapse Analytics.

1. Nel menu [del portale](https://portal.azure.com) di Azure selezionare Guida **e supporto**.

   ![Il link Aiuto e supporto](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. In **Guida e supporto**selezionare Nuova richiesta di **supporto**.

    ![Creare una nuova richiesta di supportoCreate a new support request](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Esaminare il piano di supporto di [Azure.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * Il supporto per **fatturazione, quote e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
   * Il supporto **Break-fix** viene fornito tramite il supporto [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)o [Premier.](https://azure.microsoft.com/support/plans/premier/) I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
   * Servizi di **consulenza** e **mentoring per sviluppatori** sono disponibili solo per i livelli di supporto tecnico [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se si dispone di un piano di supporto Premier, è anche possibile segnalare problemi di Analisi synapse di Azure nel [portale online Microsoft Premier.](https://premier.microsoft.com/) Per altre informazioni sui vari piani di supporto, vedere Piani di supporto di [Azure,](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) inclusi ambito, tempi di risposta, prezzi e così via.  Per le domande frequenti sul supporto di Azure, vedere Domande frequenti sul supporto di Azure.For frequently asked questions about Azure support, see [Azure support FAQs](https://azure.microsoft.com/support/faq/).

1. Per **Tipo di**problema , selezionare il tipo di problema appropriato. Per i problemi di break-fix, selezionare **Tecnico**. Per le richieste di aumento della quota, selezionare **Limiti di servizio e sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > La presente nella versione restante di questo articolo è incentrata sulle richieste di aumento delle quote. Ma è anche possibile selezionare **Tecnico** qui per le richieste di supporto per la risoluzione dei problemi. Se si seleziona **Tecnico**, verrà chiesto di fornire un riepilogo e quindi identificare un tipo di problema selezionando **Seleziona tipo di problema**. Potresti vedere le soluzioni per risolvere il problema. Se le soluzioni presentate non risolvono il problema, selezionare **Avanti:Dettagli** e compilare il modulo per inviare il ticket di supporto.

1. Per le richieste di aumento della quota, selezionare **Analisi synapse** di Azure per Tipo **di quota**. Selezionare **quindi Avanti: Soluzioni >>**.

   ![Selezionare un tipo di quota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Nella finestra **Dettagli** selezionare **Fornisci dettagli** per immettere informazioni aggiuntive.

   ![Il link "Fornisci dettagli"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipi di richiesta di quota

Facendo clic su **Fornisci dettagli** viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. The following sections describe the different quota requests available for Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unità data warehouse (DKU) per server

Utilizzare la procedura seguente per richiedere un aumento delle DKU per server.

1. Selezionare le **unità di data warehouse (DCU) per** ogni tipo di quota del server.

1. Nell'elenco **Risorsa** selezionare la risorsa di destinazione.

1. Nel campo **Richiedi quota** immettere il nuovo limite DWU richiesto.

   ![Dettagli della quota DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Utilizzare la procedura seguente per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione.**

1. Nell'elenco **Posizione** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **Nuova quota** immettere la richiesta per il numero massimo di server nell'area.

   ![Dettagli della quota dei server](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Abilitare l'accesso in sottoscrizione a un'areaEnable subscription access to a region

Alcuni tipi di offerta non sono disponibili in tutte le aree geografiche. È possibile che venga visualizzato un errore simile al seguente:

`This location is not available for subscription`

Se l'abbonamento deve accedere in una determinata area, utilizzare l'opzione **Altra richiesta di quota** per richiedere l'accesso. Nella richiesta specificare i dettagli dell'offerta e dello SKU che si desidera abilitare per l'area. Per esplorare le opzioni offerte e SKU, vedere Prezzi di [Azure Synapse Analytics.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

![Altri dettagli della quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di supporto del database SQL. Quindi selezionare **Avanti: Rivedere e creare>>** e, dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="monitor-a-support-ticket"></a>Monitorare un ticket di supporto

Dopo aver inviato la richiesta di supporto, il team di supporto di Azure contatterà l'utente. Per controllare i dettagli e lo stato della richiesta, fare clic su **Tutte le richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Altre risorse

È anche possibile connettersi con la community di Analisi synapse di Azure in [Overflow dello Stack](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) o tramite il forum MSDN di Azure SQL Data [Warehouse.](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)

