---
title: Creare un'identità per un'app Azure nel portale | Documentazione Microsoft
description: Descrive come creare una nuova applicazione ed entità servizio di Azure Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee12cb2a0fdd4547ffae8dfce4fddee03167e539
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257417"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Procedura: usare il portale per creare un'applicazione Azure AD e un'entità servizio che possano accedere alle risorse

Questo articolo illustra come creare una nuova applicazione Azure Active Directory e il servizio dell'entità che può essere utilizzato con il controllo di accesso basato sui ruoli. Se il codice che si sta usando deve accedere alle risorse o modificarle, è possibile creare un'identità per l'app. Questa identità è nota come entità servizio. È quindi possibile assegnare le autorizzazioni richieste all'entità servizio. Questo articolo illustra come usare il portale per creare l'entità servizio. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso delle identità gestite per le risorse di Azure per l'identità dell'applicazione. Se il codice viene eseguito in un servizio che supporta le identità gestite e accede alle risorse che supportano l'autenticazione di Azure AD, le identità gestite rappresentano un'opzione migliore. Per altre informazioni sulle identità gestite per le risorse di Azure, inclusi i servizi attualmente supportati, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Si passerà direttamente alla creazione dell'identità. Se si verifica un problema, controllare le [autorizzazioni necessarie](#required-permissions) per assicurarsi che l'account possa creare l'identità.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**.
1. Selezionare **Registrazioni per l'app**.

   ![Selezionare Registrazioni per l'app](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Selezionare **Registrazione nuova applicazione**.

   ![Aggiungere l'app](./media/howto-create-service-principal-portal/select-add-app.png)

1. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** come tipo di applicazione da creare. Non è possibile creare credenziali per un'[applicazione nativa](../manage-apps/application-proxy-configure-native-client-application.md) e non è possibile usare questo tipo per creare un'applicazione automatica. Dopo aver impostato i valori selezionare **Crea**.

   ![Assegnare un nome all'applicazione](./media/howto-create-service-principal-portal/create-app.png)

Sono state create un'applicazione e un'entità servizio di Azure AD.

## <a name="assign-the-application-to-a-role"></a>Assegnare l'applicazione a un ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo offre le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Tutti i servizi** e **Sottoscrizioni**.

   ![Selezionare la sottoscrizione](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selezionare la sottoscrizione specifica a cui assegnare l'applicazione.

   ![Selezionare la sottoscrizione per l'assegnazione](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se la sottoscrizione che si sta cercando non viene visualizzata, selezionare il **filtro per le sottoscrizioni globali**. Assicurarsi che la sottoscrizione desiderata sia selezionata per il portale. 

1. Selezionare **Controllo di accesso (IAM)**.
1. Selezionare **Aggiungi assegnazione ruolo**.

   ![Selezionare aggiungi assegnazione ruolo](./media/howto-create-service-principal-portal/select-add.png)

1. Selezionare il ruolo che si desidera assegnare all'applicazione. Per consentire all'applicazione di eseguire azioni quali istanze di **riavvio**, **avvio** e **arresto**, selezionare il ruolo **Collaboratore**. Per impostazione predefinita, le applicazioni di Azure AD non sono visualizzate nelle opzioni disponibili. Per trovare l'applicazione, cercare il nome e selezionarlo.

   ![Selezionare il ruolo](./media/howto-create-service-principal-portal/select-role.png)

1. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

L'entità servizio è stata configurata ed è possibile iniziare a usarla per eseguire script o app. Nella sezione successiva viene illustrato come ottenere i valori necessari quando si accede a livello di codice.

## <a name="get-values-for-signing-in"></a>Ottenere i valori per l'accesso

### <a name="get-tenant-id"></a>Ottenere l'ID tenant

Quando si accede a livello di codice, è necessario specificare l'ID tenant con la richiesta di autenticazione.

1. Selezionare **Azure Active Directory**.
1. Selezionare **Proprietà**.

   ![Selezionare le proprietà di Azure AD](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. Copiare l'**ID directory** per ottenere l'ID tenant.

   ![ID tenant](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Ottenere l'ID applicazione e la chiave di autenticazione

Sono necessari anche l'ID dell'applicazione e una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni app** in Azure AD selezionare l'applicazione.

   ![Selezionare l'applicazione](./media/howto-create-service-principal-portal/select-app.png)

1. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione.

   ![ID client](./media/howto-create-service-principal-portal/copy-app-id.png)

1. Selezionare **Impostazioni**.

   ![Selezionare Impostazioni](./media/howto-create-service-principal-portal/select-settings.png)

1. Selezionare **Chiavi**.
1. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

   ![Salvare le chiavi](./media/howto-create-service-principal-portal/save-key.png)

   Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Il valore della chiave sarà fornito insieme all'ID applicazione per eseguire l'accesso come applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

   ![Chiave salvata](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Autorizzazioni necessarie

È necessario disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure.

### <a name="check-azure-ad-permissions"></a>Controllare le autorizzazioni di Azure AD

1. Selezionare **Azure Active Directory**.
1. Annotare il proprio ruolo. Se si ha il ruolo **Utente**, assicurarsi che anche gli utenti non amministratori possano registrare le applicazioni.

   ![Trovare un utente](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selezionare **Impostazioni utente**.

   ![Selezionare Impostazioni utente](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Controllare l'impostazione **Registrazioni per l'app**. Questo valore può essere impostato solo da un amministratore. Se è impostato su **Sì**, qualsiasi utente nel tenant di Azure AD può registrare un'app.

   ![Visualizzare le registrazioni dell'app](./media/howto-create-service-principal-portal/view-app-registrations.png)

Se l'impostazione relativa alle registrazioni dell'app è impostata su **No**, solo gli utenti con un ruolo di amministratore possono registrare questi tipi di applicazioni. Vedere [Ruoli disponibili](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [Autorizzazioni dei ruoli](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) per informazioni sui ruoli di amministratore disponibili e le specifiche autorizzazioni di Azure AD assegnate a ogni ruolo. Se l'account è assegnato al ruolo Utente, ma l'impostazione relativa alle registrazioni dell'app è limitata agli utenti amministratori, chiedere al proprio amministratore di essere assegnati a uno dei ruoli di amministratore in grado di gestire tutti gli aspetti delle registrazioni dell'app oppure di consentire agli utenti di registrare le app.

### <a name="check-azure-subscription-permissions"></a>Controllare le autorizzazioni di sottoscrizione di Azure

Nella sottoscrizione di Azure è necessario che l'account disponga dell'accesso `Microsoft.Authorization/*/Write` per assegnare un'app di Active Directory a un ruolo. Questa azione è concessa tramite il ruolo [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o [Amministratore accessi utente](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se il proprio account è assegnato al ruolo **Collaboratore**, non si dispone dell'autorizzazione appropriata. Se si tenterà di assegnare l'entità servizio a un ruolo si riceve un errore.

Per controllare le proprie autorizzazioni di sottoscrizione:

1. Selezionare il proprio account nell'angolo superiore destro e quindi **Autorizzazioni personali**.

   ![Selezionare le autorizzazioni utente](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Nell'elenco a discesa selezionare la sottoscrizione in cui si vuole creare l'entità servizio. Selezionare quindi **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.

   ![Trovare un utente](./media/howto-create-service-principal-portal/view-details.png)

1. Visualizzare i propri ruoli assegnati e determinare se si dispone delle autorizzazioni adeguate per assegnare un'app di Active Directory a un ruolo. In caso contrario chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente. Nella figura seguente l'utente è assegnato al ruolo Proprietario, perciò dispone delle autorizzazioni adeguate.

   ![Visualizzare le autorizzazioni](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/role-assignments-portal.md).  
* Per un elenco di azioni disponibili che è possibile concedere o negare agli utenti, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
