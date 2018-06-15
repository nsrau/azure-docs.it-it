---
title: Utilizzo di database forniti dal componente Adapter SQL nello Stack di Azure | Documenti Microsoft
description: Come creare e gestire database SQL eseguito il provisioning con il Provider di risorse di Adapter SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b9f92b4d85e17bc848d82be413df1d0dad7c8548
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294939"
---
# <a name="create-sql-databases"></a>Creare il database SQL
Self-servizi dei database vengono forniti tramite il portale per gli utenti. Un utente di Azure Stack deve avere una sottoscrizione che abbia un'offerta, che contiene il servizio database SQL.

1. Accedi al [Azure Stack](azure-stack-poc.md) portale per gli utenti (amministratori servizio possono anche usare il portale di amministrazione).

2. Fare clic su **+ nuova** &gt; **dati e archiviazione "** &gt; **Database di SQL Server** &gt; **aggiungere**.

3. Compilare il modulo con i dettagli del database, incluso un **nome del Database**, **dimensioni massime**e modificare gli altri parametri in base alle esigenze. Viene chiesto di selezionare un'unità SKU per il database. Se vengono aggiunti i server di hosting, sono state assegnate a uno SKU. I database vengono creati nel pool di server che costituiscono lo SKU di hosting.

  ![Nuovo database](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Le dimensioni del database devono essere almeno 64 MB. Può essere aumentato utilizzando le impostazioni.

4. Immettere le impostazioni di accesso: **accesso al Database**, e **Password**. Queste impostazioni sono le credenziali di autenticazione di SQL Server creato per l'accesso al database solo. Il nome utente account di accesso deve essere globalmente univoco. Creare una nuova impostazione di account di accesso o selezionarne uno esistente. È possibile riutilizzare le impostazioni di accesso per altri database utilizzando lo stesso SKU.

    ![Creare un nuovo account di database](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Invio del form e attendere il completamento della distribuzione.

    Nel pannello risulta, si noti che il campo "Stringa di connessione". È possibile utilizzare tale stringa in qualsiasi applicazione che richiede l'accesso di SQL Server (ad esempio, un'app web) in pila di Azure.

    ![Recuperare la stringa di connessione](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Eliminare i database di SQL AlwaysOn
Quando un database di SQL AlwaysOn viene eliminato dal provider di risorse, è stata eliminata correttamente dal database primario e la disponibilità di AlwaysOn gruppo, ma per progettazione, SQL AG inserisce il database in stato in ogni replica di ripristino e non elimina il database a meno che non attivato. Se non viene eliminato un database, le repliche secondarie passa a non in stato di sincronizzazione. Aggiunge un nuovo database di nuovo per il gruppo di disponibilità con lo stesso tramite RP funziona comunque.

## <a name="verify-sql-alwayson-databases"></a>Verificare i database di SQL AlwaysOn
I database AlwaysOn devono mostrare come sincronizzato e disponibile in tutte le istanze e nel gruppo di disponibilità. Dopo il failover, il database deve connettersi. È possibile utilizzare SQL Server Management Studio per verificare che un database è la sincronizzazione:

![Verificare di AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse di SQL Server](azure-stack-sql-resource-provider-maintain.md)
