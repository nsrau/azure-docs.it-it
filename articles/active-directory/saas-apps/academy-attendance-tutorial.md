---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Academy Attendance | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Academy Attendance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dced51f8-079f-45c2-936b-8b1dd790bfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e3214572042f4a6d6cf5e6e160a4d37a9f2909d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082122"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-academy-attendance"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Academy Attendance

Questa esercitazione descrive come integrare Academy Attendance con Azure Active Directory (Azure AD). Integrando Academy Attendance con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Academy Attendance.
* Abilitare gli utenti per l'accesso automatico ad Academy Attendance con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Academy Attendance abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Academy Attendance supporta l'accesso SSO avviato da **SP**


* Academy Attendance supporta il provisioning utenti **JIT**


## <a name="adding-academy-attendance-from-the-gallery"></a>Aggiunta di Academy Attendance dalla raccolta

Per configurare l'integrazione di Academy Attendance in Azure AD, è necessario aggiungere Academy Attendance dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Academy Attendance** nella casella di ricerca.
1. Selezionare **Academy Attendance** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-academy-attendance"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Academy Attendance

Configurare e testare l'accesso Single Sign-On di Azure AD con Academy Attendance usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Academy Attendance.

Per configurare e testare l'accesso SSO di Azure AD con Academy Attendance, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Academy Attendance](#configure-academy-attendance-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Academy Attendance](#create-academy-attendance-test-user)** : per avere una controparte di B.Simon in Academy Attendance collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Academy Attendance** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.aattendance.com/sso/saml2/login?idp=<IDP_NAME>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.aattendance.com/sso/saml2/metadata?idp=<IDP_NAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Academy Attendance](mailto:support@yournextconcepts.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Academy Attendance prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Academy Attendance supporta due ruoli per gli utenti: **Lecturer** (Docente) e **Student** (Studente). Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Vedere [questo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) documento che illustra come creare ruoli personalizzati in Azure AD.

1. Oltre quelli elencati in precedenza, l'applicazione Academy Attendance prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME |  Attributo di origine|
    | --------- | --------------- |
    | role      | user.assignedroles |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Academy Attendance** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Academy Attendance.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Academy Attendance**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-academy-attendance-sso"></a>Configurare l'accesso SSO di Academy Attendance

Per configurare l'accesso Single Sign-On sul lato **Academy Attendance**, è necessario inviare il file **XML dei metadati della federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Academy Attendance](mailto:support@yournextconcepts.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-academy-attendance-test-user"></a>Creare l'utente di test di Academy Attendance

In questa sezione viene creato un utente di nome Britta Simon in Academy Attendance. Academy Attendance supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Academy Attendance, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Academy Attendance nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Academy Attendance per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Academy Attendance con Azure AD](https://aad.portal.azure.com/)

