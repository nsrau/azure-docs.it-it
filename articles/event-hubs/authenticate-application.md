---
title: Autenticare un'applicazione per accedere alle risorse di Hub eventi di AzureAuthenticate an application to access Azure Event Hubs resources
description: Questo articolo fornisce informazioni sull'autenticazione di un'applicazione con Azure Active Directory per accedere alle risorse di Hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520988"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticare un'applicazione con Azure Active Directory per accedere alle risorse di Hub eventiAuthenticate an application with Azure Active Directory to access Event Hubs resources
Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basata su Azure Active Directory (Azure AD). Un vantaggio chiave dell'uso di Azure AD con hub eventi di Azure è che non è più necessario archiviare le credenziali nel codice. Al contrario, è possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma Microsoft Identity.Instead, you can request an OAuth 2.0 access token from the Microsoft Identity platform. Il nome della risorsa `https://eventhubs.azure.net/` per richiedere un token è (per i `https://<namespace>.servicebus.windows.net`client Kafka, la risorsa per richiedere un token è ). Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse di Hub eventi di Azure.If the authentication succeeds, Azure AD returns an access token to the application, and the application can then use the access token to authorize request to Azure Event Hubs resources.

Quando un ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso può essere definito in base al livello di sottoscrizione, al gruppo di risorse, allo spazio dei nomi Hub eventi o a qualsiasi risorsa al di sotto di esso. Una sicurezza di Azure AD può assegnare ruoli a un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per](../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security can assign roles to a user, a group, an application service principal, or a managed identity for Azure resources. 

> [!NOTE]
> Una definizione di ruolo è una raccolta di autorizzazioni, Il controllo degli accessi in base al ruolo controlla la modalità di applicazione di queste autorizzazioni tramite l'assegnazione dei ruoli. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Per ulteriori informazioni, vedere [Informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Ruoli predefiniti per hub eventi di AzureBuilt-in roles for Azure Event Hubs
Azure offre i ruoli RBAC predefiniti seguenti per autorizzare l'accesso ai dati di Hub eventi usando Azure AD e OAuth:

- [Proprietario dei dati di Hub](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)eventi di Azure: usare questo ruolo per concedere l'accesso completo alle risorse di Hub eventi.
- [Mittente dati hub eventi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)di Azure: usare questo ruolo per concedere l'accesso all'invio alle risorse di Hub eventi.
- [Ricevitore dati Hub di azure:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)usare questo ruolo per concedere l'accesso alla ricezione alle risorse di Hub eventi.   

> [!IMPORTANT]
> La nostra versione di anteprima supportava l'aggiunta di privilegi di accesso ai dati di Event Hubs al ruolo Proprietario o Collaboratore. Tuttavia, i privilegi di accesso ai dati per il ruolo Proprietario e Collaboratore non vengono più rispettati. Se si usa il ruolo Proprietario o Collaboratore, passare al ruolo Proprietario dati hub eventi di Azure.If you are using the Owner or Contributor role, switch to using the Azure Event Hubs Data Owner role.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare ruoli RBAC tramite il portale di AzureAssign RBAC roles using the Azure portal  
Per altre informazioni sulla gestione dell'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo e il portale di Azure, vedere [questo articolo.](..//role-based-access-control/role-assignments-portal.md) 

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure.After you've determined the appropriate scope for a role assignment, navigate to that resource in the Azure portal. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> I passaggi descritti di seguito assegnano un ruolo all'hub eventi negli spazi dei nomi Hub eventi, ma è possibile seguire la stessa procedura per assegnare un ruolo con ambito a qualsiasi risorsa Hub eventi.

1. Nel [portale di Azure](https://portal.azure.com/)passare allo spazio dei nomi Hub eventi.
2. Nella pagina **Panoramica** selezionare l'hub eventi per il quale si desidera assegnare un ruolo.

    ![Selezionare l'hub eventi](./media/authenticate-application/select-event-hub.png)
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'hub eventi. 
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo. Selezionare il pulsante **Aggiungi** sulla barra degli strumenti, quindi selezionare **Aggiungi assegnazione ruolo**. 

    ![Pulsante Aggiungi sulla barra degli strumenti](./media/authenticate-application/role-assignments-add-button.png)
1. Nella pagina **Aggiungi assegnazione ruolo** eseguire la procedura seguente:On the Add role assignment page, do the following steps:
    1. Selezionare il **ruolo Hub eventi** che si desidera assegnare. 
    1. Eseguire la ricerca per individuare **l'entità** di sicurezza (utente, gruppo, entità servizio) a cui si desidera assegnare il ruolo.
    1. Selezionare **Salva** per salvare l'assegnazione di ruolo. 

        ![Assegnare un ruolo a un utenteAssign role to a user](./media/authenticate-application/assign-role-to-user.png)
    4. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che gli utenti di Azure si trova nel ruolo Proprietario dati hub eventi di Azure.For example, the following image shows that Azure-users is in the Azure Event Hubs Data Owner role. 
        
        ![Utente nell'elenco](./media/authenticate-application/user-in-list.png)

È possibile seguire passaggi simili per assegnare un ruolo con ambito allo spazio dei nomi, al gruppo di risorse o alla sottoscrizione di Hub eventi. Dopo aver definito il ruolo e il relativo ambito, è possibile testare questo comportamento con esempi [in questo percorso GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Eseguire l'autenticazione da un'applicazione
Un vantaggio chiave dell'uso di Azure AD con gli hub eventi è che le credenziali non devono più essere archiviate nel codice. Al contrario, è possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma di identità Microsoft.Instead, you can request an OAuth 2.0 access token from Microsoft identity platform. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste agli hub eventi di Azure.If authentication succeeds, Azure AD returns the access token to the application, and the application can then use the access token to authorize requests to Azure Event Hubs.

Nelle sezioni seguenti viene illustrato come configurare l'applicazione nativa o l'applicazione Web per l'autenticazione con la piattaforma di identità Microsoft 2.0.Following sections shows you how to configure your native application or web application for authentication with Microsoft identity platform 2.0. Per ulteriori informazioni sulla piattaforma di identità Microsoft 2.0, vedere Panoramica della piattaforma di [identità Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD
Il primo passaggio nell'uso di Azure AD per autorizzare le risorse di Hub eventi consiste nella registrazione dell'applicazione client con un tenant di Azure AD dal portale di [Azure.](https://portal.azure.com/) Quando si registra l'applicazione client, si forniscono informazioni sull'applicazione ad Active Directory. Azure AD fornisce quindi un ID client (denominato anche ID applicazione) che è possibile usare per associare l'applicazione al runtime di Azure AD. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Le immagini seguenti illustrano i passaggi per la registrazione di un'applicazione Web:

![Registrare un'applicazione](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come URI di reindirizzamento. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido, perché specifica l'URL in cui vengono forniti i token.

Dopo aver registrato l'applicazione, **l'ID dell'applicazione (client)** verrà visualizzato in **Impostazioni:**

![ID applicazione dell'applicazione registrata](./media/authenticate-application/application-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Creare un segreto client   
L'applicazione necessita di un segreto client per dimostrare la propria identità quando si richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura.

1. Passare alla registrazione dell'app nel portale di Azure.Navigate to your app registration in the Azure portal.
1. Selezionare l'impostazione **Certificati & segreti.**
1. In **Segreti client**selezionare Nuovo **segreto client** per creare un nuovo segreto.
1. Fornire una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in un luogo sicuro. Il valore di riempimento viene visualizzato una sola volta.

    ![Segreto client](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token  
Dopo aver registrato l'applicazione e concesso le autorizzazioni per inviare/ricevere dati negli hub eventi di Azure, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire il token OAuth 2.0.Once you've registered your application and granted it permissions to send/receive data in Azure Event Hubs, you can add code to your application to authenticate a security principal and acquire OAuth 2.0 token. Per autenticare e acquisire il token, è possibile utilizzare una delle librerie di autenticazione della piattaforma di [identità Microsoft](../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID o Connect 1.0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta per gli hub eventi di Azure.Your application can then use the access token to authorize a request against Azure Event Hubs.

Per un elenco degli scenari per i quali è supportata l'acquisizione di token, vedere la sezione [Scenari](https://aka.ms/msal-net-scenarios) della libreria di [autenticazione Microsoft (MSAL) per il](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repository GitHub .NET.

## <a name="samples"></a>Esempi
- Esempi di [Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Questi esempi usano la vecchia libreria **Microsoft.Azure.EventHubs,** ma è possibile aggiornarla facilmente usando la libreria **Azure.Messaging.EventHubs** più recente. Per spostare l'esempio dall'uso della libreria precedente a una nuova, vedere la [Guida per eseguire la migrazione da Microsoft.Azure.EventHubs ad Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Esempi di Azure.Messaging.EventHubsAzure.Messaging.EventHubs samples](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Questo esempio è stato aggiornato per usare la libreria **Azure.Messaging.EventHubs** più recente.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../role-based-access-control/overview.md)il controllo degli accessi in base al ruolo?
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo con i modelli di Azure Resource ManagerManage role-based access control (RBAC) with Azure Resource Manager Templates](../role-based-access-control/role-assignments-template.md)

Vedere i seguenti articoli correlati:
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources](authenticate-managed-identity.md)
- [Autenticare le richieste agli hub eventi di Azure usando le firme di accesso condivisoAuthenticate requests to Azure Event Hubs using Shared Access Signatures](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizzare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthorize access to Event Hubs resources using Shared Access Signatures](authorize-access-shared-access-signature.md)

