---
title: Anteprima di Azure il controllo di accesso Service Bus Role-Based (RBAC) | Documenti Microsoft
description: Controllo di accesso di Azure Service Bus basata sui ruoli
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Controllo di accesso Active Directory Role-Based (anteprima)

Microsoft Azure offre la gestione di controllo di accesso integrata per le risorse e applicazioni basate su Azure Active Directory (Azure AD). Con Azure AD, è possibile gestire gli account utente e applicazioni basate su applicazioni in modo specifico per di Azure oppure è possibile federare l'infrastruttura esistente di Active Directory con Azure AD per single sign-on aziendali che si estende anche a risorse di Azure e le applicazioni ospitate di Azure. Successivamente, è possibile assegnare alle identità di applicazione e dell'utente di Azure AD globali e specifico del servizio ai ruoli per concedere l'accesso alle risorse di Azure.

Per il Bus di servizio di Azure, la gestione di spazi dei nomi e tutte le relative risorse tramite il portale di Azure e l'API di gestione risorse di Azure è già protetto mediante il *controllo di accesso basato sui ruoli* modello (RBAC). RBAC per operazioni di runtime è una funzionalità in anteprima pubblica. 

Un'applicazione che utilizza Azure AD accessi non è necessario gestire le regole di firma di accesso condiviso e le chiavi o eventuali altri token di accesso specifico al Bus di servizio. L'app client interagisce con Azure AD per stabilire un contesto di autenticazione e acquisisce un token di accesso per il Bus di servizio. Con gli account utente di dominio che richiedono l'accesso interattivo, l'applicazione non gestisce le credenziali direttamente.

## <a name="service-bus-roles-and-permissions"></a>Le autorizzazioni e ruoli di Service Bus

Per l'anteprima pubblica iniziale, è possibile aggiungere solo account di Azure AD e le entità servizio per i ruoli di "Proprietario" o "Collaboratore" di uno spazio dei nomi di messaggistica del Bus di servizio. Questa operazione garantisce il controllo completo di identità su tutte le entità nello spazio dei nomi. Operazioni di gestione che modificano la topologia di spazio dei nomi sono inizialmente solo la gestione delle risorse tramite Azure supportate e non tramite l'interfaccia di gestione di Service Bus REST nativo. Questo supporto significa anche che il client di .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oggetto non può essere utilizzato con un account di Azure AD.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Utilizzare il Bus di servizio con un account utente di dominio di Azure AD

Nella sezione seguente descrive i passaggi necessari per creare ed eseguire un'applicazione di esempio che richiede un Azure interattiva utente di Active Directory per l'accesso, come il Bus di servizio l'accesso all'account utente e come utilizzare tale identità per accedere agli hub di eventi. 

In questa introduzione descrive una semplice applicazione console, il [codice per il quale è in Github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Creare un account utente di Active Directory

Il primo passaggio è facoltativo. Ogni sottoscrizione di Azure è automaticamente associato a un tenant Azure Active Directory e se si ha accesso a una sottoscrizione di Azure, l'account utente è già registrato. Pertanto, che è possibile utilizzare l'account. 

Se si desidera creare un account specifico per questo scenario, [seguire questi passaggi](../automation/automation-create-aduser-account.md). È necessario disporre dell'autorizzazione per creare gli account nel tenant di Azure Active Directory, che potrebbe non essere il caso di scenari aziendali più grandi.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del Bus di servizio

Successivamente, [creare uno spazio dei nomi di messaggistica del Bus di servizio](service-bus-create-namespace-portal.md) in una delle aree di Azure che dispongono del supporto di anteprima per RBAC: **Stati Uniti orientali**, **Stati Uniti orientali 2**, o **Europa occidentale** . 

Dopo aver creato lo spazio dei nomi, passare alla relativa **il controllo di accesso (IAM)** pagina del portale e quindi fare clic su **Aggiungi** per aggiungere l'account utente di Azure AD per il ruolo proprietario. Se si utilizza il proprio account utente e lo spazio dei nomi è stato creato, si è già il ruolo di proprietario. Per aggiungere un account diverso per il ruolo, cercare il nome dell'applicazione web nel **aggiungere autorizzazioni** pannello **selezionare** campo e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

L'account utente dispone ora dell'accesso allo spazio dei nomi Service Bus e alla coda creata in precedenza.
 
### <a name="register-the-application"></a>Registrare l'applicazione

Prima di poter eseguire l'applicazione di esempio, la registrazione in Azure AD e approvare la richiesta di consenso che consente all'applicazione di accedere a Azure Service Bus per suo conto. 

Poiché l'applicazione di esempio è un'applicazione console, è necessario registrare un'applicazione nativa e aggiungere le autorizzazioni dell'API per **ServiceBus** al set di "autorizzazioni richieste". Le applicazioni native è inoltre necessitano un **URI di reindirizzamento** in Azure Active Directory che serve come un identificatore URI non è necessario essere una destinazione di rete. Utilizzare `http://servicebus.microsoft.com` per questo esempio, poiché l'esempio di codice già utilizza quell'URI.

Le operazioni di registrazione dettagliata vengono spiegate in [questa esercitazione](../active-directory/develop/active-directory-integrating-applications.md). Seguire i passaggi per registrare un **nativo** app, quindi seguire le istruzioni di aggiornamento per aggiungere il **ServiceBus** API per le autorizzazioni necessarie. Mentre si esegue la procedura, prendere nota del **TenantId** e **ApplicationId**, come è necessario che questi valori per eseguire l'applicazione.

### <a name="run-the-app"></a>Esecuzione dell'app

Prima di poter eseguire l'esempio, modificare il file app. config e, a seconda dello scenario, impostare i valori seguenti:

- `tenantId`: Impostare su **TenantId** valore.
- `clientId`: Impostare su **ApplicationId** valore. 
- `clientSecret`: Se si desidera accedere utilizzando la chiave privata client, è possibile crearlo in Azure AD. Inoltre, è possibile utilizzare un'app web o l'API invece di un'applicazione nativa. Inoltre, aggiungere l'app in **il controllo di accesso (IAM)** nello spazio dei nomi creato in precedenza.
- `serviceBusNamespaceFQDN`: Impostare il nome DNS completo dello spazio dei nomi del Bus di servizio appena creato; ad esempio, `example.servicebus.windows.net`.
- `queueName`: Impostare il nome della coda che è stato creato.
- L'URI di reindirizzamento specificato nell'app nei passaggi precedenti.
 
Quando si esegue l'applicazione console, viene chiesto di selezionare un tipo di scenario. Fare clic su **accesso utente interattivo** digitando il numero e premendo INVIO. L'applicazione visualizza una finestra di dialogo di accesso, chiede il consenso dell'utente accedere a Bus di servizio e quindi utilizza il servizio per eseguire lo scenario di trasmissione e ricezione mediante l'identità dell'account di accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)