---
title: 'Esercitazione: Integrazione di Azure Active Directory con TINFOIL SECURITY | Microsoft Docs'
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
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262381"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Esercitazione: Integrazione di Azure Active Directory con TINFOIL SECURITY

Questa esercitazione descrive come integrare TINFOIL SECURITY con Azure Active Directory (Azure AD).
L'integrazione di TINFOIL SECURITY con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TINFOIL SECURITY.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TINFOIL SECURITY con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TINFOIL SECURITY, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di TINFOIL SECURITY abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TINFOIL SECURITY supporta l'accesso SSO avviato da **IDP**

## <a name="adding-tinfoil-security-from-the-gallery"></a>Aggiunta di TINFOIL SECURITY dalla raccolta

Per configurare l'integrazione di TINFOIL SECURITY in Azure AD, è necessario aggiungere TINFOIL SECURITY dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TINFOIL SECURITY dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **TINFOIL SECURITY**, selezionare **TINFOIL SECURITY** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![TINFOIL SECURITY nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY con un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TINFOIL SECURITY.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per TINFOIL SECURITY](#configure-tinfoil-security-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di TINFOIL SECURITY](#create-tinfoil-security-test-user)**: per avere una controparte di Britta Simon in TINFOIL SECURITY collegata alla relativa rappresentazione in Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [TINFOIL SECURITY](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TINFOIL SECURITY](common/preintegrated.png)

5. L'applicazione TINFOIL SECURITY prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona  **Modifica** per aprire la finestra di dialogo **Attributi utente**. 

        ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione TINFOIL SECURITY prevede il passaggio di altri attributi nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nella casella di testo **Attributo di origine** incollare il valore di ID account ottenuto più avanti nell'esercitazione.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

8. Nella sezione **Certificato di firma SAML** copiare il valore **Identificazione personale** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

9. Nella sezione **Configura TINFOIL SECURITY** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-tinfoil-security-single-sign-on"></a>Configurare l'accesso Single Sign-On per TINFOIL SECURITY

1. In un'altra finestra del Web browser accedere al sito aziendale TINFOIL SECURITY come amministratore.

2. Nel barra degli strumenti in alto fare clic su **Account**.
   
    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

3. Fare clic su **Security**.
   
    ![Sicurezza](./media/tinfoil-security-tutorial/ic798972.png "Sicurezza")

4. Nella pagina di configurazione **Single Sign-On** eseguire la procedura seguente:
   
    ![Single Sign-On](./media/tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. Selezionare **Enable SAML**.
   
    b. Fare clic su **Configurazione manuale**.
   
    c. Nella casella di testo **SAML Post URL** (URL post SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    d. Nella casella di testo **SAML Certificate Fingerprint** (Impronta digitale certificato SAML) incollare il valore di **Identificazione personale** copiato dalla sezione **Certificato di firma SAML**.
  
    e. Copiare il valore **Your Account ID** (ID account) e incollarlo nella casella di testo **Valore attributo** nella sezione **Aggiungi attributo** nel portale di Azure.
   
    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TINFOIL SECURITY.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **TINFOIL SECURITY**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TINFOIL SECURITY**.

    ![Collegamento di TINFOIL SECURITY nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-tinfoil-security-test-user"></a>Creare l'utente di test di TINFOIL SECURITY

Per consentire agli utenti di Azure AD di accedere a TINFOIL SECURITY, è necessario eseguirne il provisioning in TINFOIL SECURITY. In TINFOIL SECURITY il provisioning è un'attività manuale.

**Per eseguire il provisioning di un utente, eseguire la procedura seguente:**

1. Se l'utente fa parte di un account aziendale, è necessario [contattare il team di supporto TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) per creare l'account utente.

1. Se l'utente è un normale utente SaaS TINFOIL SECURITY, può aggiungere un collaboratore a uno qualsiasi dei siti dell'utente. Viene attivato un processo per l'invio all'indirizzo di posta elettronica specificato di un invito a creare un nuovo account utente di TINFOIL SECURITY.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da TINFOIL SECURITY per eseguire il provisioning degli account utente di Azure AD.
> 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di TINFOIL SECURITY nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TINFOIL SECURITY per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

