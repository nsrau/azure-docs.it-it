---
title: Autenticare un'applicazione per accedere alle risorse di hub eventi di Azure
description: Questo articolo fornisce informazioni sull'autenticazione di un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 9ea22dfc83883238923de06a764d7ef1f11cb475
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992991"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticare un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi
Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basata su Azure Active Directory (Azure AD). Un vantaggio fondamentale dell'uso di Azure AD con hub eventi di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse di hub eventi di Azure.

Quando un ruolo viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse, allo spazio dei nomi di hub eventi o a qualsiasi risorsa sottostante. Un Azure AD sicurezza può assegnare ruoli a un utente, a un gruppo, a un'entità servizio dell'applicazione o a un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Una definizione di ruolo è una raccolta di autorizzazioni. Il controllo degli accessi in base al ruolo controlla il modo in cui queste autorizzazioni vengono applicate tramite l'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Per ulteriori informazioni, vedere [Understanding the different Roles](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Ruoli predefiniti per hub eventi di Azure
Azure fornisce i seguenti ruoli predefiniti di controllo degli accessi in base al ruolo per autorizzare l'accesso ai dati di hub eventi tramite Azure AD e OAuth:

- [Proprietario dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview): Usare questo ruolo per concedere l'accesso completo alle risorse di hub eventi.
- [Mittente dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender-preview): Usare questo ruolo per concedere l'accesso di trasmissione alle risorse di hub eventi.
- [Ricevitore di dati di hub eventi di Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver-preview): Usare questo ruolo per concedere l'accesso alle risorse di hub eventi.   

> [!IMPORTANT]
> La versione di anteprima supporta l'aggiunta di privilegi di accesso ai dati di hub eventi al ruolo proprietario o collaboratore. Tuttavia, i privilegi di accesso ai dati per il proprietario e il ruolo Collaboratore non vengono più rispettati. Se si usa il ruolo proprietario o collaboratore, passare a usare il ruolo di proprietario dei dati di hub eventi di Azure.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare i ruoli RBAC usando il portale di Azure  
Per altre informazioni sulla gestione dell'accesso alle risorse di Azure con RBAC e la portale di Azure, vedere [questo articolo](..//role-based-access-control/role-assignments-portal.md). 

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> La procedura descritta di seguito assegna un ruolo all'hub eventi negli spazi dei nomi di hub eventi, ma è possibile seguire la stessa procedura per assegnare un ruolo con ambito a qualsiasi risorsa di hub eventi.

1. Nella [portale di Azure](https://portal.azure.com/)passare allo spazio dei nomi di hub eventi.
2. Nella pagina **Overview (panoramica** ) selezionare l'hub eventi per cui si vuole assegnare un ruolo.

    ![Selezionare l'hub eventi](./media/authenticate-application/select-event-hub.png)
1. Selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'hub eventi. 
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo. Selezionare il pulsante **Aggiungi** sulla barra degli strumenti e quindi selezionare **Aggiungi assegnazione ruolo**. 

    ![Pulsante Aggiungi sulla barra degli strumenti](./media/authenticate-application/role-assignments-add-button.png)
1. Nella pagina **Aggiungi assegnazione ruolo** eseguire le operazioni seguenti:
    1. Selezionare il **ruolo di hub eventi** che si vuole assegnare. 
    1. Eseguire una ricerca per individuare l' **entità di sicurezza** (utente, gruppo, entità servizio) a cui si desidera assegnare il ruolo.
    1. Selezionare **Save (Salva** ) per salvare l'assegnazione di ruolo. 

        ![Assegnare un ruolo a un utente](./media/authenticate-application/assign-role-to-user.png)
    4. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che Azure-Users si trova nel ruolo di proprietario dei dati di hub eventi di Azure. 
        
        ![Utente nell'elenco](./media/authenticate-application/user-in-list.png)

È possibile seguire una procedura simile per assegnare un ruolo con ambito allo spazio dei nomi di hub eventi, al gruppo di risorse o alla sottoscrizione. Dopo aver definito il ruolo e il relativo ambito, è possibile testare questo comportamento con gli esempi [in questo percorso di GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Eseguire l'autenticazione da un'applicazione
Un vantaggio fondamentale dell'uso di Azure AD con hub eventi è che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste a hub eventi di Azure.

Le sezioni seguenti illustrano come configurare un'applicazione nativa o un'applicazione Web per l'autenticazione con Microsoft Identity Platform 2,0. Per ulteriori informazioni sulla piattaforma Microsoft Identity Platform 2,0, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD
Il primo passaggio nell'uso di Azure AD per autorizzare le risorse di hub eventi è la registrazione dell'applicazione client con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com/). Quando si registra l'applicazione client, è necessario fornire informazioni sull'applicazione ad Active Directory. Azure AD fornisce quindi un ID client (noto anche come ID applicazione) che è possibile usare per associare l'applicazione a Azure AD Runtime. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Nelle immagini seguenti vengono illustrati i passaggi per la registrazione di un'applicazione Web:

![Registra un'applicazione](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l'URI di reindirizzamento. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido perché specifica l'URL a cui vengono forniti i token.

Dopo aver registrato l'applicazione, l' **ID applicazione (client)** verrà visualizzato in **Impostazioni**:

![ID applicazione dell'applicazione registrata](./media/authenticate-application/application-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Creare un segreto client   
L'applicazione richiede un segreto client per dimostrare la propria identità quando richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura.

1. Passare alla registrazione dell'app nel portale di Azure.
1. Selezionare l'impostazione **certificati & segreti** .
1. In **segreti client**selezionare **nuovo segreto client** per creare un nuovo segreto.
1. Specificare una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in una posizione sicura. Il valore di riempimento viene visualizzato una sola volta.

    ![Segreto client](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token  
Dopo aver registrato l'applicazione e concesso le autorizzazioni per l'invio e la ricezione di dati nell'hub eventi di Azure, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire il token OAuth 2,0. Per autenticare e acquisire il token, è possibile usare una delle librerie di [autenticazione della piattaforma di identità Microsoft](../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID o Connect 1,0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta a hub eventi di Azure.

Per un elenco degli scenari per i quali è supportato l'acquisizione di token, vedere la sezione [scenari](https://aka.ms/msal-net-scenarios) del repository [Microsoft Authentication Library (MSAL) per .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) github.


## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni su RBAC, vedere [che cos'è il controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md)?
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo (RBAC) con modelli di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Vedere gli articoli correlati seguenti:
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md)
- [Autenticare le richieste a hub eventi di Azure usando le firme di accesso condiviso](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso](authorize-access-shared-access-signature.md)

