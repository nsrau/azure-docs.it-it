---
title: Identità gestite per le risorse di Azure con il bus di servizio
description: Questo articolo descrive come usare le identità gestite per accedere con le entità del bus di servizio di Azure (code, argomenti e sottoscrizioni).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 46a1db94d576174b837a40c646fcf9e082e339c8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461617"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse del bus di servizio di AzureAuthenticate a managed identity with Azure Active Directory to access Azure Service Bus resources
Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client del bus di servizio in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi della messaggistica del bus di servizio. Quando l'app si connette, il bus di servizio associa il contesto dell'entità gestita al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo. Una volta eseguita l'associazione a un'identità gestita, il client del bus di servizio può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'entità gestita ai ruoli del bus di servizio. 

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un'entità del bus di servizio, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2.0.First, the security principal's identity is authenticated, and an OAuth 2.0 token is returned. Il nome della risorsa `https://servicebus.azure.net`per richiedere un token è .
 1. Successivamente, il token viene passato come parte di una richiesta al servizio bus di servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio una macchina virtuale di Azure, un set di scalabilità di macchine virtuali o un'app Di Azure Function, può usare un'identità gestita per accedere alle risorse. 

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli RBAC all'entità di sicurezza. Il bus di servizio di Azure fornisce ruoli RBAC che includono set di autorizzazioni per le risorse del bus di servizio. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni di cui l'entità disticherà. Per altre informazioni sull'assegnazione di ruoli RBAC al bus di servizio di Azure, vedere Ruoli RBAC incorporati per il bus di servizio di Azure.To learn more about assigning RBAC roles to Azure Service Bus, see [Built-in RBAC roles for Azure Service Bus.](#built-in-rbac-roles-for-azure-service-bus) 

Le applicazioni native e le applicazioni Web che effettuano richieste al bus di servizio possono anche autorizzare con Azure AD. Questo articolo illustra come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse del bus di servizio. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione di ruoli RBAC per i diritti di accessoAssigning RBAC roles for access rights
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Il bus di servizio di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usati per accedere alle entità del bus di servizio ed è inoltre possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso può essere il livello di sottoscrizione, il gruppo di risorse o lo spazio dei nomi del bus di servizio. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione o un'identità gestita per le risorse di Azure.An Azure AD security principal may be a user, a group, an application service principal, or a managed identity for Azure resources.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ruoli RBAC incorporati per il bus di servizio di AzureBuilt-in RBAC roles for Azure Service Bus
Per il bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API Gestione risorse di Azure è già protetto mediante il modello di *Controllo degli accessi in base al ruolo*. Azure offre i ruoli RBAC predefiniti seguenti per autorizzare l'accesso a uno spazio dei nomi del bus di servizio:Azure provides the below built-in RBAC roles for authorizing access to a Service Bus namespace:

- [Proprietario dei dati del bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)di servizio di Azure: consente l'accesso ai dati allo spazio dei nomi del bus di servizio e alle relative entità (code, argomenti, sottoscrizioni e filtri)Azure Service Bus Data Owner : Enables data access to Service Bus namespace and its entities (queues, topics, subscriptions, and filters)
- Mittente dei dati del bus di [servizio di Azure:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)usare questo ruolo per concedere l'accesso all'invio allo spazio dei nomi del bus di servizio e alle relative entità.
- [Ricevitore di dati](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)del bus di servizio di Azure: usare questo ruolo per concedere l'accesso alla ricezione dello spazio dei nomi del bus di servizio e delle relative entità. 

## <a name="resource-scope"></a>Ambito della risorsa 
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che l'entità di sicurezza deve disporre. Le procedure consigliate stabiliscono che è sempre consigliabile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in cui è possibile definire l'ambito dell'accesso alle risorse del bus di servizio, a partire dall'ambito più ristretto:

- **Coda**, **argomento**o **sottoscrizione**: l'assegnazione di ruolo si applica all'entità del bus di servizio specifica. Attualmente, il portale di Azure non supporta l'assegnazione di utenti/gruppi/identità gestite ai ruoli RBAC del bus di servizio a livello di sottoscrizione. Ecco un esempio di utilizzo dell'interfaccia della riga di comando di Azure: az-role-assignment-create per assegnare un'identità a un ruolo RBAC del bus di servizio:Here's an example of using the Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) to assign an identity to a Service Bus RBAC role: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Spazio dei nomi del bus**di servizio: l'assegnazione di ruolo si estende sull'intera topologia del bus di servizio nello spazio dei nomi e sul gruppo di consumer associato.
- **Gruppo di risorse:** l'assegnazione dei ruoli si applica a tutte le risorse del bus di servizio nel gruppo di risorse.
- **Sottoscrizione:** l'assegnazione del ruolo si applica a tutte le risorse del bus di servizio in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> Tenere presente che la propagazione delle assegnazioni di ruolo RBAC può richiedere fino a cinque minuti. 

