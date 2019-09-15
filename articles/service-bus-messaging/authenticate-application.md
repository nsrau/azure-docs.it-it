---
title: Autenticare un'applicazione per accedere alle entità del bus di servizio di Azure
description: Questo articolo fornisce informazioni sull'autenticazione di un'applicazione con Azure Active Directory per accedere alle entità del bus di servizio di Azure (code, argomenti e così via)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996997"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autenticare e autorizzare un'applicazione con Azure Active Directory per accedere alle entità del bus di servizio di Azure
Il bus di servizio di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle entità del bus di servizio (code, argomenti, sottoscrizioni o filtri). Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un'entità del bus di servizio, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. Il nome della risorsa per richiedere un token `https://servicebus.azure.net`è.
 1. Successivamente, il token viene passato come parte di una richiesta al servizio del bus di servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione è in esecuzione in un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita al servizio del bus di servizio, vedere [autenticare l'accesso alle risorse del bus di servizio di Azure con Azure Active Directory e identità gestite per le risorse di Azure](service-bus-managed-service-identity.md). 

Il passaggio di autorizzazione richiede che uno o più ruoli RBAC siano assegnati all'entità di sicurezza. Il bus di servizio di Azure fornisce ruoli RBAC che includono i set di autorizzazioni per le risorse del bus di servizio. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sull'assegnazione di ruoli RBAC al bus di servizio di Azure, vedere [ruoli RBAC predefiniti per il bus di servizio di Azure](#built-in-rbac-roles-for-azure-service-bus). 

Le applicazioni native e le applicazioni Web che effettuano richieste al bus di servizio possono anche autorizzare con Azure AD. Questo articolo illustra come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse del bus di servizio. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione di ruoli RBAC per i diritti di accesso
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Il bus di servizio di Azure definisce un set di ruoli RBAC predefiniti che comprende i set comuni di autorizzazioni usate per accedere alle entità del bus di servizio ed è anche possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse o allo spazio dei nomi del bus di servizio. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ruoli RBAC predefiniti per il bus di servizio di Azure
Per il bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API Gestione risorse di Azure è già protetto mediante il modello di *Controllo degli accessi in base al ruolo*. Azure fornisce i ruoli RBAC predefiniti seguenti per autorizzare l'accesso a uno spazio dei nomi del bus di servizio:

- [Proprietario dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Consente l'accesso ai dati allo spazio dei nomi del bus di servizio e alle relative entità (code, argomenti, sottoscrizioni e filtri)
- [Mittente dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Usare questo ruolo per concedere l'accesso di trasmissione allo spazio dei nomi del bus di servizio e alle relative entità.
- [Ricevitore di dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Usare questo ruolo per concedere la ricezione dell'accesso allo spazio dei nomi del bus di servizio e alle relative entità. 

## <a name="resource-scope"></a>Ambito delle risorse 
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è sempre preferibile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in cui è possibile definire l'ambito di accesso alle risorse del bus di servizio, a partire dall'ambito più restrittivo:

- **Coda**, **argomento**o **sottoscrizione**: L'assegnazione di ruolo si applica all'entità specifica del bus di servizio. Attualmente, il portale di Azure non supporta l'assegnazione di utenti/gruppi/identità gestite ai ruoli RBAC del bus di servizio a livello di sottoscrizione. 
- **Spazio dei nomi del bus di servizio**: L'assegnazione di ruolo si estende all'intera topologia del bus di servizio nello spazio dei nomi e al gruppo di consumer associato.
- **Gruppo di risorse**: L'assegnazione di ruolo si applica a tutte le risorse del bus di servizio nel gruppo di risorse.
- **Sottoscrizione** L'assegnazione di ruolo si applica a tutte le risorse del bus di servizio in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione. 

Per ulteriori informazioni sulla definizione dei ruoli predefiniti, vedere informazioni sulle [definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)dei ruoli. Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere [creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare i ruoli RBAC usando il portale di Azure  
Per altre informazioni sulla gestione dell'accesso alle risorse di Azure con RBAC e la portale di Azure, vedere [questo articolo](..//role-based-access-control/role-assignments-portal.md). 

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> Nei passaggi descritti di seguito viene assegnato un ruolo allo spazio dei nomi del bus di servizio. È possibile seguire la stessa procedura per assegnare un ruolo ad altri ambiti supportati (gruppo di risorse, sottoscrizione e così via).

1. Nella [portale di Azure](https://portal.azure.com/)passare allo spazio dei nomi del bus di servizio. Selezionare **controllo di accesso (IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per lo spazio dei nomi. Se è necessario creare uno spazio dei nomi del bus di servizio, seguire le istruzioni fornite in questo articolo: [Creare uno spazio dei nomi di messaggistica del bus di servizio](service-bus-create-namespace-portal.md).

    ![Selezionare controllo di accesso nel menu a sinistra.](./media/authenticate-application/select-access-control-menu.png)
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo. Selezionare il pulsante **Aggiungi** sulla barra degli strumenti e quindi selezionare **Aggiungi assegnazione ruolo**. 

    ![Pulsante Aggiungi sulla barra degli strumenti](./media/authenticate-application/role-assignments-add-button.png)
1. Nella pagina **Aggiungi assegnazione ruolo** eseguire le operazioni seguenti:
    1. Selezionare il **ruolo del bus di servizio** che si vuole assegnare. 
    1. Eseguire una ricerca per individuare l' **entità di sicurezza** (utente, gruppo, entità servizio) a cui si desidera assegnare il ruolo.
    1. Selezionare **Save (Salva** ) per salvare l'assegnazione di ruolo. 

        ![Assegnare un ruolo a un utente](./media/authenticate-application/assign-role-to-user.png)
    4. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che Azure-Users si trova nel ruolo di proprietario dei dati del bus di servizio di Azure. 
        
        ![Utente nell'elenco](./media/authenticate-application/user-in-list.png)

È possibile seguire una procedura simile per assegnare un ruolo con ambito a un gruppo di risorse o a una sottoscrizione. Dopo aver definito il ruolo e il relativo ambito, è possibile testare questo comportamento con gli [esempi in GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Eseguire l'autenticazione da un'applicazione
Un vantaggio fondamentale dell'uso di Azure AD con il bus di servizio è che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste al bus di servizio di Azure.

Le sezioni seguenti illustrano come configurare un'applicazione nativa o un'applicazione Web per l'autenticazione con Microsoft Identity Platform 2,0. Per ulteriori informazioni sulla piattaforma Microsoft Identity Platform 2,0, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD
Il primo passaggio nell'uso di Azure AD per autorizzare entità del bus di servizio è la registrazione dell'applicazione client con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com/). Quando si registra l'applicazione client, è necessario fornire informazioni sull'applicazione ad Active Directory. Azure AD fornisce quindi un ID client (noto anche come ID applicazione) che è possibile usare per associare l'applicazione a Azure AD Runtime. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Nelle immagini seguenti vengono illustrati i passaggi per la registrazione di un'applicazione Web:

![Registra un'applicazione](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l'URI di reindirizzamento. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido perché specifica l'URL a cui vengono forniti i token.

Dopo aver registrato l'applicazione, l' **ID applicazione (client)** verrà visualizzato in **Impostazioni**:

![ID applicazione dell'applicazione registrata](./media/authenticate-application/application-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Prendere nota di **TenantId** e **ApplicationID**. Questi valori sono necessari per eseguire l'applicazione.

### <a name="create-a-client-secret"></a>Creare un segreto client   
L'applicazione richiede un segreto client per dimostrare la propria identità quando richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura.

1. Passare alla registrazione dell'app nel portale di Azure se non è già presente nella pagina.
1. Selezionare **certificati & segreti** nel menu a sinistra.
1. In **segreti client**selezionare **nuovo segreto client** per creare un nuovo segreto.

    ![Nuovo segreto client-pulsante](./media/authenticate-application/new-client-secret-button.png)
1. Specificare una descrizione per il segreto, scegliere l'intervallo di scadenza desiderato e quindi selezionare **Aggiungi**.

    ![Pagina Aggiungi segreto client](./media/authenticate-application/add-client-secret-page.png)
1. Copiare immediatamente il valore del nuovo segreto in una posizione sicura. Il valore di riempimento viene visualizzato una sola volta.

    ![Segreto client](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Autorizzazioni per l'API del bus di servizio
Se l'applicazione è un'applicazione console, è necessario registrare un'applicazione nativa e aggiungere le autorizzazioni API per **Microsoft. ServiceBus** al set di **autorizzazioni necessarie** . Le applicazioni native necessitano anche di un **URI di reindirizzamento** in Azure ad, che funge da identificatore; non è necessario che l'URI sia una destinazione di rete. Per questo esempio usare `https://servicebus.microsoft.com`, poiché questo URI è già usato nel codice di esempio.

### <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token  
Dopo aver registrato l'applicazione e concesso le autorizzazioni per l'invio e la ricezione di dati nel bus di servizio di Azure, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire il token OAuth 2,0. Per autenticare e acquisire il token, è possibile usare una delle librerie di [autenticazione della piattaforma di identità Microsoft](../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID o Connect 1,0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta sul bus di servizio di Azure.

Per un elenco degli scenari per i quali è supportato l'acquisizione di token, vedere la sezione [scenari](https://aka.ms/msal-net-scenarios) del repository [Microsoft Authentication Library (MSAL) per .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) github.

## <a name="sample-on-github"></a>Esempio in GitHub
Vedere l'esempio seguente su GitHub: [Controllo degli accessi in base al ruolo per il bus di servizio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Usare l'opzione di **accesso Secret client** , non l'opzione **interattiva User Login** . Quando si usa l'opzione client Secret (segreto client), non viene visualizzata una finestra popup. L'applicazione usa l'ID tenant e l'ID app per l'autenticazione. 

### <a name="run-the-sample"></a>Eseguire l'esempio

Prima di poter eseguire l'esempio, modificare il file **app. config** e, a seconda dello scenario, impostare i valori seguenti:

- `tenantId`: impostare sul valore di **TenantId**.
- `clientId`: impostare sul valore di **ApplicationId**.
- `clientSecret`: se si vuole accedere usando il segreto client, crearlo in Azure AD. Usare inoltre un'API o un'app Web al posto di un'app nativa. Aggiungere anche l'app in **Controllo di accesso (IAM)** nello spazio dei nomi creato in precedenza.
- `serviceBusNamespaceFQDN`: impostare sul nome DNS completo dello spazio dei nomi del bus di servizio appena creato, ad esempio `example.servicebus.windows.net`.
- `queueName`: impostare sul nome della coda creata.
- URI di reindirizzamento specificato nell'app nei passaggi precedenti.

Quando si esegue l'applicazione console, viene richiesto di selezionare uno scenario. Selezionare **accesso utente interattivo** digitando il relativo numero e premendo INVIO. L'applicazione visualizza una finestra di accesso, chiede il consenso per l'accesso al bus di servizio e quindi usa il servizio per eseguire lo scenario di invio e ricezione mediante l'identità dell'account di accesso.


## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni su RBAC, vedere [che cos'è il controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md)?
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo (RBAC) con modelli di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

- [Esempi RBAC del bus di servizio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
- [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
