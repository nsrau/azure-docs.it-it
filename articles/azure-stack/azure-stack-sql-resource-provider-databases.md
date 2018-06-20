---
title: Utilizzo di database forniti dal provider di risorse di Adapter SQL nello Stack di Azure | Documenti Microsoft
description: Come creare e gestire database SQL eseguito il provisioning con il provider di risorse di Adapter SQL
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
ms.openlocfilehash: a82db16f2012672f6e2669f2fd8198b177f501f3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264183"
---
# <a name="create-sql-databases"></a>Creare il database SQL

È possibile creare e gestire i database nel portale per gli utenti self-servizi. Un utente di Azure Stack deve avere una sottoscrizione con un'offerta che include il servizio database SQL.

1. Accedi ai [Azure Stack](azure-stack-poc.md) portale per gli utenti.

2. Selezionare **+ nuova** &gt; **dati e archiviazione "** &gt; **Database di SQL Server** &gt; **aggiungere**.

3. Sotto **Create Database**, immettere le informazioni necessarie, ad esempio **nome del Database** e **Max Size in MB**.

   >[!NOTE]
   >Le dimensioni del database devono essere almeno 64 MB, che è possibile aumentare dopo aver distribuito il database.

   Configurare le altre impostazioni come richiesto per l'ambiente.

4. Sotto **Create Database**, selezionare **SKU**. Sotto **selezionare un'unità SKU**, selezionare l'unità SKU per il database.

   ![Creare un database](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Come server di hosting che vengono aggiunti allo Stack di Azure, sono state assegnate uno SKU. I database vengono creati nel pool di server in un'unità SKU di hosting.

5. Selezionare **account di accesso**.
6. Sotto **selezionare un account di accesso**, selezionare un account di accesso esistente oppure scegliere **+ crea un nuovo account di accesso**.
7. Sotto **nuovo account di accesso**, immettere un nome per **accesso al Database** e un **Password**.

   >[!NOTE]
   >Queste impostazioni sono le credenziali di autenticazione di SQL Server creato per l'accesso a questo database solo. Il nome utente account di accesso deve essere globalmente univoco. È possibile riutilizzare le impostazioni dell'account di accesso per altri database che utilizzano lo stesso SKU.

   ![Creare un nuovo account di database](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Selezionare **OK** per terminare la distribuzione del database.

Sotto **Essentials**, che viene visualizzato dopo aver distribuito il database, prendere nota del **stringa di connessione**. È possibile utilizzare questa stringa in qualsiasi applicazione che deve accedere al database di SQL Server.

![Recuperare la stringa di connessione](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Database SQL Always On

Per impostazione predefinita, i database Always On vengono gestiti in modo diverso rispetto a in un ambiente server autonomo. Per altre informazioni, vedere [Introduzione a SQL Server Always On gruppi di disponibilità in macchine virtuali di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Verificare i database SQL AlwaysOn

Nella schermata seguente viene illustrato come è possibile utilizzare SQL Server Management Studio per esaminare lo stato di database in SQL AlwaysOn.

![Stato del database di AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On database dovrebbero mostrare come Synchronized e disponibile in tutte le istanze SQL e visualizzati in gruppi di disponibilità. Nell'acquisizione schermata precedente, l'esempio di database è newdb1 e il suo stato sia **newdb1 (sincronizzato)**.

### <a name="delete-an-alwayson-database"></a>Eliminare un database AlwaysOn

Quando si elimina un database di SQL AlwaysOn dal provider di risorse, SQL Elimina database dalla replica primaria e dal gruppo di disponibilità.

SQL quindi il database viene posto nello stato Restoring in altre repliche e non eliminare il database a meno che non attivato. Se il database non è stato eliminato, le repliche secondarie passi a uno stato non in sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse di SQL Server](azure-stack-sql-resource-provider-maintain.md)
