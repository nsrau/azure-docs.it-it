---
title: Creare un'identità per un'app Azure nel portale | Documentazione Microsoft
description: Descrive come creare una nuova applicazione ed entità servizio di Azure Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: c2b8498b2d32e2c3c7ed5dca3295ae6a98fa2676
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse

Quando il codice deve accedere alle risorse o modificarle, è necessario configurare un'applicazione Azure Active Directory (AD) a cui assegnare le autorizzazioni richieste. Questo approccio è preferibile rispetto all'esecuzione dell'app con le proprie credenziali perché è possibile assegnare autorizzazioni all'identità dell'app diverse dalle proprie autorizzazioni. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.

Questo articolo illustra come eseguire questa procedura tramite il portale. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso dell'identità del servizio gestito di Azure Active Directory per l'identità dell'applicazione. MSI di Azure Active Directory è una funzionalità di anteprima pubblica di Azure Active Directory che semplifica la creazione di un'identità per il codice. Se il codice viene eseguito in un servizio che supporta MSI di Azure Active Directory e accede alle risorse che supportano l'autenticazione di Azure Active Directory, MSI di Azure Active Directory è un'opzione migliore. Per altre informazioni su MSI di Azure Active Directory, inclusi i servizi che al momento lo supportano, vedere [Identità del servizio gestito per le risorse di Azure](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per completare questo articolo è necessario disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure. Assicurarsi di avere le autorizzazioni appropriate per eseguire questi passaggi.

### <a name="check-azure-active-directory-permissions"></a>Controllare le autorizzazioni di Azure Active Directory

1. Selezionare **Azure Active Directory**.

   ![Selezionare Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. In Azure Active Directory selezionare **Impostazioni utente**.

   ![Selezionare Impostazioni utente](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Controllare l'impostazione **Registrazioni per l'app**. Se il valore è **Sì**, gli utenti non amministratori possono registrare app di Active Directory. Questa impostazione indica che qualsiasi utente in Azure AD può registrare un'app. È possibile passare a [Controllare le autorizzazioni di sottoscrizione di Azure](#check-azure-subscription-permissions).

   ![Visualizzare le registrazioni dell'app](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Se Registrazioni per l'app è impostata su **No**, solo gli utenti amministratori possono registrare app. Controllare se l'account è un amministratore per il tenant di Azure AD. Selezionare **Panoramica** ed esaminare le informazioni utente. Se l'account è assegnato al ruolo Utente, ma l'impostazione Registrazioni per l'app (del passaggio precedente) è limitata agli utenti amministratori, chiedere all'amministratore di essere assegnati a un ruolo amministrativo o di consentire agli utenti di registrare le app.

   ![Trova un utente](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Controllare le autorizzazioni di sottoscrizione di Azure

Nella sottoscrizione di Azure è necessario che l'account disponga dell'accesso `Microsoft.Authorization/*/Write` per assegnare un'app di Active Directory a un ruolo. Questa azione è concessa tramite il ruolo [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore accessi utente](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se il proprio account è assegnato al ruolo **Collaboratore**, non si dispone dell'autorizzazione appropriata. Se si tenterà di assegnare l'entità servizio a un ruolo si riceve un errore.

Per controllare le proprie autorizzazioni di sottoscrizione:

1. Selezionare il proprio account nell'angolo superiore destro e quindi **Autorizzazioni personali**.

   ![selezionare le autorizzazioni utente](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Selezionare la sottoscrizione nell'elenco a discesa. Selezionare **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.

   ![Trova un utente](./media/resource-group-create-service-principal-portal/view-details.png)

1. Visualizzare i propri ruoli assegnati e determinare se si dispone delle autorizzazioni adeguate per assegnare un'app di Active Directory a un ruolo. In caso contrario chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente. Nella figura seguente l'utente è assegnato al ruolo Proprietario, perciò dispone delle autorizzazioni adeguate.

   ![Visualizzare le autorizzazioni](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**.

   ![Selezionare Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Selezionare **Registrazioni per l'app**.

   ![Selezionare Registrazioni per l'app](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Selezionare **Registrazione nuova applicazione**.

   ![Aggiungere l'app](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** come tipo di applicazione da creare. Non è possibile creare credenziali per un'applicazione di tipo [nativo](../active-directory/active-directory-application-proxy-native-client.md). Pertanto, tale tipo non è valido per un'applicazione automatica. Dopo aver impostato i valori selezionare **Crea**.

   ![assegnare un nome all'applicazione](./media/resource-group-create-service-principal-portal/create-app.png)

L'applicazione è stata creata.

## <a name="get-application-id-and-authentication-key"></a>Ottenere l'ID applicazione e la chiave di autenticazione

Quando si esegue l'accesso a livello di codice sono necessari l'ID dell'applicazione e una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni dell'app** in Azure Active Directory selezionare l'applicazione.

   ![Selezionare l'applicazione](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Alcune [applicazioni di esempio](#log-in-as-the-application) definiscono questo valore come ID client.

   ![ID client](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Per generare una chiave di autenticazione, selezionare **Impostazioni**.

   ![selezionare impostazioni](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Per generare una chiave di autenticazione selezionare **Chiavi**.

   ![Selezionare Chiavi](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

   ![Salvare la chiave](./media/resource-group-create-service-principal-portal/save-key.png)

   Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Il valore della chiave sarà fornito insieme all'ID applicazione per eseguire l'accesso come applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

   ![chiave salvata](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Ottenere l'ID tenant

Quando si esegue l'accesso a livello di codice è necessario specificare l'ID tenant con la richiesta di autenticazione.

1. Selezionare **Azure Active Directory**.

   ![Selezionare Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Per ottenere l'ID tenant selezionare **Proprietà** per il tenanto di Azure AD.

   ![selezionare le proprietà di Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copiare l'**ID directory**. Questo valore è l'ID tenant.

   ![ID tenant](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Assegnare l'applicazione al ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

   ![selezionare la sottoscrizione](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Selezionare la sottoscrizione specifica (risorsa o un gruppo di risorse) a cui assegnare l'applicazione.

   ![selezionare la sottoscrizione per l'assegnazione](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Selezionare **Controllo di accesso (IAM)**.

   ![selezionare accesso](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Selezionare **Aggiungi**.

   ![selezionare aggiungi](./media/resource-group-create-service-principal-portal/select-add.png)

1. Selezionare il ruolo che si desidera assegnare all'applicazione. L'immagine seguente mostra il ruolo **Lettore**.

   ![selezionare il ruolo](./media/resource-group-create-service-principal-portal/select-role.png)

1. Cercare l'applicazione e selezionarla.

   ![Cercare l'app](./media/resource-group-create-service-principal-portal/search-app.png)

1. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-configure.md).  
* Per un elenco di azioni disponibili che è possibile concedere o negare agli utenti, vedere [Operazioni di provider di risorse con Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
