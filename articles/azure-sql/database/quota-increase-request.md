---
title: Richiedere un aumento della quota
description: Questa pagina descrive come creare una richiesta di supporto per aumentare le quote per il database SQL di Azure e Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 4557d2ecdb49cd50396986f0ea30277f50ecf9f7
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987286"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Aumento della quota della richiesta per database SQL di Azure e SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo illustra come richiedere un aumento della quota per il database SQL di Azure e Azure SQL Istanza gestita. Viene inoltre illustrato come abilitare l'accesso alla sottoscrizione a un'area.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Crea una nuova richiesta di supporto

Usare la procedura seguente per creare una nuova richiesta di supporto dal portale di Azure per il database SQL.

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Guida e supporto**.

   ![Collegamento Guida e supporto](./media/quota-increase-request/help-plus-support.png)

1. In **Guida e supporto** selezionare **Nuova richiesta di supporto**.

    ![Creare una nuova richiesta di supporto](./media/quota-increase-request/new-support-request.png)

1. Per **tipo di problema**selezionare **limiti per servizio e sottoscrizione (quote)**.

   ![Selezionare un tipo di problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare una sottoscrizione per una quota aumentata](./media/quota-increase-request/select-subscription-support-request.png)

1. Per **tipo di quota**selezionare uno dei tipi di quote seguenti:

   - **Database SQL** per le quote del singolo database e del pool elastico.
   - **Istanza gestita di database SQL** per le istanze gestite.

   Selezionare quindi **Next: Soluzioni >>** .

   ![Selezionare un tipo di quota](./media/quota-increase-request/select-quota-type.png)

1. Nella finestra dei **Dettagli** selezionare **immettere i dettagli** per immettere informazioni aggiuntive.

   ![Immettere il collegamento Dettagli](./media/quota-increase-request/provide-details-link.png)

Se si fa clic su **invio dettagli** , viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. Le sezioni seguenti descrivono le diverse opzioni per il **database SQL** e i tipi di quota **istanza gestita di database SQL** .

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Tipi di quota del database SQL

Le sezioni seguenti descrivono le opzioni di aumento della quota per i tipi di quota del **database SQL** :

- Unità di transazione di database (DTU) per server
- Server per sottoscrizione
- Accesso all'area della serie M
- Accesso all'area

### <a name="database-transaction-units-dtus-per-server"></a>Unità di transazione di database (DTU) per server

Usare la procedura seguente per richiedere un aumento di DTU per server.

1. Selezionare le **unità di transazione di database (DTU) per** tipo di quota server.

1. Nell'elenco **Risorse** selezionare la risorsa di destinazione.

1. Nel campo **nuova quota** immettere il nuovo limite DTU che si sta richiedendo.

   ![Dettagli quota DTU](./media/quota-increase-request/quota-details-dtus.png)

Per altre informazioni, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](resource-limits-dtu-single-databases.md) e i [limiti delle risorse per i pool elastici usando il modello di acquisto DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Seguire i passaggi seguenti per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione**.

1. Nell'elenco **Posizione** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **Nuova quota** immettere la richiesta per il numero massimo di server in tale area.

   ![Dettagli quota server](./media/quota-increase-request/quota-details-servers.png)

Per altre informazioni, vedere [limiti delle risorse del database SQL e governance delle risorse](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a>Abilitare l'accesso alla sottoscrizione a un'area

Alcuni tipi di offerte non sono disponibili in ogni area. È possibile che venga visualizzato un errore simile al seguente:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Se la sottoscrizione deve accedere a una determinata area, selezionare l'opzione di **accesso all'area** . Nella richiesta specificare i dettagli dell'offerta e dello SKU che si vuole abilitare per l'area. Per esplorare le opzioni offerte e SKU, vedere [prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Selezionare il tipo di quota di **accesso all'area** .

1. Nell'elenco **selezionare un percorso** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Immettere il **modello di acquisto**e i dettagli di **utilizzo previsti** .

   ![Accesso all'area richiesta](./media/quota-increase-request/quota-details-whitelisting.png)

### <a name="enable-m-series-access-to-a-region"></a><a id="mseries"></a>Abilitare l'accesso alla serie M in un'area

Per abilitare l'hardware della serie M per una sottoscrizione e un'area, è necessario aprire una richiesta di supporto.

1. Selezionare il tipo di quota di **accesso all'area della serie M** .

1. Nell'elenco **selezionare un percorso** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.


   ![Accesso all'area richiesta](./media/quota-increase-request/quota-m-series.png)

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a>Tipo di quota SQL Istanza gestita

Per il tipo di quota **SQL istanza gestita** , attenersi alla procedura seguente:

1. Nell'elenco **Region (area** ) selezionare l'area di Azure di destinazione.

1. Immettere i nuovi limiti richiesti per la **subnet** e la **vCore**.

   ![Dettagli quota di SQL Istanza gestita](./media/quota-increase-request/quota-details-managed-instance.png)

Per altre informazioni, vedere [Panoramica di Azure SQL istanza gestita limiti delle risorse](../managed-instance/resource-limits.md).

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di quota del database SQL. Selezionare quindi **Next: Rivedi e crea>>** e dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver inviato la richiesta, questa verrà rivista. L'utente verrà contattato con una risposta in base alle informazioni fornite nel modulo.

Per altre informazioni sui limiti di Azure, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
