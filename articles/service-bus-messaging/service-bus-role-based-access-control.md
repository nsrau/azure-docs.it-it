---
title: Controllo degli accessi in base al ruolo del bus di servizio di Azure (anteprima) | Microsoft Docs
description: Controllo degli accessi in base al ruolo del bus di servizio di Azure
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: e4571a8918b7877b728b54129e47ffcf4af9b46a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65979635"
---
# <a name="active-directory-role-based-access-control-preview"></a>Controllo degli accessi in base al ruolo di Active Directory (anteprima)

Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basata su Azure Active Directory (Azure AD). Con Azure AD, è possibile gestire gli account utente e le applicazioni, in modo specifico le applicazioni basate su Azure, oppure è possibile federare l'infrastruttura esistente di Active Directory con Azure AD per ottenere funzionalità di accesso Single Sign-On a livello aziendale che si estendono anche alle risorse di Azure e alle applicazioni ospitate in Azure. È quindi possibile assegnare le identità di applicazioni e utenti di Azure AD a ruoli globali e specifici del servizio, per concedere l'accesso alle risorse di Azure.

Per il bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API Gestione risorse di Azure è già protetto mediante il modello di *Controllo degli accessi in base al ruolo*. Controllo degli accessi in base al ruolo per le operazioni di runtime è una funzionalità ora disponibile in versione di anteprima pubblica.

Per un'applicazione che usa Controllo degli accessi in base al ruolo di Azure AD non è necessario gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso specifici del bus di servizio. L'app client interagisce con Azure AD per stabilire un contesto di autenticazione e acquisisce un token di accesso per il bus di servizio. Con gli account utente di dominio che richiedono l'accesso interattivo, l'applicazione non gestisce mai le credenziali direttamente.

## <a name="service-bus-roles-and-permissions"></a>Ruoli e autorizzazioni del bus di servizio

Azure offre i seguenti ruoli RBAC predefiniti per autorizzare l'accesso a uno spazio dei nomi del Bus di servizio:

* [Proprietario dei dati del Bus di servizio (anteprima)](../role-based-access-control/built-in-roles.md#service-bus-data-owner): Consente l'accesso alle relative entità (code, argomenti, sottoscrizioni e i filtri) e dello spazio dei nomi del Bus di servizio

>[!IMPORTANT]
> È supportato in precedenza aggiungendo identità gestita per il **"Proprietario"** oppure **"Collaboratore"** ruolo.
>
> Accesso ai dati, tuttavia, i privilegi per **"Proprietario"** e **"Collaboratore"** non verranno rispettato non è più ruoli. Se si usa il **"Proprietario"** oppure **"Collaboratore"** ruolo, quindi quelli dovrà essere adattate per utilizzare il **"Proprietario del servizio del Bus di dati"** ruolo.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Usare il bus di servizio con un account utente di dominio di Azure AD

La sezione seguente descrive i passaggi necessari per creare ed eseguire un'applicazione di esempio che richiede un interattivo di Azure AD all'utente di eseguire, come concedere l'accesso del Bus di servizio per quell'account utente e come usare tale identità per accedere a hub eventi.

Questa introduzione descrive una semplice applicazione console, il cui [codice è disponibile su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Creare un account utente di Active Directory

Il primo passaggio è facoltativo. Ogni sottoscrizione di Azure è automaticamente associata a un tenant di Azure Active Directory e, se si ha accesso a una sottoscrizione di Azure, l'account utente è già registrato. Ciò significa che è possibile usare il proprio account.

Se si vuole comunque creare un account specifico per questo scenario, [seguire questi passaggi](../automation/automation-create-aduser-account.md). È necessario avere l'autorizzazione per la creazione di account nel tenant di Azure Active Directory, cosa che potrebbe non essere possibile nelle aziende più grandi.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Successivamente [creare uno spazio dei nomi di messaggistica del Bus di servizio](service-bus-create-namespace-portal.md).

Una volta creato lo spazio dei nomi, passare alla relativa pagina **Controllo di accesso (IAM)** nel portale e quindi fare clic su **Aggiungi assegnazione ruolo** per aggiungere l'account utente di Azure AD al ruolo Proprietario. Se si usa il proprio account utente e si è creato lo spazio dei nomi, si sta già usando il ruolo Proprietario. Per aggiungere un account utente diverso al ruolo, cercare il nome dell'applicazione Web nel campo **Seleziona** del pannello **Aggiungi autorizzazioni** e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

L'account utente dispone ora dell'accesso allo spazio dei nomi del bus di servizio e alla coda creata in precedenza.

### <a name="register-the-application"></a>Registrare l'applicazione

Prima di poter eseguire l'applicazione di esempio, registrarla in Azure AD e approvare la richiesta di consenso per permettere all'applicazione di accedere al bus di servizio di Azure.

Poiché l'applicazione di esempio è un'applicazione console, è necessario registrare un'applicazione nativa e aggiungere le autorizzazioni API per **Microsoft.ServiceBus** al set di autorizzazioni richieste. Le applicazioni native richiedono anche un oggetto **redirect-URI** in Azure AD, che funge da identificatore. Non è necessario che l'URI sia una destinazione di rete. Per questo esempio usare `https://servicebus.microsoft.com`, poiché questo URI è già usato nel codice di esempio.

I passaggi di registrazione dettagliati sono illustrati in [questa esercitazione](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Seguire i passaggi per registrare un'app **nativa** e quindi seguire le istruzioni di aggiornamento per aggiungere l'API **Microsoft.ServiceBus** alle autorizzazioni necessarie. Mentre si esegue la procedura, prendere nota di **TenantId** e **ApplicationId**, perché questi valori saranno necessari per eseguire l'applicazione.

### <a name="run-the-app"></a>Esecuzione dell'app

Prima di eseguire l'esempio, modificare il file App.config e, a seconda dello scenario, impostare i valori seguenti:

- `tenantId`: impostare sul valore di **TenantId**.
- `clientId`: impostare sul valore di **ApplicationId**.
- `clientSecret`: se si vuole accedere usando il segreto client, crearlo in Azure AD. Usare inoltre un'API o un'app Web al posto di un'app nativa. Aggiungere anche l'app in **Controllo di accesso (IAM)** nello spazio dei nomi creato in precedenza.
- `serviceBusNamespaceFQDN`: impostare sul nome DNS completo dello spazio dei nomi del bus di servizio appena creato, ad esempio `example.servicebus.windows.net`.
- `queueName`: impostare sul nome della coda creata.
- URI di reindirizzamento specificato nell'app nei passaggi precedenti.

Quando si esegue l'applicazione console, viene chiesto di selezionare uno scenario. Fare clic su **Interactive User Login** (Accesso utente interattivo) digitando il relativo numero e premendo INVIO. L'applicazione visualizza una finestra di accesso, chiede il consenso per l'accesso al bus di servizio e quindi usa il servizio per eseguire lo scenario di invio e ricezione mediante l'identità dell'account di accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
