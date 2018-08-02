---
title: Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come ottenere visibilità a livello di tenant nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144568"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure
Questo articolo illustra come iniziare a eseguire diverse azioni che ottimizzano i vantaggi offerti dal Centro sicurezza di Azure. Queste azioni consentono di ottenere visibilità su tutte le sottoscrizioni di Azure collegate al tenant di Azure Active Directory e di gestire in modo efficiente la sicurezza dell'organizzazione su vasta scala applicando i criteri di sicurezza in più sottoscrizioni secondo una logica aggregativa.

## <a name="management-groups"></a>Gruppi di gestione
I gruppi di gestione di Azure consentono di gestire in modo efficiente non solo l'accesso, i criteri e la creazione di report sui gruppi di sottoscrizioni, ma anche l'intero spazio di Azure, eseguendo azioni sul gruppo di gestione radice. A ogni tenant di Azure AD viene assegnato un gruppo di gestione principale denominato gruppo di gestione radice. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. 

Il gruppo di gestione radice viene creato automaticamente quando si esegue una delle azioni seguenti: 
1. Si acconsente esplicitamente all'uso dei gruppi di gestione di Azure passando a **Gruppi di gestione** nel [portale di Azure](https://portal.azure.com).
2. Si crea un gruppo di gestione tramite una chiamata API.
3. Si crea un gruppo di gestione con PowerShell.

Per una panoramica dettagliata dei gruppi di gestione, vedere l'articolo [Organizzare le risorse con i gruppi di gestione di Azure](../azure-resource-manager/management-groups-overview.md).

## <a name="create-a-management-group-in-the-azure-portal"></a>Creare un gruppo di gestione nel portale di Azure
È possibile organizzare le sottoscrizioni in gruppi di gestione a cui vengono applicati i criteri di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente i criteri applicati al gruppo di gestione. Anche se i gruppi di gestione non sono necessari per l'onboarding del Centro sicurezza, è consigliabile creare almeno un gruppo di gestione per poter creare il gruppo di gestione radice. Dopo la creazione del gruppo, vi saranno collegate tutte le sottoscrizioni nel tenant di Azure AD. Per le istruzioni relative a PowerShell e per altre informazioni, vedere [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](../azure-resource-manager/management-groups-create.md).

 
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Gruppi di gestione**.
3. Nella pagina principale selezionare **Nuovo Gruppo di gestione.** 

    ![Gruppo principale](./media/security-center-management-groups/main.png) 
4.  Compilare il campo ID del gruppo di gestione. 
    - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. 
    - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.  

      ![Create](./media/security-center-management-groups/create_context_menu.png)  
5.  Selezionare **Salva**

