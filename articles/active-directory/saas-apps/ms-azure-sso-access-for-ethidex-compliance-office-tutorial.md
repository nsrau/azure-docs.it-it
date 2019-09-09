---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con MS Azure SSO Access for Ethidex Compliance Office™ | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MS Azure SSO Access for Ethidex Compliance Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0ec97683fe8597ced234fe4b7251a4daeed49f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con MS Azure SSO Access for Ethidex Compliance Office™

Questa esercitazione descrive come integrare MS Azure SSO Access for Ethidex Compliance Office™ con Azure Active Directory (Azure AD). Integrando MS Azure SSO Access for Ethidex Compliance Office™ con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a MS Azure SSO Access for Ethidex Compliance Office™.
* Abilitare gli utenti per l'accesso automatico a MS Azure SSO Access for Ethidex Compliance Office™ con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di MS Azure SSO Access for Ethidex Compliance Office™ abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MS Azure SSO Access for Ethidex Compliance Office™ supporta l'accesso Single Sign-On avviato da **IDP**

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Aggiunta di MS Azure SSO Access for Ethidex Compliance Office™ dalla raccolta

Per configurare l'integrazione di MS Azure SSO Access for Ethidex Compliance Office™ in Azure AD, è necessario aggiungere MS Azure SSO Access for Ethidex Compliance Office™ dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **MS Azure SSO Access for Ethidex Compliance Office™** nella casella di ricerca.
1. Selezionare **MS Azure SSO Access for Ethidex Compliance Office™** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per MS Azure SSO Access for Ethidex Compliance Office™

Configurare e testare l'accesso SSO di Azure AD con MS Azure SSO Access for Ethidex Compliance Office™ usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MS Azure SSO Access for Ethidex Compliance Office™.

Per configurare e testare l'accesso SSO di Azure AD con MS Azure SSO Access for Ethidex Compliance Office™, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di MS Azure SSO Access for Ethidex Compliance Office](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di MS Azure SSO Access for Ethidex Compliance Office](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** : per avere una controparte di B.Simon in MS Azure SSO Access for Ethidex Compliance Office™ collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **MS Azure SSO Access for Ethidex Compliance Office™** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.


1. L'applicazione MS Azure SSO Access for Ethidex Compliance Office™ prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione MS Azure SSO Access for Ethidex Compliance Office™ prevede che **nameidentifier** sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura MS Azure SSO Access for Ethidex Compliance Office™** copiare gli URL appropriati in base alle esigenze.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MS Azure SSO Access for Ethidex Compliance Office™.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **MS Azure SSO Access for Ethidex Compliance Office™**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Configurare l'accesso Single Sign-On di MS Azure SSO Access for Ethidex Compliance Office

Per configurare l'accesso Single Sign-On sul lato **MS Azure SSO Access for Ethidex Compliance Office™**, è necessario inviare il **Certificato (base)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di MS Azure SSO Access for Ethidex Compliance Office™](mailto:support@ethidex.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Creare l'utente di test di MS Azure SSO Access for Ethidex Compliance Office

In questa sezione viene creato un utente di nome B.Simon in MS Azure SSO Access for Ethidex Compliance Office™. Collaborare con il  [team di supporto di MS Azure SSO Access for Ethidex Compliance Office™](mailto:support@ethidex.com) per aggiungere gli utenti alla piattaforma MS Azure SSO Access for Ethidex Compliance Office™. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di MS Azure SSO Access for Ethidex Compliance Office™ nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di MS Azure SSO Access for Ethidex Compliance Office™ per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare MS Azure SSO Access for Ethidex Compliance Office™ con Azure AD](https://aad.portal.azure.com/)

