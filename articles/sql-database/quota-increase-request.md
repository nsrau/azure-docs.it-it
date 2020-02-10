---
title: Richiedere un aumento della quota
description: Questa pagina descrive come creare una richiesta di supporto per aumentare le quote per i database singoli, i server e le istanze gestite del database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111098"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Aumento della quota della richiesta per il database SQL di Azure

Questo articolo illustra come richiedere un aumento della quota per il database SQL di Azure per database singoli, server e istanze gestite. Viene inoltre illustrato come abilitare l'accesso alla sottoscrizione a un'area.

## <a id="newquota"></a>Crea una nuova richiesta di supporto

Usare la procedura seguente per creare una nuova richiesta di supporto dal portale di Azure per il database SQL.

1. Scegliere **Guida e supporto**dal menu [portale di Azure](https://portal.azure.com) .

   ![Il collegamento alla guida e al supporto tecnico](./media/quota-increase-request/help-plus-support.png)

1. In **Guida e supporto**selezionare **nuova richiesta di supporto**.

    ![Crea una nuova richiesta di supporto](./media/quota-increase-request/new-support-request.png)

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)** .

   ![Selezionare un tipo di problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare una sottoscrizione per una quota aumentata](./media/quota-increase-request/select-subscription-support-request.png)

1. Per **tipo di quota**selezionare uno dei tipi di quote seguenti:

   - **Database SQL** per le quote del singolo database e del pool elastico.
   - **Istanza gestita di database SQL** per le istanze gestite.

   Quindi selezionare **Avanti: soluzioni > >** .

   ![Selezionare un tipo di quota](./media/quota-increase-request/select-quota-type.png)

1. Nella finestra dei **Dettagli** selezionare **specificare i dettagli** per immettere informazioni aggiuntive.

   ![Collegamento "fornire i dettagli"](./media/quota-increase-request/provide-details-link.png)

Se si fa clic su **Fornisci dettagli** , viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. Le sezioni seguenti descrivono le diverse opzioni per il **database SQL** e i tipi di quota **istanza gestita di database SQL** .

## <a id="sqldbquota"></a>Tipi di quota del database SQL

Le sezioni seguenti descrivono tre opzioni di aumento della quota per i tipi di quota del **database SQL** :

- Unità di transazione di database (DTU) per server
- Server per sottoscrizione
- Abilitare l'accesso alla sottoscrizione a un'area

### <a name="database-transaction-units-dtus-per-server"></a>Unità di transazione di database (DTU) per server

Usare la procedura seguente per richiedere un aumento di DTU per server.

1. Selezionare le **unità di transazione di database (DTU) per** tipo di quota server.

1. Nell'elenco di **risorse** selezionare la risorsa di destinazione.

1. Nel campo **nuova quota** immettere il nuovo limite DTU che si sta richiedendo.

   ![Dettagli quota DTU](./media/quota-increase-request/quota-details-dtus.png)

Per altre informazioni, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](sql-database-dtu-resource-limits-single-databases.md) e i [limiti delle risorse per i pool elastici usando il modello di acquisto DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Usare la procedura seguente per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione** .

1. Nell'elenco **location (percorso** ) selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **nuova quota** immettere la richiesta per il numero massimo di server in tale area.

   ![Dettagli quota server](./media/quota-increase-request/quota-details-servers.png)

Per altre informazioni, vedere [limiti delle risorse del database SQL e governance delle risorse](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a>Abilitare l'accesso alla sottoscrizione a un'area

Alcuni tipi di offerte non sono disponibili in ogni area. Potrebbe essere visualizzato un errore simile al seguente:

`This location is not available for subscription`

Se la sottoscrizione richiede l'accesso in una determinata area, usare l'opzione **altra richiesta di quota** per richiedere l'accesso. Nella richiesta specificare i dettagli dell'offerta e dello SKU che si vuole abilitare per l'area. Per esplorare le opzioni offerte e SKU, vedere [prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Altri dettagli quota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>Tipo di quota istanza gestita

Per il tipo di quota **SQL Server istanza gestita** , attenersi alla procedura seguente:

1. Nell'elenco **Region (area** ) selezionare l'area di Azure di destinazione.

1. Immettere i nuovi limiti richiesti per la **subnet** e la **vCore**.

   ![Dettagli quota istanza gestita](./media/quota-increase-request/quota-details-managed-instance.png)

Per altre informazioni, vedere [Panoramica dei limiti delle risorse dell'istanza gestita di database SQL di Azure](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di quota del database SQL. Fare quindi clic su **Avanti: rivedere + crea > >** e, dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver inviato la richiesta, questa verrà rivista. Si verrà contattati con una risposta in base alle informazioni fornite nel modulo.

Per altre informazioni sui limiti di Azure, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).
