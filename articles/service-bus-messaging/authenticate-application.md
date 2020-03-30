---
title: Autenticare un'applicazione per accedere alle entità del bus di servizio di AzureAuthenticate an application to access Azure Service Bus entities
description: Questo articolo fornisce informazioni sull'autenticazione di un'applicazione con Azure Active Directory per accedere alle entità del bus di servizio di Azure (code, argomenti e così via).
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259292"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autenticare e autorizzare un'applicazione con Azure Active Directory per accedere alle entità del bus di servizio di AzureAuthenticate and authorize an application with Azure Active Directory to access Azure Service Bus entities
Il bus di servizio di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle entità del bus di servizio (code, argomenti, sottoscrizioni o filtri). Con Azure AD è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni a un'entità di sicurezza, che può essere un'entità servizio utente, un gruppo o un'applicazione. Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [Understanding the different roles](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un'entità del bus di servizio, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2.0.First, the security principal's identity is authenticated, and an OAuth 2.0 token is returned. Il nome della risorsa `https://servicebus.azure.net`per richiedere un token è .
 1. Successivamente, il token viene passato come parte di una richiesta al servizio bus di servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio una macchina virtuale di Azure, un set di scalabilità di macchine virtuali o un'app Di Azure Function, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita al servizio bus di servizio, vedere [Autenticare l'accesso alle risorse](service-bus-managed-service-identity.md)del bus di servizio di Azure con Azure Active Directory e le identità gestite per le risorse di Azure. 

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli RBAC all'entità di sicurezza. Il bus di servizio di Azure fornisce ruoli RBAC che includono set di autorizzazioni per le risorse del bus di servizio. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni di cui l'entità disticherà. Per altre informazioni sull'assegnazione di ruoli RBAC al bus di servizio di Azure, vedere Ruoli RBAC incorporati per il bus di servizio di Azure.To learn more about assigning RBAC roles to Azure Service Bus, see [Built-in RBAC roles for Azure Service Bus.](#built-in-rbac-roles-for-azure-service-bus) 

