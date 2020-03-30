---
title: "Esercitazione: Registrare un'applicazioneTutorial: Register an application"
titleSuffix: Azure AD B2C
description: Informazioni su come registrare un'applicazione Web in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183092"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Esercitazione: Registrare un'applicazione in Azure Active Directory B2CTutorial: Register an application in Azure Active Directory B2C

Prima che [le applicazioni](application-types.md) possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito. Questa esercitazione mostra come registrare un'applicazione Web usando il portale di Azure.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato creato il proprio [tenant B2C](tutorial-create-tenant.md)di Azure AD, crearne uno ora. Usare un tenant Azure AD B2C esistente.

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Applicazioni** corrente o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
1. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. Ad esempio, è possibile impostarlo `https://localhost:44316`per l'ascolto locale in . Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.

    A scopo di test come questa `https://jwt.ms` esercitazione è possibile impostarla su cui viene visualizzato il contenuto di un token per l'ispezione. Per questa esercitazione, impostare l'URL di **risposta su** `https://jwt.ms`.

    Per gli URL di risposta si applicano le restrizioni seguenti:

    * L'URL di risposta `https`deve iniziare con lo schema .
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Ad esempio, se l'applicazione include `.../abc/response-oidc`come parte `.../ABC/response-oidc` del relativo percorso , non specificare nell'URL di risposta. Poiché il browser Web considera i percorsi `.../abc/response-oidc` come maiuscole/minuscole, i cookie associati `.../ABC/response-oidc` potrebbero essere esclusi se reindirizzati all'URL senza corrispondenza tra maiuscole e minuscole.

1. Selezionare **Crea** per completare la registrazione dell'applicazione.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapp1*.
1. Selezionare **Account in qualsiasi directory dell'organizzazione o provider di identità**.
1. In **URI di reindirizzamento**selezionare **Web**e quindi immettere `https://jwt.ms` nella casella di testo URL.

    L'URI di reindirizzamento è l'endpoint a cui l'utente viene inviato dal server di autorizzazione (Azure AD B2C, in questo caso) dopo aver completato l'interazione con l'utente e a cui viene inviato un token di accesso o un codice di autorizzazione in caso di corretta autorizzazione. In un'applicazione di produzione si tratta in genere di `https://contoso.com/auth-response`un endpoint accessibile pubblicamente in cui l'app è in esecuzione, ad esempio . A scopo di test come questa esercitazione, è possibile impostarla su `https://jwt.ms`, un'applicazione Web di proprietà di Microsoft che visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). Durante lo sviluppo dell'app, è possibile aggiungere `https://localhost:5000`l'endpoint in cui l'applicazione è in ascolto in locale, ad esempio . È possibile aggiungere e modificare gli URI di reindirizzamento nelle applicazioni registrate in qualsiasi momento.

    Le restrizioni seguenti si applicano agli URI di reindirizzamento:

    * L'URL di risposta `https`deve iniziare con lo schema .
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Ad esempio, se l'applicazione include `.../abc/response-oidc`come parte `.../ABC/response-oidc` del relativo percorso , non specificare nell'URL di risposta. Poiché il browser Web considera i percorsi `.../abc/response-oidc` come maiuscole/minuscole, i cookie associati `.../ABC/response-oidc` potrebbero essere esclusi se reindirizzati all'URL senza corrispondenza tra maiuscole e minuscole.

1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.

Una volta completata la registrazione dell'applicazione, abilitare il flusso di concessione implicita:Once the application registration is complete, enable the implicit grant flow:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Concessione implicita**selezionare entrambe le caselle di controllo Token di **accesso** e **token ID.**
1. Selezionare **Salva**.

* * *

## <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice di autorizzazione per un token di accesso, è necessario creare un segreto dell'applicazione.

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Nella pagina **Azure AD B2C - Applicazioni** selezionare l'applicazione creata, ad esempio *webapp1*.
1. Selezionare **Chiavi** e quindi **Genera chiave**.
1. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene utilizzato come segreto dell'applicazione nel codice dell'applicazione.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Nella pagina **Azure AD B2C - Registrazioni app (anteprima)** selezionare l'applicazione creata, ad esempio *webapp1*.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Selezionare **Nuovo segreto client**.
1. Immettere una descrizione per il segreto client nella casella **Descrizione**. Ad esempio, *clientsecret1*.
1. In **Scadenza**selezionare una durata per la quale il segreto è valido, quindi selezionare **Aggiungi**.
1. Registrare il **Valore** del segreto. Questo valore viene utilizzato come segreto dell'applicazione nel codice dell'applicazione.

* * *

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Successivamente, scopri come creare flussi utente per consentire agli utenti di iscriversi, accedere e gestire i propri profili.

> [!div class="nextstepaction"]
> [Creare flussi utente nei processi B2C di Azure Active DirectoryCreate user flows in Azure Active Directory B2C >](tutorial-create-user-flows.md)
