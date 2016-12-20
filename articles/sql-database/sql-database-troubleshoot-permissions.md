---
title: "Come eseguire attività amministrative, ad esempio reimpostare la password di amministratore | Microsoft Docs"
description: "Illustra come eseguire attività amministrative comuni nel database SQL di Azure. Ad esempio, reimpostazione delle password amministratore, concessione e rimozione dell&quot;accesso."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: Reimposta password amministratore
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1a175572bf541f0be3936ad8934f52ea6b90dfa1


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Come eseguire attività amministrative comuni come il ripristino della password amministratore nel database SQL di Azure
Usare le azioni rapide illustrate in questo argomento per concedere e rimuovere l'accesso a un database SQL di Azure. Per altre informazioni, vedere:

* [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md)
* [Protezione del database SQL](sql-database-security.md)
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Per reimpostare la password amministratore per un server logico
* Nel [Portale di Azure](https://portal.azure.com) fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta password**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Per garantire che solo agli indirizzi IP autorizzati sia consentito l'accesso al server
* Vedere [Procedura: configurare le impostazioni del firewall su Database SQL mediante il portale di Azure](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Per creare utenti del database indipendente nel database utente
* Usare l'istruzione [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) e vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Per autenticare gli utenti del database indipendente tramite Azure Active Directory
* Vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Per creare altri account di accesso per utenti con privilegi elevati nel database master virtuale
* Usare l'istruzione [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) e vedere la sezione sulla gestione degli account di accesso di [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](sql-database-manage-logins.md) per altri dettagli.




<!--HONumber=Nov16_HO3-->


