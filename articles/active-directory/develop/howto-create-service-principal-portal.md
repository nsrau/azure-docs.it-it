---
title: Creare un'app Azure AD & entità servizio nel portale
titleSuffix: Microsoft identity platform
description: Creare una nuova Azure Active Directory app & entità servizio per gestire l'accesso alle risorse con il controllo degli accessi in base al ruolo in Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: d14e31aa4fbeb2d29137c554f14333e1617c484a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265902"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Procedure: Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse

Questo articolo illustra come creare una nuova applicazione Azure Active Directory (Azure AD) e un'entità servizio che possono essere usate con il controllo degli accessi in base al ruolo. Quando si dispone di applicazioni, servizi ospitati o strumenti automatici che devono accedere alle risorse o modificarle, è possibile creare un'identità per l'app. Questa identità è nota come entità servizio. L'accesso alle risorse è limitato dai ruoli assegnati all'entità servizio, garantendo il controllo sulle risorse a cui è possibile accedere e a quale livello. Per motivi di sicurezza è sempre consigliabile usare le identità servizio per gli strumenti automatici, invece di consentire loro di accedere con un'identità utente.

Questo articolo illustra come usare il portale per creare l'entità servizio nel portale di Azure. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.  È anche possibile [usare Azure PowerShell per creare un'entità servizio](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso delle identità gestite per le risorse di Azure per l'identità dell'applicazione. Se il codice viene eseguito in un servizio che supporta identità gestite e accede a risorse che supportano l'autenticazione Azure AD, le identità gestite rappresentano un'opzione migliore. Per altre informazioni sulle identità gestite per le risorse di Azure, inclusi i servizi attualmente supportati, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Registrazione dell'app, oggetti app ed entità servizio
Non è possibile creare direttamente un'entità servizio usando il portale di Azure.  Quando si registra un'applicazione tramite la portale di Azure, un oggetto applicazione e un'entità servizio vengono creati automaticamente nella home directory o nel tenant.  Per altre informazioni sulla relazione tra la registrazione dell'app, gli oggetti applicazione e le entità servizio, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Autorizzazioni necessarie per la registrazione di un'app

È necessario disporre di autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare all'applicazione un ruolo nella sottoscrizione di Azure.

### <a name="check-azure-ad-permissions"></a>Controllare le autorizzazioni di Azure AD

1. Selezionare **Azure Active Directory**.
1. Annotare il proprio ruolo. Se si ha il ruolo **Utente**, assicurarsi che anche gli utenti non amministratori possano registrare le applicazioni.

   ![Trovare il ruolo. Se si è un utente, assicurarsi che gli utenti non amministratori possano registrare le app](./media/howto-create-service-principal-portal/view-user-info.png)

1. Nel riquadro sinistro selezionare **impostazioni utente**.
1. Controllare l'impostazione **Registrazioni per l'app**. Questo valore può essere impostato solo da un amministratore. Se è impostato su **Sì**, qualsiasi utente nel tenant di Azure AD può registrare un'app.

Se l'impostazione relativa alle registrazioni dell'app è impostata su **No**, solo gli utenti con un ruolo di amministratore possono registrare questi tipi di applicazioni. Vedere [Ruoli disponibili](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [Autorizzazioni dei ruoli](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) per informazioni sui ruoli di amministratore disponibili e le specifiche autorizzazioni di Azure AD assegnate a ogni ruolo. Se l'account è assegnato al ruolo utente, ma l'impostazione di registrazione dell'app è limitata agli utenti amministratori, richiedere all'amministratore di assegnare uno dei ruoli di amministratore che possono creare e gestire tutti gli aspetti delle registrazioni di app o consentire agli utenti di registrare le app.

### <a name="check-azure-subscription-permissions"></a>Controllare le autorizzazioni di sottoscrizione di Azure

Nella sottoscrizione di Azure, l'account deve avere `Microsoft.Authorization/*/Write` accesso per assegnare un ruolo a un'app ad. Questa azione è concessa tramite il ruolo [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o [Amministratore accessi utente](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se al proprio account è assegnato il ruolo **collaboratore** , non si dispone delle autorizzazioni appropriate. Si riceverà un errore durante il tentativo di assegnare l'entità servizio a un ruolo.

Per controllare le proprie autorizzazioni di sottoscrizione:

1. Cercare e selezionare le **sottoscrizioni**oppure selezionare **sottoscrizioni** nella **Home** page.

   ![Ricerca](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selezionare la sottoscrizione in cui si desidera creare l'entità servizio.

   ![Selezionare la sottoscrizione per l'assegnazione](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se la sottoscrizione che si sta cercando non viene visualizzata, selezionare il **filtro per le sottoscrizioni globali**. Assicurarsi che la sottoscrizione desiderata sia selezionata per il portale.

1. Selezionare **Autorizzazioni personali**. Selezionare quindi **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.

   ![Selezionare la sottoscrizione in cui si vuole creare l'entità servizio](./media/howto-create-service-principal-portal/view-details.png)

1. Selezionare **Visualizza** nelle **assegnazioni di ruolo** per visualizzare i ruoli assegnati e determinare se si dispone delle autorizzazioni appropriate per assegnare un ruolo a un'app ad. In caso contrario chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente. Nell'immagine seguente all'utente viene assegnato il ruolo proprietario, il che significa che l'utente dispone di autorizzazioni appropriate.

   ![Questo esempio mostra che all'utente è assegnato il ruolo di proprietario](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Registrare un'applicazione con Azure AD e creare un'entità servizio

Si passerà direttamente alla creazione dell'identità. Se si verifica un problema, controllare le [autorizzazioni necessarie](#permissions-required-for-registering-an-app) per assicurarsi che l'account possa creare l'identità.

1. Accedere al proprio account Azure tramite il [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Assegnare un nome all'applicazione. Selezionare un tipo di account supportato, che determina chi può usare l'applicazione. In **URI di reindirizzamento**selezionare **Web** per il tipo di applicazione che si desidera creare. Immettere l'URI a cui viene inviato il token di accesso. Non è possibile creare credenziali per un'[applicazione nativa](../manage-apps/application-proxy-configure-native-client-application.md) e non è possibile usare questo tipo per creare un'applicazione automatica. Dopo aver impostato i valori, selezionare **Register (registra**).

   ![Digitare un nome per l'applicazione](./media/howto-create-service-principal-portal/create-app.png)

Sono state create un'applicazione e un'entità servizio di Azure AD.

## <a name="assign-a-role-to-the-application"></a>Assegnare un ruolo all'applicazione

Per accedere alle risorse nella sottoscrizione, è necessario assegnare un ruolo all'applicazione. Decidere quale ruolo offre le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Ad esempio, l'aggiunta di un'applicazione al ruolo *lettore* per un gruppo di risorse significa che può leggere il gruppo di risorse e tutte le risorse in esso contenute.

1. Nella portale di Azure selezionare il livello di ambito a cui si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo nell'ambito della sottoscrizione, cercare e selezionare le **sottoscrizioni**oppure selezionare **sottoscrizioni** nella **Home** page.

   ![Ad esempio, assegnare un ruolo nell'ambito della sottoscrizione](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selezionare la sottoscrizione specifica a cui assegnare l'applicazione.

   ![Selezionare la sottoscrizione per l'assegnazione](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se la sottoscrizione che si sta cercando non viene visualizzata, selezionare il **filtro per le sottoscrizioni globali**. Assicurarsi che la sottoscrizione desiderata sia selezionata per il portale.

1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. Selezionare il ruolo che si desidera assegnare all'applicazione. Ad esempio, per consentire all'applicazione di eseguire azioni come il **riavvio**, l' **avvio** e l' **arresto** delle istanze, selezionare il ruolo **collaboratore** .  Per altre informazioni sui [ruoli disponibili](../../role-based-access-control/built-in-roles.md) , vedere Azure ad le applicazioni non vengono visualizzate nelle opzioni disponibili. Per trovare l'applicazione, cercare il nome e selezionarlo.

   ![Selezionare il ruolo da assegnare all'applicazione](./media/howto-create-service-principal-portal/select-role.png)

1. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione viene visualizzata nell'elenco di utenti con un ruolo per tale ambito.

L'entità servizio è stata configurata ed è possibile iniziare a usarla per eseguire script o app. Per gestire l'entità servizio (autorizzazioni, autorizzazioni consentite dall'utente, vedere quali utenti hanno acconsentito, esaminare le autorizzazioni, vedere informazioni di accesso e altro), passare ad **applicazioni aziendali**.

Nella sezione successiva viene illustrato come ottenere i valori necessari quando si accede a livello di codice.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Ottenere i valori di ID tenant e app per l'accesso

Quando si esegue l'accesso a livello di codice, è necessario passare l'ID tenant con la richiesta di autenticazione e l'ID applicazione.  È necessario anche un certificato o una chiave di autenticazione, descritta nella sezione seguente. Per ottenere questi valori eseguire la procedura seguente:

1. Selezionare **Azure Active Directory**.
1. Da **Registrazioni app** in Azure AD selezionare l'applicazione.
1. Copiare l'ID directory (tenant) e archiviarlo nel codice dell'applicazione.

    ![Copiare il valore della directory (ID tenant) e archiviarla nel codice dell'app](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    L'ID directory (tenant) è disponibile anche nella pagina Panoramica directory predefinita.

1. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione.

   ![Copiare il valore di ID applicazione (client)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Autenticazione: due opzioni

Per le entità servizio sono disponibili due tipi di autenticazione: autenticazione basata su password (segreto applicazione) e autenticazione basata su certificati. È *consigliabile usare un certificato*, ma è anche possibile creare un segreto dell'applicazione.

### <a name="option-1-upload-a-certificate"></a>Opzione 1: caricare un certificato

Se si dispone di un certificato, è possibile utilizzarne uno esistente.  Facoltativamente, è possibile creare un certificato autofirmato *solo a scopo di test*. Per creare un certificato autofirmato, aprire PowerShell ed eseguire [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) con i parametri seguenti per creare il certificato nell'archivio certificati utente del computer:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Esportare questo certificato in un file usando lo snap-in MMC [Gestione certificati utente](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) accessibile dal pannello di controllo di Windows.

1. Selezionare **Esegui** dal menu **Start** , quindi immettere **certmgr. msc**.

   Viene visualizzato lo strumento Gestione certificati per l'utente corrente.

1. Per visualizzare i certificati, in **certificati-utente corrente** nel riquadro sinistro, espandere la directory **personale** .
1. Fare clic con il pulsante destro del mouse sul certificato creato, selezionare **tutte le attività->Esporta**.
1. Seguire l'esportazione guidata certificati.  Non esportare la chiave privata ed esportare in un. File CER.

Per caricare il certificato:

1. Selezionare **Azure Active Directory**.
1. Da **Registrazioni app** in Azure AD selezionare l'applicazione.
1. Selezionare **Certificati e segreti**.
1. Selezionare **Carica certificato** e selezionare il certificato (un certificato esistente o il certificato autofirmato esportato).

    ![Selezionare Carica certificato e selezionare quello che si desidera aggiungere](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selezionare **Aggiungi**.

Dopo aver registrato il certificato con l'applicazione nel portale di registrazione delle applicazioni, è necessario abilitare il codice dell'applicazione client per l'uso del certificato.

### <a name="option-2-create-a-new-application-secret"></a>Opzione 2: creare una nuova chiave privata dell'applicazione

Se si sceglie di non usare un certificato, è possibile creare un nuovo segreto dell'applicazione.

1. Selezionare **Azure Active Directory**.
1. Da **Registrazioni app** in Azure AD selezionare l'applicazione.
1. Selezionare **Certificati e segreti**.
1. Selezionare **Segreti client -> Nuovo segreto client**.
1. Specificare una descrizione del segreto e una durata. Al termine, fare clic su **Aggiungi**.

   Il valore del segreto client verrà visualizzato dopo il salvataggio. Copiare questo valore perché non sarà possibile recuperare la chiave in un secondo momento. Il valore della chiave sarà fornito con l'ID applicazione per accedere come applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

   ![Copiare il valore del segreto perché non sarà possibile recuperarlo in seguito](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configurare i criteri di accesso per le risorse
Tenere presente che potrebbe essere necessario configurare autorizzazioni aggiuntive per le risorse a cui l'applicazione deve accedere. Ad esempio, è necessario aggiornare anche i [criteri di accesso](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) dell'insieme di credenziali delle chiavi per consentire all'applicazione di accedere a chiavi, segreti o certificati.

1. Nella [portale di Azure](https://portal.azure.com)passare all'insieme di credenziali delle chiavi e selezionare **criteri di accesso**.
1. Selezionare **Aggiungi criteri di accesso**, quindi selezionare la chiave, il segreto e le autorizzazioni per i certificati per cui si vuole concedere l'applicazione.  Selezionare l'entità servizio creata in precedenza.
1. Selezionare **Aggiungi** per aggiungere i criteri di accesso, quindi **Salva** per eseguire il commit delle modifiche.
    ![Aggiungere un criterio di accesso](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare Azure PowerShell per creare un'entità servizio](howto-authenticate-service-principal-powershell.md).
* Per informazioni su come specificare i criteri di sicurezza, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/role-assignments-portal.md).
* Per un elenco di azioni disponibili che è possibile concedere o negare agli utenti, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
* Per informazioni sull'uso delle registrazioni di app con **Microsoft Graph**, vedere le informazioni di riferimento sulle API per le [applicazioni](/graph/api/resources/application) .
