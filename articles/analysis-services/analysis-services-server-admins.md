---
title: Gestire gli amministratori del server in Azure Analysis Services | Microsoft Docs
description: Informazioni su come gestire gli amministratori del server per un server Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25c8b49401486edfad0a1a60fe2258a586f6adc2
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017

---
# <a name="manage-server-administrators"></a>Gestire gli amministratori del server
Gli amministratori del server devono essere un utente o un gruppo valido in Azure Active Directory (Azure AD) per il tenant in cui si trova il server. È possibile usare **Amministratori di Analysis Services** nel pannello di controllo del server nel portale di Azure o Proprietà server in SSMS per gestire gli amministratori del server. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Per aggiungere amministratori del server usando il portale di Azure
1. Nel pannello di controllo del server fare clic su **Amministratori di Analysis Services**.
2. Nel pannello **\<nomeserver> - Amministratori di Analysis Services** fare clic su **Aggiungi**.
3. Nel pannello **Aggiungi amministratori del server** selezionare gli account utente da Azure AD o invitare gli utenti esterni in base all'indirizzo di posta elettronica.

    ![Amministratori del server nel portale di Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Per aggiungere amministratori del server usando SSMS
1. Fare clic con il pulsante destro del mouse sul server > **Proprietà**.
2. In **Proprietà computer Analysis Server** fare clic su **Sicurezza**.
3. Fare clic su **Aggiungi** e quindi immettere l'indirizzo di posta elettronica per un utente o un gruppo in Azure AD.
   
    ![Aggiungere gli amministratori del server in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Passaggi successivi 
[Autenticazione e autorizzazioni utente](analysis-services-manage-users.md)  
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md)  


