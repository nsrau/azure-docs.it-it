---
title: 'Guida introduttiva: registrare le app con la piattaforma di identità Microsoft | Azure'
description: Questa Guida introduttiva illustra come aggiungere e registrare un'applicazione con la piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892864"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft

In questa Guida introduttiva si registra un'applicazione con l'esperienza **registrazioni app** nel portale di Azure. L'app è integrata con la piattaforma di identità Microsoft. Gli sviluppatori aziendali e i provider SaaS (software-as-a-Service) possono sviluppare servizi cloud commerciali o applicazioni line-of-business che possono essere integrati con la piattaforma delle identità Microsoft. L'integrazione fornisce l'accesso sicuro e l'autorizzazione per tali servizi.

## <a name="prerequisites"></a>Prerequisites

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrare una nuova applicazione mediante il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più di un tenant, selezionare l'account nell'angolo in alto a destra. Impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Cercare e selezionare **Azure Active Directory**. In **Gestisci**selezionare **registrazioni app**.
1. Selezionare **Nuova registrazione**.
1. In **registra un'applicazione**immettere un nome di applicazione significativo da visualizzare agli utenti.
1. Specificare gli utenti che possono usare l'applicazione, come indicato di seguito:

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si sta creando un'applicazione line-of-business. Questa opzione non è disponibile se non si sta registrando l'applicazione in una directory.<br><br>Questa opzione esegue il mapping solo a un tenant singolo di Azure AD.<br><br>Questa opzione è l'impostazione predefinita, a meno che non si stia registrando l'app all'esterno di una directory. Nei casi in cui l'app viene registrata all'esterno di una directory, l'impostazione predefinita è costituita da account Microsoft personali e multi-tenant Azure AD. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si preferisce includere tutti i clienti aziendali o di istituti di istruzione.<br><br>Questa opzione esegue il mapping solo a multi-tenant Azure AD.<br><br>Se l'app è stata registrata come Azure AD solo a tenant singolo, è possibile aggiornarla in modo che sia Azure AD multi-tenant e di nuovo in un tenant singolo tramite la pagina di **autenticazione** . |
    | **Account in qualsiasi directory organizzativa e account Microsoft personali** | Selezionare questa opzione per includere il set più ampio possibile di clienti.<br><br>Questa opzione esegue il mapping ad account Microsoft personali e multi-tenant Azure AD.<br><br>Se l'app è stata registrata come Azure AD account Microsoft multi-tenant e personali, non è possibile modificare questa impostazione nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione. |

1. In **URI di reindirizzamento (facoltativo)** selezionare il tipo di app che si sta compilando: **Web** o **client pubblico (Mobile & desktop)** . Immettere quindi l'URI di reindirizzamento o l'URL di risposta per l'applicazione.

    * Per le applicazioni Web, specificare l'URL di base dell'app. Ad esempio, `https://localhost:31544` potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web.
    * Per le applicazioni client pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico dell'applicazione, ad esempio `myapp://auth`.

    Per esempi di applicazioni Web o applicazioni native, vedere le guide introduttive nella [piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Al termine, selezionare **Registra**.

    ![Mostra la schermata per registrare una nuova applicazione nel portale di Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD assegna all'app un ID univoco dell'applicazione o del client. Il portale apre la pagina **Panoramica** dell'applicazione. Per aggiungere funzionalità all'applicazione, è possibile selezionare altre opzioni di configurazione, tra cui personalizzazione, certificati e segreti, autorizzazioni per le API e altro ancora.

![Esempio di una pagina di panoramica dell'app appena registrata](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come accedere alle API Web.
> [!div class="nextstepaction"]
> [Guida introduttiva: configurare un'applicazione client per accedere alle API Web](quickstart-configure-app-access-web-apis.md)

* Per informazioni sulle autorizzazioni, vedere [autorizzazioni e consenso nell'endpoint della piattaforma Microsoft Identity](v2-permissions-and-consent.md).

* Per esporre le API Web, vedere [Guida introduttiva: configurare un'applicazione per esporre le API Web](quickstart-configure-app-expose-web-apis.md).

* Per gestire gli account supportati, vedere [Guida introduttiva: modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md).

* Per creare un'app e aggiungere funzionalità, vedere le guide introduttive nella [piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

* Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

* Per altre informazioni sulle linee guida sulla personalizzazione da usare quando si sviluppano app, vedere [linee guida sulla personalizzazione per le applicazioni](howto-add-branding-in-azure-ad-apps.md).
