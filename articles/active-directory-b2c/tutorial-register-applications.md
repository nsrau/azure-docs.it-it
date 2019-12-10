---
title: "Esercitazione: registrare un'applicazione"
titleSuffix: Azure AD B2C
description: Informazioni su come registrare un'applicazione Web in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76d38e4b35111cd2c09685653fb4948f98387132
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950783"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Esercitazione: registrare un'applicazione in Azure Active Directory B2C

Prima che le [applicazioni](active-directory-b2c-apps.md) possano interagire con Azure Active Directory B2C (Azure ad B2C), devono essere registrate in un tenant gestito dall'utente. Questa esercitazione mostra come registrare un'applicazione Web usando il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. Usare un tenant Azure AD B2C esistente.

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Applicazioni** corrente o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
1. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. Ad esempio, è possibile impostarlo in modo che sia in ascolto localmente in `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.

    A scopo di test come questa esercitazione, è possibile impostarlo su `https://jwt.ms` che Visualizza il contenuto di un token per l'ispezione. Per questa esercitazione, impostare l' **URL di risposta** su `https://jwt.ms`.

    Agli URL di risposta si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo schema `https`.
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser considera i percorsi come distinzione tra maiuscole e minuscole, i cookie associati a `.../abc/response-oidc` possono essere esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza corrispondenza maiuscole/minuscole.

1. Fare clic su **Crea** per completare la registrazione dell'applicazione.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapp1*.
1. Selezionare **account in qualsiasi directory organizzativa o provider di identità**.
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere `https://jwt.ms` nella casella di testo URL.

    L'URI di reindirizzamento è l'endpoint a cui l'utente viene inviato dal server di autorizzazione (in questo caso Azure AD B2C) dopo aver completato l'interazione con l'utente e a cui viene inviato un token di accesso o un codice di autorizzazione dopo l'autorizzazione. In un'applicazione di produzione, si tratta in genere di un endpoint accessibile pubblicamente in cui l'app è in esecuzione, ad esempio `https://contoso.com/auth-response`. A scopo di test come questa esercitazione, è possibile impostarlo su `https://jwt.ms`, un'applicazione Web di proprietà di Microsoft che Visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). Durante lo sviluppo di app, è possibile aggiungere l'endpoint in cui l'applicazione è in ascolto localmente, ad esempio `https://localhost:5000`. È possibile aggiungere e modificare gli URI di reindirizzamento nelle applicazioni registrate in qualsiasi momento.

    Per gli URI di reindirizzamento si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo schema `https`.
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser considera i percorsi come distinzione tra maiuscole e minuscole, i cookie associati a `.../abc/response-oidc` possono essere esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza corrispondenza maiuscole/minuscole.

1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.

Al termine della registrazione dell'applicazione, abilitare il flusso di concessione implicita:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Concessione implicita** selezionare entrambe le caselle di controllo **Token di accesso** e **ID token**.
1. Selezionare **Salva**.

* * *

## <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice per un token, è necessario creare un segreto dell'applicazione.

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Nella pagina **Azure ad B2C-applicazioni** selezionare l'applicazione creata, ad esempio *app Web 1*.
1. Selezionare **chiavi** e quindi selezionare **Genera chiave**.
1. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Usare questo valore come segreto dell'applicazione nel codice dell'applicazione.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Nella pagina **Azure ad B2C-registrazioni app (anteprima)** selezionare l'applicazione creata, ad esempio *app Web 1*.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Selezionare **Nuovo segreto client**.
1. Immettere una descrizione per il segreto client nella casella **Descrizione**. Ad esempio, *clientsecret1*.
1. In **Scadenza**selezionare una durata per la quale il segreto è valido, quindi selezionare **Aggiungi**.
1. Registrare il **Valore** del segreto. Usare questo valore come segreto dell'applicazione nel codice dell'applicazione.

* * *

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Successivamente, si apprenderà come creare flussi utente per consentire agli utenti di iscriversi, accedere e gestire i propri profili.

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C >](tutorial-create-user-flows.md)