Le applicazioni native e le applicazioni Web che effettuano richieste al bus di servizio possono anche autorizzare con Azure AD. Questo articolo illustra come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse del bus di servizio. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione di ruoli RBAC per i diritti di accessoAssigning RBAC roles for access rights
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Il bus di servizio di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usati per accedere alle entità del bus di servizio ed è inoltre possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso può essere il livello di sottoscrizione, il gruppo di risorse o lo spazio dei nomi del bus di servizio. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per](../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, a group, an application service principal, or a managed identity for Azure resources.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ruoli RBAC incorporati per il bus di servizio di AzureBuilt-in RBAC roles for Azure Service Bus
Per il bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API Gestione risorse di Azure è già protetto mediante il modello di *Controllo degli accessi in base al ruolo*. Azure offre i ruoli RBAC predefiniti seguenti per autorizzare l'accesso a uno spazio dei nomi del bus di servizio:Azure provides the below built-in RBAC roles for authorizing access to a Service Bus namespace:

- [Proprietario dei dati del bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)di servizio di Azure: consente l'accesso ai dati allo spazio dei nomi del bus di servizio e alle relative entità (code, argomenti, sottoscrizioni e filtri)Azure Service Bus Data Owner : Enables data access to Service Bus namespace and its entities (queues, topics, subscriptions, and filters)
- Mittente dei dati del bus di [servizio di Azure:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)usare questo ruolo per concedere l'accesso all'invio allo spazio dei nomi del bus di servizio e alle relative entità.
- [Ricevitore di dati](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)del bus di servizio di Azure: usare questo ruolo per concedere l'accesso alla ricezione dello spazio dei nomi del bus di servizio e delle relative entità. 

## <a name="resource-scope"></a>Ambito della risorsa 
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che l'entità di sicurezza deve disporre. Le procedure consigliate stabiliscono che è sempre consigliabile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in cui è possibile definire l'ambito dell'accesso alle risorse del bus di servizio, a partire dall'ambito più ristretto:

- **Coda**, **argomento**o **sottoscrizione**: l'assegnazione di ruolo si applica all'entità del bus di servizio specifica. Attualmente, il portale di Azure non supporta l'assegnazione di utenti/gruppi/identità gestite ai ruoli RBAC del bus di servizio a livello di sottoscrizione. 
- **Spazio dei nomi del bus**di servizio: l'assegnazione di ruolo si estende sull'intera topologia del bus di servizio nello spazio dei nomi e sul gruppo di consumer associato.
- **Gruppo di risorse:** l'assegnazione dei ruoli si applica a tutte le risorse del bus di servizio nel gruppo di risorse.
- **Sottoscrizione:** l'assegnazione del ruolo si applica a tutte le risorse del bus di servizio in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> Tenere presente che la propagazione delle assegnazioni di ruolo RBAC può richiedere fino a cinque minuti. 

Per ulteriori informazioni sulla modalità di definizione dei ruoli predefiniti, vedere [Informazioni sulle definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)di ruolo . Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure.For information about creating custom RBAC roles, see [Create custom roles for Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare ruoli RBAC tramite il portale di AzureAssign RBAC roles using the Azure portal  
Per altre informazioni sulla gestione dell'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo e il portale di Azure, vedere [questo articolo.](..//role-based-access-control/role-assignments-portal.md) 

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure.After you've determined the appropriate scope for a role assignment, navigate to that resource in the Azure portal. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> I passaggi descritti di seguito assegnano un ruolo allo spazio dei nomi del bus di servizio. È possibile seguire la stessa procedura per assegnare un ruolo ad altri ambiti supportati (gruppo di risorse, sottoscrizione e così via).

1. Nel [portale di Azure](https://portal.azure.com/)passare allo spazio dei nomi del bus di servizio. Selezionare Controllo di accesso **(IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per lo spazio dei nomi. Se è necessario creare uno spazio dei nomi del bus di servizio, seguire le istruzioni riportate in questo articolo: Creare uno spazio dei nomi di [messaggistica del bus](service-bus-create-namespace-portal.md)di servizio .

    ![Selezionare Controllo di accesso nel menu a sinistra](./media/authenticate-application/select-access-control-menu.png)
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo. Selezionare il pulsante **Aggiungi** sulla barra degli strumenti, quindi selezionare **Aggiungi assegnazione ruolo**. 

    ![Pulsante Aggiungi sulla barra degli strumenti](./media/authenticate-application/role-assignments-add-button.png)
1. Nella pagina **Aggiungi assegnazione ruolo** eseguire la procedura seguente:On the Add role assignment page, do the following steps:
    1. Selezionare il **ruolo Bus di servizio** che si desidera assegnare. 
    1. Eseguire la ricerca per individuare **l'entità** di sicurezza (utente, gruppo, entità servizio) a cui si desidera assegnare il ruolo.
    1. Selezionare **Salva** per salvare l'assegnazione di ruolo. 

        ![Assegnare un ruolo a un utenteAssign role to a user](./media/authenticate-application/assign-role-to-user.png)
    4. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che Azure-users è nel ruolo Proprietario dati bus di servizio di Azure.For example, the following image shows that Azure-users is in the Azure Service Bus Data Owner role. 
        
        ![Utente nell'elenco](./media/authenticate-application/user-in-list.png)

È possibile seguire passaggi simili per assegnare un ruolo con ambito a un gruppo di risorse o a una sottoscrizione. Dopo aver definito il ruolo e il relativo ambito, è possibile testare questo comportamento con gli [esempi in GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Eseguire l'autenticazione da un'applicazione
Un vantaggio chiave dell'uso di Azure AD con il bus di servizio è che le credenziali non devono più essere archiviate nel codice. Al contrario, è possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma di identità Microsoft.Instead, you can request an OAuth 2.0 access token from Microsoft identity platform. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste al bus di servizio di Azure.If authentication succeeds, Azure AD returns the access token to the application, and the application can then use the access token to authorize requests to Azure Service Bus.

Nelle sezioni seguenti viene illustrato come configurare l'applicazione nativa o l'applicazione Web per l'autenticazione con la piattaforma di identità Microsoft 2.0.Following sections shows you how to configure your native application or web application for authentication with Microsoft identity platform 2.0. Per ulteriori informazioni sulla piattaforma di identità Microsoft 2.0, vedere Panoramica della piattaforma di [identità Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD
Il primo passaggio nell'uso di Azure AD per autorizzare le entità del bus di servizio è la registrazione dell'applicazione client con un tenant di Azure AD dal portale di Azure.The first step in using Azure AD to authorize Service Bus entities is registering your client application with an Azure AD tenant from the [Azure portal](https://portal.azure.com/). Quando si registra l'applicazione client, si forniscono informazioni sull'applicazione ad Active Directory. Azure AD fornisce quindi un ID client (denominato anche ID applicazione) che è possibile usare per associare l'applicazione al runtime di Azure AD. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Le immagini seguenti illustrano i passaggi per la registrazione di un'applicazione Web:

![Registrare un'applicazione](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come URI di reindirizzamento. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido, perché specifica l'URL in cui vengono forniti i token.

Dopo aver registrato l'applicazione, **l'ID dell'applicazione (client)** verrà visualizzato in **Impostazioni:**

![ID applicazione dell'applicazione registrata](./media/authenticate-application/application-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Prendere nota del **TenantId** e **ApplicationId**. Questi valori sono necessari per eseguire l'applicazione.

### <a name="create-a-client-secret"></a>Creare un segreto client   
L'applicazione necessita di un segreto client per dimostrare la propria identità quando si richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura.

1. Passare alla registrazione dell'app nel portale di Azure se non si è già presenti nella pagina.
1. Selezionare **Certificati & segreti** nel menu a sinistra.
1. In **Segreti client**selezionare Nuovo **segreto client** per creare un nuovo segreto.

    ![Nuovo segreto client - pulsante](./media/authenticate-application/new-client-secret-button.png)
1. Specificare una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato e quindi selezionare **Aggiungi**.

    ![Pagina Aggiungi segreto client](./media/authenticate-application/add-client-secret-page.png)
1. Copiare immediatamente il valore del nuovo segreto in un luogo sicuro. Il valore di riempimento viene visualizzato una sola volta.

    ![Segreto client](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Autorizzazioni per l'API del bus di servizio
Se l'applicazione è un'applicazione console, è necessario registrare un'applicazione nativa e aggiungere le autorizzazioni API per **Microsoft.ServiceBus** al set **di autorizzazioni richiesto.** Le applicazioni native richiedono anche un **URI di reindirizzamento** in Azure AD, che funge da identificatore. non è necessario che l'URI sia una destinazione di rete. Per questo esempio usare `https://servicebus.microsoft.com`, poiché questo URI è già usato nel codice di esempio.

### <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token  
Dopo aver registrato l'applicazione e concesso le autorizzazioni per inviare/ricevere dati nel bus di servizio di Azure, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire il token OAuth 2.0. Per autenticare e acquisire il token, è possibile utilizzare una delle librerie di autenticazione della piattaforma di [identità Microsoft](../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID o Connect 1.0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta sul bus di servizio di Azure.Your application can then use the access token to authorize a request against Azure Service Bus.

Per un elenco degli scenari per i quali è supportata l'acquisizione di token, vedere la sezione [Scenari](https://aka.ms/msal-net-scenarios) della libreria di [autenticazione Microsoft (MSAL) per il](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repository GitHub .NET.

## <a name="sample-on-github"></a>Esempio in GitHub
Vedere l'esempio seguente su GitHub: controllo di accesso in base al [ruolo per il bus di](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)servizio . 

Utilizzare l'opzione **Accesso segreto client,** non l'opzione **Accesso utente interattivo.** Quando si utilizza l'opzione del segreto client, non viene visualizzata una finestra popup. L'applicazione utilizza l'ID tenant e l'ID app per l'autenticazione. 

### <a name="run-the-sample"></a>Eseguire l'esempio

Prima di eseguire l'esempio, modificare il file **app.config** e, a seconda dello scenario, impostare i valori seguenti:

- `tenantId`: impostare sul valore di **TenantId**.
- `clientId`: impostare sul valore di **ApplicationId**.
- `clientSecret`: se si vuole accedere usando il segreto client, crearlo in Azure AD. Usare inoltre un'API o un'app Web al posto di un'app nativa. Aggiungere anche l'app in **Controllo di accesso (IAM)** nello spazio dei nomi creato in precedenza.
- `serviceBusNamespaceFQDN`: impostare sul nome DNS completo dello spazio dei nomi del bus di servizio appena creato, ad esempio `example.servicebus.windows.net`.
- `queueName`: impostare sul nome della coda creata.
- URI di reindirizzamento specificato nell'app nei passaggi precedenti.

Quando si esegue l'applicazione console, viene richiesto di selezionare uno scenario. Selezionare **Accesso utente interattivo** digitandone il numero e premendo INVIO. L'applicazione visualizza una finestra di accesso, chiede il consenso per l'accesso al bus di servizio e quindi usa il servizio per eseguire lo scenario di invio e ricezione mediante l'identità dell'account di accesso.


## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../role-based-access-control/overview.md)il controllo degli accessi in base al ruolo?
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo con i modelli di Azure Resource ManagerManage role-based access control (RBAC) with Azure Resource Manager Templates](../role-based-access-control/role-assignments-template.md)

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

- [Esempi di controllo degli accessi in base al ruolo del bus di servizio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
- [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