Per ulteriori informazioni sulla modalità di definizione dei ruoli predefiniti, vedere [Informazioni sulle definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)di ruolo . Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure.For information about creating custom RBAC roles, see [Create custom roles for Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse del bus di servizio dalla macchina virtuale, è necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource ManagerAzure Resource Manager client libraries](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere autorizzazioni a un'identità gestita in Azure ADGrant permissions to a managed identity in Azure AD
Per autorizzare una richiesta al servizio bus di servizio da un'identità gestita nell'applicazione, configurare innanzitutto le impostazioni del controllo degli accessi in base al ruolo per tale identità gestita. Il bus di servizio di Azure definisce i ruoli RBAC che includono le autorizzazioni per l'invio e la lettura dal bus di servizio. Quando il ruolo Controllo degli accessi in base al ruolo viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso alle entità del bus di servizio nell'ambito appropriato.

Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [Autenticare e autorizzare con Azure Active Directory per l'accesso alle risorse](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)del bus di servizio.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usare il bus di servizio con le identità gestite per le risorse di Azure
Per utilizzare il bus di servizio con identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa una semplice applicazione che viene eseguita con un'identità gestita e accede alle risorse del bus di servizio.

In questo caso si usa un'applicazione Web di esempio ospitata nel servizio app di [Azure.](https://azure.microsoft.com/services/app-service/) Per istruzioni dettagliate per la creazione di un'applicazione Web, vedere [Creare un'app Web ASP.NET Core in AzureFor](../app-service/app-service-web-get-started-dotnet.md) step-by-step instructions for creating a web application, see Create an ASP.NET Core web app in Azure

Una volta creata l'applicazione, attenersi alla seguente procedura: 

1. Vai a **Impostazioni** e seleziona **Identità**. 
1. Selezionare lo **stato** su **.** 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per un'app Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Dopo aver abilitato questa impostazione, una nuova identità del servizio viene creata in Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

A questo punto, assegnare questa identità del servizio a un ruolo nell'ambito richiesto nelle risorse del bus di servizio.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Per assegnare ruoli RBAC tramite il portale di AzureTo Assign RBAC roles using the Azure portal
Per assegnare un ruolo a uno spazio dei nomi del bus di servizio, passare allo spazio dei nomi nel portale di Azure.To assign a role to a Service Bus namespace, navigate to the namespace in the Azure portal. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> I passaggi seguenti assegnano un ruolo di identità del servizio agli spazi dei nomi del bus di servizio. È possibile seguire la stessa procedura per assegnare un ruolo in altri ambiti supportati (gruppo di risorse e sottoscrizione). 
> 
> [Creare uno spazio dei nomi](service-bus-create-namespace-portal.md) di messaggistica del bus di servizio, se non si dispone di uno. 

1. Nel portale di Azure passare allo spazio dei nomi del bus di servizio e visualizzare la **panoramica** per lo spazio dei nomi. 
1. Selezionare Controllo di accesso **(IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per lo spazio dei nomi del bus di servizio.
1.  Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
3.  Selezionare **Aggiungi** per aggiungere un nuovo ruolo.
4.  Nella pagina **Aggiungi assegnazione ruolo** selezionare i ruoli del bus di servizio di Azure che si desidera assegnare. Quindi cercare per individuare l'identità del servizio registrato per assegnare il ruolo.
    
    ![Pagina Aggiungi assegnazione ruolo](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selezionare **Salva**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che l'identità del servizio ha il proprietario dei dati del bus di servizio di Azure.For example, the following image shows that service identity has Azure Service Bus Data owner.
    
    ![Identità assegnata a un ruolo](./media/service-bus-managed-service-identity/role-assigned.png)

Dopo aver assegnato il ruolo, l'applicazione Web avrà accesso alle entità bus di servizio nell'ambito definito. 

### <a name="run-the-app"></a>Eseguire l'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

La pagina Default.aspx è la pagina di destinazione. Il codice è disponibile nel file Default.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione al bus di servizio per l'invio o la ricezione di messaggi.

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();`. In questo modo, non ci sono segreti da conservare e usare. Il flusso del contesto dell'identità gestita nel bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token. Questo modello è più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere con facilità i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![Ottenere il profilo di pubblicazioneGet publish profile](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata. Fare quindi clic su **Invia** o **Ricevi**.


> [!NOTE]
> - L'identità gestita funziona solo all'interno dell'ambiente di Azure, nei servizi app, nelle macchine virtuali di Azure e nei set di scalabilità. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication, usata dal pacchetto NuGet del bus di servizio, rappresenta un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale. Questa libreria consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'interfaccia della riga di comando di Azure 2.0 o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - Le identità gestite attualmente non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
