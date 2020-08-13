---
title: 'Guida introduttiva: Registrare app con Microsoft Identity Platform | Azure'
description: Questo argomento di avvio rapido illustra come aggiungere e registrare un'applicazione con Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115357"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform

In questo argomento di avvio rapido si registra un'applicazione usando l'esperienza **Registrazioni app** nel portale di Azure. 

L'app viene integrata con Microsoft Identity Platform registrandola con un tenant di Azure Active Directory. Gli sviluppatori aziendali e i provider di servizi SaaS (software-as-a-service) possono sviluppare servizi cloud commerciali o applicazioni line-of-business integrabili con Microsoft Identity Platform. L'integrazione garantisce la sicurezza dell'accesso e delle autorizzazioni per tali servizi.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un [tenant di Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrare una nuova applicazione mediante il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra. Impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Cercare e selezionare **Azure Active Directory**. In **Gestisci** selezionare **Registrazioni app**.
1. Selezionare **Nuova registrazione**.
1. In **Registra un'applicazione** immettere un nome di applicazione significativo da visualizzare agli utenti.
1. Specificare chi può usare l'applicazione, come indicato di seguito:

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si sta creando un'applicazione line-of-business. Questa opzione non è disponibile se l'applicazione non viene registrata in una directory.<br><br>Questa opzione esegue il mapping solo a un tenant singolo di Azure AD.<br><br>È l'opzione predefinita a meno che non si registri l'app all'esterno di una directory. Nei casi in cui l'app viene registrata all'esterno di una directory, l'impostazione predefinita è costituita da account Microsoft personali e multi-tenant Azure AD. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si preferisce includere tutti i clienti aziendali o di istituti di istruzione.<br><br>Questa opzione esegue il mapping solo a multi-tenant Azure AD.<br><br>Un'app registrata solo come a tenant singolo di Azure AD può essere aggiornata a multi-tenant di Azure AD e di nuovo a tenant singolo tramite la pagina **Autenticazione**. |
    | **Account in qualsiasi directory organizzativa e account Microsoft personali** | Selezionare questa opzione per includere il set più ampio possibile di clienti.<br><br>Questa opzione esegue il mapping ad account Microsoft personali e multi-tenant Azure AD.<br><br>Se l'app è stata registrata come multi-tenant di Azure AD e per account Microsoft personali, non è possibile cambiare questa impostazione nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione. |

1. In **URI di reindirizzamento (facoltativo)** selezionare il tipo di app da creare: **Web** o **Client pubblico (per dispositivi mobili e desktop)** . Quindi immettere l'URI di reindirizzamento, o URL di risposta, per l'applicazione.

    * Per le applicazioni Web, specificare l'URL di base dell'app. Ad esempio, `https://localhost:31544` potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web.
    * Per le applicazioni client pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico dell'applicazione, ad esempio `myapp://auth`.

    Per esempi di applicazioni Web o native, vedere gli argomenti di avvio rapido in [Microsoft Identity Platform](./index.yml).

1. Al termine, selezionare **Registra**.

    ![Mostra la schermata per registrare una nuova applicazione nel portale di Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD assegna all'app un ID applicazione o client univoco. Il portale apre la pagina **Panoramica** dell'applicazione. Per aggiungere funzionalità all'applicazione, è possibile selezionare altre opzioni di configurazione, come Personalizzazione, Certificati e segreti, Autorizzazioni API e altro ancora.

![Esempio di una pagina di panoramica dell'app appena registrata](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Passaggi successivi

* Per accedere alle API Web, vedere [Avvio rapido: Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)

* Per altre informazioni sulle autorizzazioni, vedere [Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform](v2-permissions-and-consent.md).

* Per esporre le API Web, vedere [Avvio rapido: Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md).

* Per gestire gli account supportati, vedere [Avvio rapido: Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md).

* Per creare un'app e aggiungere funzionalità, vedere gli argomenti di avvio rapido in [Microsoft Identity Platform](./index.yml).

* Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

* Per altre informazioni sulle linee guida relative alla personalizzazione che è consigliabile seguire per lo sviluppo di app, vedere [Linee guida sulla personalizzazione per le applicazioni](howto-add-branding-in-azure-ad-apps.md).