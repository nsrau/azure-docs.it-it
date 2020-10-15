---
title: "Esercitazione: Registrare un'applicazione"
titleSuffix: Azure AD B2C
description: Seguire questa esercitazione per informazioni su come registrare un'applicazione Web in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0fd062bd0e58ecc714e4f450c93384e47e743b65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922014"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Esercitazione: Registrare un'applicazione Web in Azure Active Directory B2C

Prima che le [applicazioni](application-types.md) possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito. Questa esercitazione mostra come registrare un'applicazione Web usando il portale di Azure.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Se invece si usa un'app nativa (ad esempio iOS, Android, per dispositivi mobili e desktop), vedere [come registrare un'applicazione client nativa](add-native-application.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. Usare un tenant Azure AD B2C esistente.

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapp1*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa o qualsiasi provider di identità. Per l'autenticazione degli utenti con Azure AD B2C**.
1. In **URI di reindirizzamento**, selezionare **Web**, quindi immettere `https://jwt.ms` nella casella di testo URL.

    L'URI di reindirizzamento è l'endpoint a cui l'utente viene inviato dal server di autorizzazione (in questo caso Azure AD B2C) dopo aver completato l'interazione con l'utente e a cui viene inviato un token di accesso o un codice di autorizzazione dopo l'autorizzazione. In un'applicazione di produzione, si tratta in genere di un endpoint accessibile pubblicamente in cui l'app è in esecuzione, ad esempio `https://contoso.com/auth-response`. A scopo di test come questa esercitazione, è possibile impostarlo su `https://jwt.ms`, un'applicazione Web di proprietà di Microsoft che visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). Durante lo sviluppo di app, è possibile aggiungere l'endpoint in cui l'applicazione è in ascolto localmente, ad esempio `https://localhost:5000`. È possibile aggiungere e modificare gli URI di reindirizzamento nelle applicazioni registrate in qualsiasi momento.

    Agli URL di reindirizzamento si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo schema `https`.
    * L'URL di risposta rileva la distinzione tra maiuscole e minuscole. Le maiuscole e le minuscole devono corrispondere a quelle nel percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser rileva la distinzione tra maiuscole e minuscole nei percorsi, è possibile che i cookie associati a `.../abc/response-oidc` vengano esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza la corrispondenza tra maiuscole e minuscole.

1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.

Al termine della registrazione dell'applicazione, abilitare il flusso di concessione implicita:

1. Nel menu a sinistra, in **Gestisci**, selezionare **Autenticazione**.
1. In **Concessione implicita** selezionare entrambe le caselle di controllo **Token di accesso** e **ID token**.
1. Selezionare **Salva**.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
1. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. È ad esempio possibile impostarlo per essere in ascolto localmente di `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.

    A scopo di test, come in questa esercitazione, è possibile impostare l'URL su `https://jwt.ms`, in modo da visualizzare il contenuto di un token per l'ispezione. Per questa esercitazione, impostare il campo **URL di risposta** su `https://jwt.ms`.

    Agli URL di risposta si applicano le restrizioni riportate di seguito.

    * L'URL di risposta deve iniziare con lo schema `https`.
    * L'URL di risposta rileva la distinzione tra maiuscole e minuscole. Le maiuscole e le minuscole devono corrispondere a quelle nel percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser rileva la distinzione tra maiuscole e minuscole nei percorsi, è possibile che i cookie associati a `.../abc/response-oidc` vengano esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza la corrispondenza tra maiuscole e minuscole.

1. Selezionare **Crea** per completare la registrazione dell'applicazione.

* * *

## <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice di autorizzazione con un token di accesso, è necessario creare un segreto dell'applicazione.


#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Nella pagina **Azure AD B2C - Registrazioni app** selezionare l'applicazione creata, ad esempio *webapp1*.
1. Nel menu a sinistra selezionare **Certificati e segreti** in **Gestisci**.
1. Selezionare **Nuovo segreto client**.
1. Immettere una descrizione per il segreto client nella casella **Descrizione**. Ad esempio, *clientsecret1*.
1. In **Scadenza**selezionare una durata per la quale il segreto è valido, quindi selezionare **Aggiungi**.
1. Registrare il **Valore** del segreto. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Nella pagina **Azure AD B2C - Applicazioni**, selezionare l'applicazione creata, ad esempio *webapp1*.
1. Selezionare **Chiavi** e quindi selezionare **Genera chiave**.
1. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.

* * *

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Successivamente, si apprenderà come creare flussi utente per consentire agli utenti di iscriversi, accedere e gestire i propri profili.

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C >](tutorial-create-user-flows.md)
