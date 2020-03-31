---
title: Richiedere un aumento della quota
description: In questa pagina viene descritto come creare una richiesta di supporto per aumentare le quote per i singoli database, server e istanze gestite del database SQL di Azure.This page describes how to create a support request to increase the quotas for Azure SQL Database single databases, servers, and managed instances.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586156"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Aumenti di quota delle richieste per il database SQL di AzureRequest quota increases for Azure SQL Database

Questo articolo illustra come richiedere un aumento della quota per il database SQL di Azure per singoli database, server e istanze gestite. Viene inoltre illustrato come abilitare l'accesso di sottoscrizione a un'area.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Creare una nuova richiesta di supportoCreate a new support request

Usare la procedura seguente per creare una nuova richiesta di supporto dal portale di Azure per il database SQL.

1. Nel menu [del portale](https://portal.azure.com) di Azure selezionare Guida **e supporto**.

   ![Il link Aiuto e supporto](./media/quota-increase-request/help-plus-support.png)

1. In **Guida e supporto**selezionare Nuova richiesta di **supporto**.

    ![Creare una nuova richiesta di supportoCreate a new support request](./media/quota-increase-request/new-support-request.png)

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/quota-increase-request/select-quota-issue-type.png)

1. In **Sottoscrizione**selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare una sottoscrizione per una quota maggiore](./media/quota-increase-request/select-subscription-support-request.png)

1. Per **Tipo di quota**, selezionare uno dei seguenti tipi di quota:

   - **Database SQL** per quote di database singolo ed elastico.
   - **Istanza gestita database SQL** per le istanze gestite.

   Selezionare **quindi Avanti: Soluzioni >>**.

   ![Selezionare un tipo di quota](./media/quota-increase-request/select-quota-type.png)

1. Nella finestra **Dettagli** selezionare **Fornisci dettagli** per immettere informazioni aggiuntive.

   ![Il link "Fornisci dettagli"](./media/quota-increase-request/provide-details-link.png)

Facendo clic su **Fornisci dettagli** viene visualizzata la finestra **Dettagli quota** che consente di aggiungere ulteriori informazioni. Nelle sezioni seguenti vengono descritte le diverse opzioni per i tipi di quota **del database SQL** e **dell'istanza gestita del database SQL.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Tipi di quota del database SQL

Nelle sezioni seguenti vengono descritte tre opzioni di aumento delle quote per i tipi di quota **del database SQL:**

- Unità di transazione del database (DCU) per serverDatabase transaction units (DTUs) per server
- Server per sottoscrizione
- Abilitare l'accesso in sottoscrizione a un'areaEnable subscription access to a region

### <a name="database-transaction-units-dtus-per-server"></a>Unità di transazione del database (DCU) per serverDatabase transaction units (DTUs) per server

Utilizzare la procedura seguente per richiedere un aumento delle DCU per server.

1. Selezionare le unità di **transazione del database (DCU) per ogni** tipo di quota del server.

1. Nell'elenco **Risorsa** selezionare la risorsa di destinazione.

1. Nel campo **Nuova quota** immettere il nuovo limite DTU richiesto.

   ![Dettagli quota DTU](./media/quota-increase-request/quota-details-dtus.png)

Per ulteriori informazioni, vedere Limiti delle risorse per singoli database che usano il modello di [acquisto DTU](sql-database-dtu-resource-limits-single-databases.md) e Limiti delle risorse per i pool elastici con il modello di [acquisto DTU.](sql-database-dtu-resource-limits-elastic-pools.md)

### <a name="servers-per-subscription"></a>Server per sottoscrizione

Utilizzare la procedura seguente per richiedere un aumento del numero di server per sottoscrizione.

1. Selezionare il tipo di quota **Server per sottoscrizione.**

1. Nell'elenco **Posizione** selezionare l'area di Azure da usare. La quota è per sottoscrizione in ogni area.

1. Nel campo **Nuova quota** immettere la richiesta per il numero massimo di server nell'area.

   ![Dettagli della quota dei server](./media/quota-increase-request/quota-details-servers.png)

Per ulteriori informazioni, vedere [Limiti delle risorse del database SQL e governance delle risorse](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Abilitare l'accesso in sottoscrizione a un'areaEnable subscription access to a region

Alcuni tipi di offerta non sono disponibili in tutte le aree geografiche. È possibile che venga visualizzato un errore simile al seguente:

`This location is not available for subscription`

Se l'abbonamento deve accedere in una determinata area, utilizzare l'opzione **Altra richiesta di quota** per richiedere l'accesso. Nella richiesta specificare i dettagli dell'offerta e dello SKU che si desidera abilitare per l'area. Per esplorare le opzioni offerte e SKU, vedere Prezzi del database SQL di Azure.To explore the offering and SKU options, see [Azure SQL Database pricing.](https://azure.microsoft.com/pricing/details/sql-database/single/)

![Altri dettagli della quota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Tipo di quota dell'istanza gestita

Per il tipo di quota **dell'istanza gestita di SQL Server,** eseguire la procedura seguente:For the SQL Server Managed Instance quota type, use the following steps:

1. Nell'elenco **Area** selezionare l'area di Azure di destinazione.

1. Immettere i nuovi limiti che si richiedono per **Subnet** e **vCore**.

   ![Dettagli quota istanza gestita](./media/quota-increase-request/quota-details-managed-instance.png)

Per altre informazioni, vedere Panoramica dei limiti delle [risorse dell'istanza gestita del database SQL](sql-database-managed-instance-resource-limits.md)di Azure.For more information, see Overview Azure SQL Database managed instance resource limits .

## <a name="submit-your-request"></a>Inviare la richiesta

Il passaggio finale consiste nel compilare i dettagli rimanenti della richiesta di quota del database SQL. Quindi selezionare **Avanti: Rivedere e creare>>** e, dopo aver esaminato i dettagli della richiesta, fare clic su **Crea** per inviare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver inviato la richiesta, verrà esaminata. Sarete contattati con una risposta in base alle informazioni fornite nel modulo.

Per altre informazioni su altri limiti di Azure, vedere [Limiti, quote e vincoli](../azure-resource-manager/management/azure-subscription-service-limits.md)di sottoscrizione e servizio di Azure.
