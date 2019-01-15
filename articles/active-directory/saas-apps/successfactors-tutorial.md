---
title: 'Esercitazione: Integrazione di Azure Active Directory con SuccessFactors | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.openlocfilehash: 4ac9837389d681aadabd280bd6f74884a5a15ba3
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065107"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Esercitazione: Integrazione di Azure Active Directory con SuccessFactors

Questa esercitazione descrive come integrare SuccessFactors con Azure Active Directory (Azure AD).
L'integrazione di SuccessFactors con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SuccessFactors.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SuccessFactors con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SuccessFactors, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SuccessFactors abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SuccessFactors supporta l'accesso SSO avviato da **SP**

## <a name="adding-successfactors-from-the-gallery"></a>Aggiunta di SuccessFactors dalla raccolta

Per configurare l'integrazione di SuccessFactors in Azure AD, è necessario SuccessFactors dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SuccessFactors dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SuccessFactors**, selezionare **SuccessFactors** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SuccessFactors nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SuccessFactors usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SuccessFactors.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SuccessFactors, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SuccessFactors](#configure-successfactors-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SuccessFactors](#create-successfactors-test-user)**: per avere una controparte di Britta Simon in SuccessFactors collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SuccessFactors, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SuccessFactors** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SuccessFactors](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Questi valori non sono reali. Aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contattare il [team di supporto clienti di SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) per ottenere questi valori.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SuccessFactors** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL disconnessione

### <a name="configure-successfactors-single-sign-on"></a>Configurare l'accesso Single Sign-On per SuccessFactors

1. In un'altra finestra del Web browser accedere al **portale di amministrazione di SuccessFactors** come amministratore.

2. Vedere **Sicurezza applicazione** e **Single Sign On Feature** (Funzionalità di accesso Single Sign-On).

3. Inserire un valore qualsiasi in **Reset Token** (Reimposta token) e fare clic su **Save Token** (Salva token) per abilitare SSO SAML.

    ![Configurazione dell'accesso Single Sign-On sul lato app][11]

    > [!NOTE]
    > Questo valore viene usato come opzione di attivazione o disattivazione. Se viene salvato un valore, SSO SAML è ON. Se viene salvato un valore vuoto, SSO SAML è OFF.

4. Nella schermata sottostante eseguire le azioni seguenti:

    ![Configurazione dell'accesso Single Sign-On sul lato app][12]
  
    a. Selezionare il pulsante di opzione **SAML v2 SSO** (SSO SAML v2)
  
    b. Impostare **SAML Asserting Party Name** (Nome dell'entità di asserzione SAML), ad esempio Autorità di certificazione SAML + Nome società.

    c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Selezionare **Asserzione** per **Require Mandatory Signature** (Richiedi firma obbligatoria).

    e. Selezionare **Abilitato** per **Enable SAML Flag** (Abilita flag SAML).

    f. Selezionare **No** per **Login Request Signature(SF Generated/SP/RP)** (Firma richiesta di accesso - Generata da SF/SP/RP).

    g. Selezionare **Browser/Post Profile** (Profilo browser/post) per **SAML Profile** (Profilo SAML).

    h. Selezionare **No** per **Enforce Certificate Valid Period** (Applicare periodo di validità certificato).

    i. Copiare il contenuto del file del certificato scaricato dal portale di Azure, copiarlo e incollarlo nella casella di testo **SAML Verifying Certificate** (Verifica certificato SAML).

    > [!NOTE] 
    > Il contenuto del certificato deve avere i tag di inizio e fine certificato.

5. Passare a SAML V2 e seguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On sul lato app][13]

    a. Selezionare **Sì** per **Support SP-initiated Global Logout** (Supporto disconnessione globale avviata da SP).

    b. Nella casella di testo **Global Logout Service URL (LogoutRequest destination)** (URL del servizio disconnessione globale - Destinazione LogoutRequest) incollare il valore **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    c. Selezionare **No** per **Require sp must encrypt all NameID element** (Richiesta a sp di crittografare tutti gli elementi NameID).

    d. Selezionare **non specificato** per **NameID Format** (Formato NameID).

    e. Selezionare **Sì** per **Enable sp initiated login (AuthnRequest)** (Abilita accesso avviato da sp - AuthnRequest).

    f. Nella casella di testo **Send request as Company-Wide issuer** (Invia richiesta come autorità di certificazione a livello di azienda) incollare il valore di **URL di accesso** copiato dal portale di Azure.

6. Eseguire questi passaggi se si vuole che i nomi utente di account di accesso utente non facciano distinzione tra maiuscole e minuscole.

    ![Configure Single Sign-On][29]

    a. Vedere **Impostazioni società** in basso.

    b. Selezionare la casella di controllo accanto alla **Enable Non-Case-Sensitive Username** (Abilita nome utente senza distinzione maiuscole/minuscole).

    c. Fare clic su **Save**.

    > [!NOTE]
    > Se si prova ad abilitare questa opzione, il sistema controlla se viene creato un nome di account di accesso SAML duplicato. Ad esempio, se il cliente ha nomi utente Utente1 e utente1. L'annullamento della distinzione maiuscole/minuscole crea questi duplicati. Il sistema visualizza un messaggio di errore e non abilita la funzionalità. Il cliente deve modificare uno dei nomi utente in modo che sia scritto diversamente.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SuccessFactors.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SuccessFactors**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SuccessFactors**.

    ![Collegamento di SuccessFactors nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-successfactors-test-user"></a>Creare l'utente di test di SuccessFactors

Per consentire agli utenti di Azure AD di accedere a SuccessFactors, è necessario effettuarne il provisioning in SuccessFactors. Nel caso di SuccessFactors, il provisioning è un'attività manuale.

Per creare utenti in SuccessFactors, è necessario contattare il [team di supporto di SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SuccessFactors nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SuccessFactors per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png