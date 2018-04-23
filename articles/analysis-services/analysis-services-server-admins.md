---
title: Gestire gli amministratori del server in Azure Analysis Services | Microsoft Docs
description: Informazioni su come gestire gli amministratori del server per un server Analysis Services in Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: def09f2853f761f3fefca80f341e6cc0557bac86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="manage-server-administrators"></a>Gestire gli amministratori del server
Gli amministratori del server devono essere un utente o un gruppo valido in Azure Active Directory (Azure AD) per il tenant in cui si trova il server. È possibile usare **Amministratori di Analysis Services** per il server nel portale di Azure o Proprietà server in SSMS per gestire gli amministratori del server. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Per aggiungere amministratori del server usando il portale di Azure
1. Nel portale, per il server fare clic su **Amministratori di Analysis Services**.
2. Nel riquadro **\<nomeserver> - Amministratori di Analysis Services** fare clic su **Aggiungi**.
3. In **Aggiungi amministratori del server** selezionare gli account utente da Azure AD o invitare gli utenti esterni in base all'indirizzo di posta elettronica.

    ![Amministratori del server nel portale di Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Per aggiungere amministratori del server usando SSMS
1. Fare clic con il pulsante destro del mouse sul server > **Proprietà**.
2. In **Proprietà computer Analysis Server** fare clic su **Sicurezza**.
3. Fare clic su **Aggiungi** e quindi immettere l'indirizzo di posta elettronica per un utente o un gruppo in Azure AD.
   
    ![Aggiungere gli amministratori del server in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Passaggi successivi 
[Autenticazione e autorizzazioni utente](analysis-services-manage-users.md)  
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md)  

