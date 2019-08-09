---
title: Creare un'identità per un'app Azure nel portale | Documentazione Microsoft
description: Descrive come creare una nuova applicazione ed entità servizio di Azure Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a28354f54978e8ba776d8b0da294652ff462a05f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853449"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Procedura: usare il portale per creare un'applicazione Azure AD e un'entità servizio che possano accedere alle risorse

Questo articolo illustra come creare una nuova applicazione Azure Active Directory (Azure AD) e un'entità servizio che possono essere usate con il controllo degli accessi in base al ruolo. Se il codice che si sta usando deve accedere alle risorse o modificarle, è possibile creare un'identità per l'app. Questa identità è nota come entità servizio. È quindi possibile assegnare le autorizzazioni richieste all'entità servizio. Questo articolo illustra come usare il portale per creare l'entità servizio. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso delle identità gestite per le risorse di Azure per l'identità dell'applicazione. Se il codice viene eseguito in un servizio che supporta identità gestite e accede a risorse che supportano l'autenticazione Azure AD, le identità gestite rappresentano un'opzione migliore. Per altre informazioni sulle identità gestite per le risorse di Azure, inclusi i servizi attualmente supportati, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Si passerà direttamente alla creazione dell'identità. Se si verifica un problema, controllare le [autorizzazioni necessarie](#required-permissions) per assicurarsi che l'account possa creare l'identità.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Assegnare un nome all'applicazione. Selezionare un tipo di account supportato, che determina chi può usare l'applicazione. In **URI di reindirizzamento**selezionare **Web** per il tipo di applicazione che si desidera creare. Immettere l'URI a cui viene inviato il token di accesso. Non è possibile creare credenziali per un'[applicazione nativa](../manage-apps/application-proxy-configure-native-client-application.md) e non è possibile usare questo tipo per creare un'applicazione automatica. Dopo aver impostato i valori, selezionare **Register (registra**).

   ![Digitare un nome per l'applicazione](./media/howto-create-service-principal-portal/create-app.png)

Sono state create un'applicazione e un'entità servizio di Azure AD.

## <a name="assign-the-application-to-a-role"></a>Assegnare l'applicazione a un ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo offre le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Tutti i servizi** e **Sottoscrizioni**.

   ![Ad esempio, assegnare un ruolo nell'ambito della sottoscrizione](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selezionare la sottoscrizione specifica a cui assegnare l'applicazione.

   ![Selezionare la sottoscrizione per l'assegnazione](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se la sottoscrizione che si sta cercando non viene visualizzata, selezionare il **filtro per le sottoscrizioni globali**. Assicurarsi che la sottoscrizione desiderata sia selezionata per il portale.

1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi assegnazione di ruolo**.
1. Selezionare il ruolo che si desidera assegnare all'applicazione. Per consentire all'applicazione di eseguire azioni quali istanze di **riavvio**, **avvio** e **arresto**, selezionare il ruolo **Collaboratore**. Per impostazione predefinita, le applicazioni di Azure AD non sono visualizzate nelle opzioni disponibili. Per trovare l'applicazione, cercare il nome e selezionarlo.

   ![Selezionare il ruolo da assegnare all'applicazione](./media/howto-create-service-principal-portal/select-role.png)

1. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

L'entità servizio è stata configurata ed è possibile iniziare a usarla per eseguire script o app. Nella sezione successiva viene illustrato come ottenere i valori necessari quando si accede a livello di codice.

## <a name="get-values-for-signing-in"></a>Ottenere i valori per l'accesso

Quando si accede a livello di codice, è necessario specificare l'ID tenant con la richiesta di autenticazione. Sono necessari anche l'ID dell'applicazione e una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Selezionare **Azure Active Directory**.
1. Da **Registrazioni app** in Azure AD selezionare l'applicazione.
1. Copiare l'ID directory (tenant) e archiviarlo nel codice dell'applicazione.

    ![Copiare il valore della directory (ID tenant) e archiviarla nel codice dell'app](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione.

   ![Copiare il valore di ID applicazione (client)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificati e segreti
Le applicazioni daemon possono usare due forme di credenziali per eseguire l'autenticazione con Azure AD: certificati e segreti dell'applicazione.  È consigliabile usare un certificato, ma è anche possibile creare un nuovo segreto dell'applicazione.

### <a name="upload-a-certificate"></a>Carica un certificato

Se si dispone di un certificato, è possibile utilizzarne uno esistente.  Facoltativamente, è possibile creare un certificato autofirmato a scopo di test. Aprire PowerShell ed eseguire [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) con i parametri seguenti per creare un certificato autofirmato nell'archivio certificati utente del computer: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Esportare questo certificato usando lo snap-in MMC [Gestione certificati utente](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) accessibile dal pannello di controllo di Windows.

Per caricare il certificato:

1. Selezionare **Certificati e segreti**.
1. Selezionare **Carica certificato** e selezionare il certificato (un certificato esistente o il certificato autofirmato esportato).

    ![Selezionare Carica certificato e selezionare quello che si desidera aggiungere](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selezionare **Aggiungi**.

Dopo aver registrato il certificato con l'applicazione nel portale di registrazione delle applicazioni, è necessario abilitare il codice dell'applicazione client per l'uso del certificato.

### <a name="create-a-new-application-secret"></a>Crea nuovo segreto applicazione

Se si sceglie di non usare un certificato, è possibile creare un nuovo segreto dell'applicazione.

1. Selezionare **Certificati e segreti**.
1. Selezionare **Segreti client -> Nuovo segreto client**.
1. Specificare una descrizione del segreto e una durata. Al termine, fare clic su **Aggiungi**.

   Il valore del segreto client verrà visualizzato dopo il salvataggio. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Il valore della chiave sarà fornito insieme all'ID applicazione per eseguire l'accesso come applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

   ![Copiare il valore del segreto perché non sarà possibile recuperarlo in seguito](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Autorizzazioni necessarie

È necessario disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure.

### <a name="check-azure-ad-permissions"></a>Controllare le autorizzazioni di Azure AD

1. Selezionare **Azure Active Directory**.
1. Annotare il proprio ruolo. Se si ha il ruolo **Utente**, assicurarsi che anche gli utenti non amministratori possano registrare le applicazioni.

   ![Trovare il ruolo. Se si è un utente, assicurarsi che gli utenti non amministratori possano registrare le app](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selezionare **Impostazioni utente**.
1. Controllare l'impostazione **Registrazioni per l'app**. Questo valore può essere impostato solo da un amministratore. Se è impostato su **Sì**, qualsiasi utente nel tenant di Azure AD può registrare un'app.

Se l'impostazione relativa alle registrazioni dell'app è impostata su **No**, solo gli utenti con un ruolo di amministratore possono registrare questi tipi di applicazioni. Vedere [Ruoli disponibili](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [Autorizzazioni dei ruoli](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) per informazioni sui ruoli di amministratore disponibili e le specifiche autorizzazioni di Azure AD assegnate a ogni ruolo. Se l'account è assegnato al ruolo Utente, ma l'impostazione relativa alle registrazioni dell'app è limitata agli utenti amministratori, chiedere al proprio amministratore di essere assegnati a uno dei ruoli di amministratore in grado di gestire tutti gli aspetti delle registrazioni dell'app oppure di consentire agli utenti di registrare le app.

### <a name="check-azure-subscription-permissions"></a>Controllare le autorizzazioni di sottoscrizione di Azure

Nella sottoscrizione di Azure è necessario che l'account disponga dell'accesso `Microsoft.Authorization/*/Write` per assegnare un'app di Active Directory a un ruolo. Questa azione è concessa tramite il ruolo [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o [Amministratore accessi utente](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se il proprio account è assegnato al ruolo **Collaboratore**, non si dispone dell'autorizzazione appropriata. Se si tenterà di assegnare l'entità servizio a un ruolo si riceve un errore.

Per controllare le proprie autorizzazioni di sottoscrizione:

1. Selezionare l'account nell'angolo in alto a destra e selezionare **...-> autorizzazioni personali**.

   ![Selezionare l'account e le autorizzazioni utente](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Nell'elenco a discesa selezionare la sottoscrizione in cui si vuole creare l'entità servizio. Selezionare quindi **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.

   ![Selezionare la sottoscrizione in cui si vuole creare l'entità servizio](./media/howto-create-service-principal-portal/view-details.png)

1. Selezionare **assegnazioni di ruolo** per visualizzare i ruoli assegnati e determinare se si dispone di autorizzazioni adeguate per assegnare un'app ad a un ruolo. In caso contrario chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente. Nella figura seguente l'utente è assegnato al ruolo Proprietario, perciò dispone delle autorizzazioni adeguate.

   ![Questo esempio mostra che l'utente è assegnato al ruolo proprietario](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/role-assignments-portal.md).  
* Per un elenco di azioni disponibili che è possibile concedere o negare agli utenti, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
