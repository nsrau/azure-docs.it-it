---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con TINFOIL SECURITY | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con TINFOIL SECURITY

Questa esercitazione descrive come integrare TINFOIL SECURITY con Azure Active Directory (Azure AD). Integrando TINFOIL SECURITY con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TINFOIL SECURITY.
* Abilitare gli utenti per l'accesso automatico a TINFOIL SECURITY con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di TINFOIL SECURITY abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TINFOIL SECURITY supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Aggiunta di TINFOIL SECURITY dalla raccolta

Per configurare l'integrazione di TINFOIL SECURITY in Azure AD, è necessario aggiungere TINFOIL SECURITY dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **TINFOIL SECURITY** nella casella di ricerca.
1. Selezionare **TINFOIL SECURITY** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per TINFOIL SECURITY

Configurare e testare l'accesso SSO di Azure AD con TINFOIL SECURITY usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TINFOIL SECURITY.

Per configurare e testare l'accesso SSO di Azure AD con TINFOIL SECURITY, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di TINFOIL SECURITY](#configure-tinfoil-security-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di TINFOIL SECURITY](#create-tinfoil-security-test-user)** : per avere una controparte di B.Simon in TINFOIL SECURITY collegata alla relativa rappresentazione in Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **TINFOIL SECURITY** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. L'applicazione Visitly prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Visitly prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME | Attributo di origine |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Il valore di accountid verrà spiegato più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

1. Nella sezione **Certificato di firma SAML** copiare il valore **Valore di identificazione personale** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

1. Nella sezione **Configura TINFOIL SECURITY** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TINFOIL SECURITY.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **TINFOIL SECURITY**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-tinfoil-security-sso"></a>Configurare l'accesso Single Sign-On di TINFOIL SECURITY

1. In un'altra finestra del Web browser accedere al sito aziendale TINFOIL SECURITY come amministratore.

1. Nel barra degli strumenti in alto fare clic su **Account**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "dashboard")

1. Fare clic su **Security**.

    ![Sicurezza](./media/tinfoil-security-tutorial/ic798972.png "Security")

1. Nella pagina di configurazione **Single Sign-On** eseguire la procedura seguente:

    ![Accesso Single Sign-On](./media/tinfoil-security-tutorial/ic798973.png "Single Sign-On")

    a. Selezionare **Enable SAML**.

    b. Fare clic su **Configurazione manuale**.

    c. Nella casella di testo **SAML Post URL** (URL post SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **SAML Certificate Fingerprint** (Impronta digitale certificato SAML) incollare il valore di **Identificazione personale** copiato dalla sezione **Certificato di firma SAML**.
  
    e. Copiare il valore **Your Account ID** (ID account) e incollarlo nella casella di testo **Attributo di origine** nella sezione **Attributi utente e attestazioni** del portale di Azure.

    f. Fare clic su **Save**.

### <a name="create-tinfoil-security-test-user"></a>Creare l'utente di test di TINFOIL SECURITY

Per consentire agli utenti di Azure AD di accedere a TINFOIL SECURITY, è necessario eseguirne il provisioning in TINFOIL SECURITY. In TINFOIL SECURITY il provisioning è un'attività manuale.

**Per eseguire il provisioning di un utente, seguire questa procedura:**

1. Se l'utente fa parte di un account aziendale, è necessario [contattare il team di supporto TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) per creare l'account utente.

1. Se l'utente è un normale utente SaaS TINFOIL SECURITY, può aggiungere un collaboratore a uno qualsiasi dei siti dell'utente. Viene attivato un processo per l'invio all'indirizzo di posta elettronica specificato di un invito a creare un nuovo account utente di TINFOIL SECURITY.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da TINFOIL SECURITY per eseguire il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di TINFOIL SECURITY nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TINFOIL SECURITY per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare TINFOIL SECURITY con Azure AD](https://aad.portal.azure.com/)