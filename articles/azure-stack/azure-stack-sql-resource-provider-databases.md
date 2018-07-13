---
title: Uso di database forniti dal provider di risorse di Adapter SQL in Azure Stack | Microsoft Docs
description: Come creare e gestire database SQL eseguito il provisioning con il provider di risorse di adattatore SQL
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2f286c48822956c82f99808092c26f6637be5cb1
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968827"
---
# <a name="create-sql-databases"></a>Creare i database SQL

È possibile creare e gestire i database nel portale per gli utenti self-servizi. Un utente di Azure Stack deve avere una sottoscrizione con un'offerta che include il servizio database SQL.

1. Accedi per il [Azure Stack](azure-stack-poc.md) portale per gli utenti.

2. Selezionare **+ nuovo** &gt; **Data + Storage** &gt; **Database di SQL Server** &gt; **aggiungere**.

3. Sotto **Create Database**, immettere le informazioni necessarie, ad esempio **nome del Database** e **Max Size in MB**.

   >[!NOTE]
   >È necessario almeno 64 MB, che è possibile aumentare le dimensioni del database dopo aver distribuito il database.

   Configurare le altre impostazioni come richiesto per l'ambiente.

4. Sotto **Create Database**, selezionare **SKU**. Sotto **selezionare uno SKU**, selezionare lo SKU per il database.

   ![Creare un database](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Come server di hosting vengono aggiunti ad Azure Stack, che vengano loro assegnate uno SKU. I database vengono creati nel pool di server in uno SKU di hosting.

5. Selezionare **account di accesso**.
6. Sotto **selezionare un account di accesso**, selezionare un account di accesso esistente oppure scegliere **+ crea un nuovo account di accesso**.
7. Sotto **nuovo account di accesso**, immettere un nome per **accesso al Database** e un **Password**.

   >[!NOTE]
   >Queste impostazioni sono le credenziali di autenticazione di SQL Server che viene creata per l'accesso a questo database solo. Il nome utente di accesso deve essere globalmente univoco. È possibile riutilizzare le impostazioni di accesso per gli altri database che utilizzano lo stesso SKU.

   ![Creare un nuovo accesso al database](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Selezionare **OK** per completare la distribuzione del database.

Sotto **Essentials**, come illustrato dopo che il database viene distribuito, annotare il **stringa di connessione**. È possibile usare questa stringa in qualsiasi applicazione che richiede l'accesso a database di SQL Server.

![Recuperare la stringa di connessione](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Database SQL Always On

Per impostazione predefinita, i database Always On sono gestiti in modo diverso rispetto a in un ambiente server autonomo. Per altre informazioni, vedere [Introduzione a SQL Server AlwaysOn in gruppi di disponibilità in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Verificare i database SQL Always On

La schermata seguente mostra come è possibile utilizzare SQL Server Management Studio per esaminare lo stato del database in SQL Always On.

![Stato del database di AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

I database Always On devono mostrare come Synchronized e disponibile in tutte le istanze SQL e vengono visualizzati in gruppi di disponibilità. Nella schermata precedente, l'esempio di database è newdb1 e il suo stato sia **newdb1 (sincronizzato)**.

### <a name="delete-an-alwayson-database"></a>Eliminare un database di AlwaysOn

Quando si elimina un database di SQL AlwaysOn dal provider di risorse, SQL Elimina database dalla replica primaria e dal gruppo di disponibilità.

SQL quindi riporti il database nello stato Restoring in altre repliche e non eliminare il database a meno che non attivata. Se il database non è stato eliminato, le repliche secondarie entrano in uno stato non in sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse di SQL Server](azure-stack-sql-resource-provider-maintain.md)
