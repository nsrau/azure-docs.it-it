---
title: Utilizzo di database forniti dal componente Adapter SQL nello Stack di Azure | Documenti Microsoft
description: Come creare e gestire database SQL eseguito il provisioning con il Provider di risorse di Adapter SQL
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 0cc08c37e879b00f8cd9a4046a4c81c55dab167c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-databases"></a>Creare il database SQL

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Self-servizi di database vengono forniti tramite l'esperienza utente del portale. Un utente richiede una sottoscrizione che abbia un'offerta, che contiene il servizio di database.

1. Accedi al [Azure Stack](azure-stack-poc.md) portale per gli utenti (amministratori servizio possono anche usare il portale di amministrazione).

2. Fare clic su **+ nuovo** &gt; **dati e archiviazione "** &gt; **Database di SQL Server (anteprima)** &gt; **Aggiungi**.

3. Compilare il modulo con i dettagli del database, incluso un **nome del Database**, **dimensioni massime**e modificare gli altri parametri in base alle esigenze. Viene chiesto di selezionare un'unità SKU per il database. Se vengono aggiunti i server di hosting, sono state assegnate a uno SKU. I database vengono creati nel pool di server che costituiscono lo SKU di hosting.

  ![Nuovo database](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Le dimensioni del database devono essere almeno 64 MB. Può essere aumentato utilizzando le impostazioni.

4. Immettere le impostazioni di accesso: **accesso al Database**, e **Password**. Queste impostazioni sono le credenziali di autenticazione di SQL Server creato per l'accesso al database solo. Il nome utente account di accesso deve essere globalmente univoco. Creare una nuova impostazione di account di accesso o selezionarne uno esistente. È possibile riutilizzare le impostazioni di accesso per altri database utilizzando lo stesso SKU.

    ![Creare un nuovo account di database](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Invio del form e attendere il completamento della distribuzione.

    Nel pannello risulta, si noti che il campo "Stringa di connessione". È possibile utilizzare tale stringa in qualsiasi applicazione che richiede l'accesso di SQL Server (ad esempio, un'app web) in pila di Azure.

    ![Recuperare la stringa di connessione](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Eliminare i database SQL
Dal portale

>[!NOTE]
>
>Quando un database di SQL AlwaysOn viene eliminato dal componente, è stata eliminata correttamente dal database primario e gruppo di disponibilità di AlwaysOn, ma dal gruppo di disponibilità SQL progettazione, il database in stato in ogni replica di ripristino e non elimina il database a meno che non attivato. Se non viene eliminato un database, le repliche secondarie passa a non in stato di sincronizzazione. Aggiunge un nuovo database di nuovo per il gruppo di disponibilità con lo stesso tramite RP funziona comunque. Vedere ![rimozione di un database secondario](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Gestire le credenziali di database
È possibile aggiornare le credenziali del database (impostazioni dell'account di accesso).

## <a name="verify-sql-alwayson-databases"></a>Verificare i database di SQL AlwaysOn
I database AlwaysOn devono mostrare come sincronizzato e disponibile in tutte le istanze e nel gruppo di disponibilità. Dopo il failover, il database deve connettersi. È possibile utilizzare SQL Server Management Studio per verificare che un database è la sincronizzazione:

![Verificare di AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
