---
title: Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come ottenere visibilità a livello di tenant nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: rkarlin
ms.openlocfilehash: f05c0469dffa074501a301802412901ead3d1e69
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720816"
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

3. In **Access management for Azure resources** (Gestione degli accessi per le risorse di Azure) impostare l'interruttore su **Sì**.

   ![Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione - Screenshot](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando si imposta l'interruttore su Sì, si viene assegnati al ruolo Amministratore Accesso utenti in Controllo degli accessi in base al ruolo Azure nell'ambito radice (/). In questo modo si ottiene l'autorizzazione ad assegnare ruoli in tutte le sottoscrizioni e i gruppi di gestione di Azure associati a questa directory di Azure AD. Questo interruttore è disponibile solo per gli utenti a cui è assegnato il ruolo Amministratore globale in Azure AD.

  - Quando si imposta l'interruttore su No, il ruolo Amministratore Accesso utenti in Controllo degli accessi in base al ruolo Azure viene rimosso dall'account utente. Non è quindi più possibile assegnare ruoli in tutte le sottoscrizioni e i gruppi di gestione di Azure associati a questa directory di Azure AD. È possibile visualizzare e gestire solo le sottoscrizioni e i gruppi di gestione di Azure ai quali si ha accesso.

4. Fare clic su **Salva** per salvare l'impostazione.

    - Questa impostazione non è una proprietà globale e si applica solo all'utente attualmente connesso.

5. Eseguire le attività necessarie per cui sono richiesti privilegi elevati. Al termine, impostare nuovamente l'opzione su **No**.


### <a name="assign-rbac-roles-to-users"></a>Assegnare ruoli Controllo degli accessi in base al ruolo agli utenti
Per ottenere visibilità in tutte le sottoscrizioni, gli amministratori tenant devono assegnare il ruolo RBAC appropriato a tutti gli utenti a cui desiderano garantire la visibilità a livello di tenant, incluso a livello di gruppo di gestione radice. È consigliabile assegnare il ruolo di **Amministratore della sicurezza** oppure il **Ruolo con autorizzazioni di lettura per la sicurezza**. Il ruolo di amministratore della sicurezza è generalmente necessario per applicare i criteri a livello di radice, mentre il ruolo con autorizzazioni di lettura per la sicurezza è sufficiente per garantire la visibilità a livello di tenant. Per altre informazioni sulle autorizzazioni concesse da questi ruoli, vedere la [Descrizione del ruolo predefinito di amministratore della sicurezza](../role-based-access-control/built-in-roles.md#security-admin) o la [Descrizione del ruolo con autorizzazioni di lettura per la sicurezza predefinito ](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Assegnare ruoli Controllo degli accessi in base al ruolo agli utenti tramite il portale di Azure: 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Per visualizzare i gruppi di gestione, selezionare **Tutti i servizi** nel menu principale di Azure, quindi selezionare **Gruppi di gestione**.
1.  Selezionare un gruppo di gestione e fare clic su **dettagli**.

    ![Schermata dei dettagli dei Gruppi di gestione](./media/security-center-management-groups/management-group-details.PNG)
 
1. Fare clic su **Controllo di accesso (IAM)** e quindi su **Assegnazioni di ruolo**.

1. Fare clic su **Aggiungi assegnazione ruolo**.

1. Selezionare il ruolo da assegnare e l'utente, quindi fare clic su **Salva**.  
   
   ![Aggiungere la schermata del Ruolo con autorizzazioni di lettura per la sicurezza](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Assegnare ruoli Controllo degli accessi in base al ruolo agli utenti con PowerShell: 
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

