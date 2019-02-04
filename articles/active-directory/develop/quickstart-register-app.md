---
title: Registrare un'app con Microsoft Identity Platform (anteprima) | Azure
description: Informazioni su come aggiungere e registrare un'applicazione con Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/02/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: fa75666c63d666f1358c1d6bf2f4b18b9eb7a813
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103327"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform (anteprima)

Gli sviluppatori aziendali e i provider SaaS (Software as a Service) possono sviluppare servizi cloud commerciali o applicazioni line-of-business integrabili con Microsoft Identity Platform per garantire la sicurezza dell'accesso e dell'autorizzazione per i propri servizi.

Questa guida introduttiva illustra come aggiungere e registrare un'applicazione usando il servizio **Registrazioni app (anteprima)** nel portale di Azure in modo da consentirne l'integrazione con Microsoft Identity Platform. Per altre informazioni sulle nuove funzionalità e sui miglioramenti nella nuova esperienza di registrazione di app, vedere [questo post di blog](https://developer.microsoft.com/graph/blogs/new-app-registration/). 

## <a name="prerequisite"></a>Prerequisito

Per iniziare, è necessario acconsentire esplicitamente all'esperienza di anteprima per le registrazioni di app nel portale di Azure. Le procedure illustrate in questa guida introduttiva corrispondono alla nuova interfaccia utente e funzionano solo se si è acconsentito esplicitamente all'esperienza di anteprima.

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrare una nuova applicazione mediante il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima) > Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.

    - **Nome**: immettere un nome di applicazione significativo, che verrà visualizzato agli utenti dell'app.
    - **Tipi di account supportati**: selezionare gli account che dovranno essere supportati dall'applicazione.

        | Tipi di account supportati | DESCRIZIONE |
        |-------------------------|-------------|
        | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si sta creando un'applicazione line-of-business. L'opzione non è disponibile se non si registra l'applicazione in una directory.<br><br>Questa opzione esegue il mapping solo a un tenant singolo di Azure AD.<br><br>È l'opzione predefinita a meno che non si registri l'app all'esterno di una directory. Nei casi in cui l'app viene registrata all'esterno di una directory, l'impostazione predefinita è costituita da account Microsoft personali e multi-tenant Azure AD. |
        | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si preferisce includere tutti i clienti aziendali o di istituti di istruzione.<br><br>Questa opzione esegue il mapping solo a multi-tenant Azure AD.<br><br>Un'app registrata solo come a tenant singolo Azure AD può essere aggiornata a multi-tenant Azure AD e di nuovo a tenant singolo tramite il pannello **Autenticazione**. |
        | **Account in qualsiasi directory organizzativa e account Microsoft personali** | Selezionare questa opzione per includere il set più ampio possibile di clienti.<br><br>Questa opzione esegue il mapping ad account Microsoft personali e multi-tenant Azure AD.<br><br>Se l'app è stata registrata come multi-tenant Azure AD e per account Microsoft personali, non è possibile modificare questa impostazione nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione. |

    - **URI di reindirizzamento (facoltativo)**: selezionare il tipo di app che si sta creando, ossia **Web** o **Client pubblico (per dispositivi mobili e desktop)**, e quindi immettere l'URI di reindirizzamento (o l'URL di risposta) per l'applicazione.
        - Per le applicazioni Web, specificare l'URL di base dell'app. Ad esempio, `http://localhost:31544` potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web.
        - Per le applicazioni client pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico dell'applicazione, ad esempio `myapp://auth`.

    Per esempi specifici per le applicazioni Web o per le applicazioni native, vedere le [guide introduttive](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Al termine, selezionare **Registra**.

    [![Registrare una nuova applicazione nel portale di Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD assegna un ID applicazione (client) univoco all'app e viene visualizzata la pagina **Panoramica** dell'applicazione. Per aggiungere altre funzionalità all'applicazione, è possibile selezionare altre opzioni di configurazione, come Personalizzazione, Certificati e segreti, Autorizzazioni API e altro ancora.

[![Pagina di panoramica dell'app appena registrata](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [autorizzazioni e consenso](v2-permissions-and-consent.md).
- Per abilitare funzionalità di configurazione aggiuntive nella registrazione dell'applicazione, come credenziali e autorizzazioni, e consentire l'accesso a utenti di altri tenant, vedere le guide introduttive seguenti:
    - [Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)
    - [Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md)
    - [Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)
- Scegliere una [guida introduttiva](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) per creare rapidamente un'app e aggiungere funzionalità come il recupero e l'aggiornamento di token, l'accesso di un utente, la visualizzazione di alcune informazioni utente e altro ancora.
- Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).
- Per altre informazioni sulle linee guida relative alla personalizzazione che è consigliabile seguire per lo sviluppo di app, vedere [Linee guida sulla personalizzazione per le applicazioni](howto-add-branding-in-azure-ad-apps.md).
