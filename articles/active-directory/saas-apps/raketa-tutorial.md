---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Raketa | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92511155"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Raketa

Questa esercitazione descrive come integrare Raketa con Azure Active Directory (Azure AD). Integrando Raketa con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Raketa.
* Abilitare gli utenti per l'accesso automatico a Raketa con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Raketa abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Raketa supporta l'accesso SSO avviato dal **provider di servizi** .
* Dopo aver configurato Raketa, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Aggiunta di Raketa dalla raccolta

Per configurare l'integrazione di Raketa in Azure AD, è necessario aggiungere Raketa dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Passare ad **Applicazioni aziendali** [2] e quindi selezionare **Tutte le applicazioni** [3].

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. Nella sezione **Aggiungi dalla raccolta** [5] digitare **Raketa** nella casella di ricerca [6].

1. Selezionare **Raketa** nel riquadro dei risultati [7] e quindi fare clic sul pulsante **Aggiungi** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Raketa

Configurare e testare l'accesso SSO di Azure AD con Raketa usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Raketa.

Per configurare e testare l'accesso SSO di Azure AD con Raketa, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Raketa](#configure-raketa-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Raketa](#create-raketa-test-user)** : per avere una controparte di B.Simon in Raketa collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Raketa** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** [9] selezionare **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** [11] per modificare le impostazioni.

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nelle caselle di testo **Identificatore (ID entità)** [12] e **URL di accesso** [14] digitare l'URL: `https://raketa.travel/`.

    1. Nella casella di testo **URL di risposta** [13] digitare un URL nel formato seguente: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>`.  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornarlo con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Raketa](mailto:help@raketa.travel). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** [15] per scaricare il certificato e salvarlo nel computer.

1. Nella sezione **Configura Raketa** copiare gli URL appropriati in base alle esigenze.

    1. URL di accesso [16]: URL della pagina Web di autorizzazione, usato per reindirizzare gli utenti al sistema di autenticazione.

    1. Identificatore di Azure AD [17]: identificatore di Azure AD.

    1. URL disconnessione [18]: URL della pagina Web, usato per reindirizzare gli utenti dopo la disconnessione.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** [1], **Utenti** [19] e quindi **Tutti gli utenti** [20].

1. Selezionare **Nuovo utente** [21] nella parte superiore della schermata.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. In **Proprietà utente** seguire questa procedura:

   1. Nel campo **Nome utente** [22] immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.

   1. Nel campo **Nome** [23] immettere `B.Simon`.

   1. Selezionare la casella di controllo **Mostra password** [25] e quindi prendere nota del valore visualizzato nella casella **Password** [24].

   1. Fare clic su **Crea** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Raketa.

1. Nel portale di Azure selezionare **Applicazioni aziendali** [2] e quindi **Tutte le applicazioni** [3].

1. Nell'elenco delle applicazioni selezionare **Raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Nella pagina di panoramica dell'app individuare la sezione **Gestisci** e selezionare **Utenti e gruppi** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Selezionare **Aggiungi utente** [29] e quindi **Utenti e gruppi** [30] nella finestra di dialogo **Aggiungi assegnazione** .

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** [31] dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** [32] nella parte inferiore della schermata.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Configura l'accesso Single Sign-On di Raketa

Per configurare l'accesso Single Sign-On sul lato **Raketa** , è necessario inviare il **certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Raketa](mailto:help@raketa.travel). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-raketa-test-user"></a>Creare l'utente di test di Raketa

In questa sezione viene creato un utente di nome B.Simon in Raketa. Collaborare con il [team di supporto tecnico di Raketa](mailto:help@raketa.travel) per aggiungere gli utenti alla piattaforma Raketa. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Raketa nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Raketa per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Raketa con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Raketa con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)