### <a name="view-management-groups-in-the-azure-portal"></a>Visualizzare i gruppi di gestione nel portale di Azure
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Per visualizzare i gruppi di gestione, selezionare **Tutti i servizi** nel menu principale di Azure.
3. In **Generale** selezionare **Gruppi di gestione**.

    ![Creare un gruppo di gestione](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Concedere la visibilità a livello di tenant e la possibilità di assegnare i criteri

Per ottenere visibilità sulla sicurezza di tutte le sottoscrizioni registrate nel tenant di Azure AD, è necessario assegnare un ruolo Controllo degli accessi in base al ruolo con autorizzazioni di lettura sufficienti nel gruppo di gestione radice.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory
Un amministratore del tenant di Azure Active Directory non ha accesso diretto alle sottoscrizioni di Azure, ma, in qualità di amministratore della directory, ha il diritto di elevare se stesso a un ruolo che vi abbia accesso. Un amministratore del tenant di Azure AD deve elevare se stesso ad Amministratore Accesso utenti a livello del gruppo di gestione radice per poter assegnare i ruoli Controllo degli accessi in base al ruolo. Per le istruzioni relative a PowerShell e per altre informazioni, vedere [Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Accedere al [portale di Azure](https://portal.azure.com) o all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com).

2. Nell'elenco di navigazione fare clic su **Azure Active Directory** e quindi su **Proprietà**.

   ![Proprietà di Azure AD - Screenshot](./media/security-center-management-groups/aad-properties.png)

3. In **Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione** impostare l'opzione su **Sì**.

   ![Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione - Screenshot](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando si imposta l'opzione su **Sì**, l'account di amministratore globale (utente attualmente connesso) viene aggiunto al ruolo Amministratore Accesso utenti nel Controllo degli accessi in base al ruolo di Azure all'ambito radice (`/`), concedendo all'utente l'accesso per visualizzare e creare report per tutte le sottoscrizioni di Azure associate al tenant di Azure AD.

   - Quando si imposta l'opzione su **No**, l'account di amministratore globale (utente attualmente connesso) viene rimosso dal ruolo Amministratore Accesso utenti nel Controllo degli accessi in base al ruolo di Azure. Non è possibile visualizzare tutte le sottoscrizioni di Azure associate al tenant di Azure AD ed è possibile visualizzare e gestire solo le sottoscrizioni di Azure a cui è stato concesso l'accesso.

4. Fare clic su **Salva** per salvare l'impostazione.

    - Questa impostazione non è una proprietà globale e si applica solo all'utente attualmente connesso.

5. Eseguire le attività necessarie per cui sono richiesti privilegi elevati. Al termine, impostare nuovamente l'opzione su **No**.

### <a name="open-or-refresh-security-center"></a>Aprire o aggiornare il Centro sicurezza
Dopo avere ottenuto l'accesso con privilegi elevati, aprire o aggiornare il Centro sicurezza di Azure per verificare di avere visibilità su tutte le sottoscrizioni nel tenant di Azure AD. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Assicurarsi di selezionare nel selettore delle sottoscrizioni tutte le sottoscrizioni da visualizzare nel Centro sicurezza.
    ![Screenshot del selettore delle sottoscrizioni](./media/security-center-management-groups/subscription-selector.png)
1. Selezionare **Tutti i servizi** nel menu principale di Azure, quindi selezionare **Centro sicurezza**.
2. In **Panoramica** è presente un grafico relativo alla copertura delle sottoscrizioni. 
    ![Screenshot del grafico sulla copertura delle sottoscrizioni](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Fare clic su **Copertura** per visualizzare l'elenco di sottoscrizioni coperte. 
    ![Screenshot dell'elenco con la copertura delle sottoscrizioni](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>Assegnare ruoli Controllo degli accessi in base al ruolo agli utenti
Dopo che un amministratore del tenant ha eseguito l'accesso con privilegi elevati, può assegnare un ruolo Controllo degli accessi in base al ruolo agli utenti pertinenti a livello del gruppo di gestione radice. Il ruolo consigliato per l'assegnazione è [**Lettore**](../role-based-access-control/built-in-roles.md#reader). Questo ruolo è necessario per fornire visibilità a livello di tenant. Il ruolo assegnato verrà automaticamente propagato a tutti i gruppi di gestione e a tutte le sottoscrizioni nel gruppo di gestione radice. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Ruoli disponibili](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles). 

1. Installare [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Eseguire i comandi seguenti: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Quando richiesto, accedere con le credenziali di amministratore globale. 

    ![Screenshot del prompt di accesso](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Concedere le autorizzazioni del ruolo Lettore eseguendo il comando seguente:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Per rimuovere il ruolo, usare il comando seguente: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Rimuovere l'accesso con privilegi elevati 
Dopo che i ruoli Controllo degli accessi in base al ruolo sono stati assegnati agli utenti, l'amministratore del tenant deve rimuovere se stesso dal ruolo Amministratore Accesso utenti.

1. Accedere al [portale di Azure](https://portal.azure.com) o all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com).

2. Nell'elenco di navigazione fare clic su **Azure Active Directory** e quindi su **Proprietà**.

3. In **Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione** impostare l'opzione su **No**.

4. Fare clic su **Salva** per salvare l'impostazione.



## <a name="adding-subscriptions-to-a-management-groups"></a>Aggiunta di sottoscrizioni a un gruppo di gestione
È possibile aggiungere le sottoscrizioni al gruppo di gestione creato. Questi passaggi non sono obbligatori per ottenere visibilità a livello di tenant sui criteri globali e sulla gestione dell'accesso.

1. In **Gruppi di gestione** selezionare un gruppo di gestione a cui aggiungere la sottoscrizione.

    ![Selezionare un gruppo di gestione a cui aggiungere la sottoscrizione](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selezionare **Aggiungi esistente**.

    ![Aggiungi esistente](./media/security-center-management-groups/add-existing.png)

3. Immettere la sottoscrizione in **Aggiungi risorsa esistente** e fare clic su **Salva**.

4. Ripetere i passaggi da 1 a 3 finché non sono state aggiunte tutte le sottoscrizioni nell'ambito.

 > [!NOTE]
 > I gruppi di gestione possono contenere sia sottoscrizioni che gruppi di gestione figlio. Quando si assegna a un utente un ruolo Controllo degli accessi in base al ruolo per il gruppo di gestione padre, l'accesso viene ereditato dalle sottoscrizioni del gruppo di gestione figlio. Anche i criteri impostati nel gruppo di gestione padre vengono ereditati dagli elementi figlio. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come ottenere visibilità a livello di tenant per il Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)

