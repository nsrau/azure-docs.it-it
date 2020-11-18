---
title: "Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)"
description: Questa guida introduttiva usa il portale di Azure per aggiungere un'applicazione della raccolta al tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: fd81e5f87aaf4a28676b79863df60b71707849e9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656514"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)

Azure Active Directory (Azure AD) offre una raccolta contenente migliaia di applicazioni preintegrate. Molte delle applicazioni usate dall'organizzazione sono probabilmente già presenti nella raccolta.

Dopo l'aggiunta di un'applicazione al tenant di Azure AD, è possibile:

- Configurare le proprietà per l'app.
- Gestire l'accesso degli utenti all'app con criteri di accesso condizionale.
- Configurare l'accesso Single Sign-On in modo che gli utenti possano accedere all'app con le credenziali Azure AD personali.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere un'applicazione al tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- (Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md)).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Aggiungere un'app al tenant di Azure AD

Per aggiungere un'applicazione al tenant di Azure AD:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
2. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali**. Verrà aperto il riquadro **Tutte le applicazioni**, in cui è visualizzato un campione casuale delle applicazioni presenti nel tenant di Azure AD.
3. Nel riquadro **Applicazioni aziendali** selezionare **Nuova applicazione**. 
    ![Selezionare Nuova applicazione per aggiungere un'app della raccolta al tenant](media/add-application-portal/new-application.png)
4. Passare alla nuova esperienza di anteprima della raccolta: Nel banner nella parte superiore della **pagina Aggiungi applicazione** selezionare il collegamento **Fare clic qui per provare la nuova raccolta di app migliorata**.
5. Viene visualizzato il riquadro **Esplora la raccolta di Azure AD (anteprima)** , contenente sezioni per le piattaforme cloud, le applicazioni locali e le applicazioni in primo piano. Per le applicazioni elencate nella sezione **Applicazioni in primo piano** vengono visualizzate icone che indicano se l'accesso Single Sign-On (SSO) federato e il provisioning sono supportati. 
    ![Cercare un'app per nome o categoria](media/add-application-portal/browse-gallery.png)
6. È possibile sfogliare la raccolta per individuare l'applicazione da aggiungere oppure cercare l'applicazione immettendone il nome nella casella di ricerca. Selezionare quindi l'applicazione nei risultati. 
7. Il passaggio successivo varia a seconda del modo in cui lo sviluppatore dell'applicazione ha implementato l'accesso Single Sign-On (SSO). L'accesso Single Sign-On può essere implementato in quattro modi, ovvero in base a SAML, OpenID Connect, password e collegamento. Quando si aggiunge un'app, è possibile scegliere di filtrare e vedere solo le app che usano una specifica implementazione di SSO, come illustrato nello screenshot. Ad esempio, uno standard comune per implementare SSO è SAML (Security Assertion Markup Language). Un altro standard comune è OIDC (OpenId Connect). Il modo in cui si configura l'accesso SSO con questi standard è diverso, quindi prendere nota del tipo di SSO implementato dall'app da aggiungere.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Screenshot che mostra il selettore dei tipi di SSO." lightbox="media/add-application-portal/sso-types.png":::

    - Se lo sviluppatore dell'app ha usato lo **standard OIDC** per l'accesso SSO, selezionare **Iscrizione**. Viene visualizzata una pagina di configurazione. Passare quindi alla guida di avvio rapido sulla configurazione dell'accesso Single Sign-On basato su OIDC.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Screenshot che mostra l'aggiunta di un'app con accesso SSO basato su OIDC.":::

    - Se lo sviluppatore dell'app ha usato lo **standard SAML** per l'accesso SSO, selezionare **Crea**. Verrà visualizzata una pagina Attività iniziali con le opzioni per configurare l'applicazione per l'organizzazione. Nel modulo è possibile modificare il nome dell'applicazione in base alle esigenze dell'organizzazione. Passare quindi alla guida di avvio rapido sulla configurazione dell'accesso Single Sign-On basato su SAML.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="Screenshot che mostra l'aggiunta di un'app con accesso SSO basato su SAML.":::


> [!IMPORTANT]
> Esistono alcune differenze importanti tra le implementazioni di SSO basate su SAML e su OIDC. Con le app basate su SAML è possibile aggiungere più istanze della stessa app, ad esempio GitHub1, GitHub2 e così via. Per le app basate su OIDC è possibile aggiungere una sola istanza di un'app. Se un'app basata su OIDC è già stata aggiunta e si prova ad aggiungerla di nuovo fornendo due volte il consenso, non verrà aggiunta nuovamente nel tenant.

Se l'applicazione che si cerca non si trova nella raccolta, è possibile selezionare il collegamento **Crea un'applicazione personalizzata** e quindi in **Che cosa si vuole fare con l'applicazione?** scegliere **Integrare qualsiasi altra applicazione non trovata nella raccolta**. Microsoft ha collaborato con molti sviluppatori di applicazioni per preconfigurarle per l'uso con Azure AD. Le app preconfigurate vengono visualizzate nella raccolta. Ma se l'app da aggiungere non è presente nell'elenco, è possibile crearne una nuova generica e quindi configurarla autonomamente o con le indicazioni dello sviluppatore che l'ha creata.

L'aggiunta di un'applicazione è stata completata. L'argomento di avvio rapido successivo illustra come modificare il logo e altre proprietà dell'applicazione.

> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare con questa serie di argomenti di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un'app, passare all'articolo successivo.
> [!div class="nextstepaction"]
> [Configurare un'app](add-application-portal-configure.md)