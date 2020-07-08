---
title: Gestire gli amministratori del server in Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive come gestire gli amministratori del server per un server di Azure Analysis Services usando le API di portale di Azure, PowerShell o REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9edc43f9b2b62a3d9da9d6fba5ab52318e8b6427
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077508"
---
# <a name="manage-server-administrators"></a>Gestire gli amministratori del server

Gli amministratori del server devono essere un utente, un'entità servizio o un gruppo di sicurezza valido nel Azure Active Directory (Azure AD) per il tenant in cui risiede il server. È possibile usare **Amministratori di Analysis Services** per il server nel portale di Azure, in Proprietà server in SSMS, PowerShell o API REST per gestire gli amministratori del server. 

Quando si aggiunge un **gruppo di sicurezza**, utilizzare `obj:groupid@tenantid` . Le entità servizio non sono supportate nei gruppi di sicurezza aggiunti al ruolo di amministratore del server.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Per aggiungere amministratori del server usando il portale di Azure

1. Nel portale, per il server fare clic su **Amministratori di Analysis Services**.
2. In ** \<servername> Analysis Services amministratori**, fare clic su **Aggiungi**.
3. In **Aggiungi amministratori del server** selezionare gli account utente da Azure AD o invitare gli utenti esterni in base all'indirizzo di posta elettronica.

    ![Amministratori del server nel portale di Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Per aggiungere amministratori del server usando SSMS

1. Fare clic con il pulsante destro del mouse sul server > **Proprietà**.
2. In **Proprietà computer Analysis Server** fare clic su **Sicurezza**.
3. Fare clic su **Aggiungi** e quindi immettere l'indirizzo di posta elettronica per un utente o un gruppo in Azure AD.
   
    ![Aggiungere gli amministratori del server in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare il cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) per specificare il parametro Administrator durante la creazione di un nuovo server. <br>
Usare il cmdlet [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) per modificare il parametro Administrator per un server esistente.

## <a name="rest-api"></a>API REST

Usare [Crea](https://docs.microsoft.com/rest/api/analysisservices/servers/create) per specificare la proprietà asAdministrator quando si crea un nuovo server. <br>
Usare [Aggiorna](https://docs.microsoft.com/rest/api/analysisservices/servers/update) per specificare la proprietà asAdministrator quando si modifica un server esistente. <br>



## <a name="next-steps"></a>Passaggi successivi 

[Autenticazione e autorizzazioni utente](analysis-services-manage-users.md)  
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md)  

