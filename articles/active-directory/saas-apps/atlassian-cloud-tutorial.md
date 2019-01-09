---
title: 'Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: 517c92336b8c5c6827717d4c2287c41eaaa14b55
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810532"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD).
L'integrazione di Atlassian Cloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Atlassian Cloud.
* È possibile abilitare gli utenti per l'accesso automatico ad Atlassian Cloud (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Atlassian Cloud, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Atlassian Cloud abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Atlassian Cloud supporta l'accesso SSO avviato da **SP e IdP**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Aggiunta di Atlassian Cloud dalla raccolta

Per configurare l'integrazione di Atlassian Cloud in Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Atlassian Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Atlassian Cloud**, selezionare **Atlassian Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Atlassian Cloud nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Atlassian Cloud usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Atlassian Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Atlassian Cloud](#configure-atlassian-cloud-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Atlassian Cloud](#create-atlassian-cloud-test-user)**: per avere una controparte di Britta Simon in Atlassian Cloud collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Atlassian Cloud** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](common/idp-relay.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://auth.atlassian.com/saml/<unique ID>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > I valori precedenti non sono valori reali. è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Questi valori vengono ottenuti dalla schermata di configurazione SAML di Atlassian Cloud, illustrata più avanti nell'esercitazione.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](common/both-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Il valore precedente dell'URL di accesso non è reale. È necessario aggiornare il valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Atlassian Cloud](https://support.atlassian.com/).

6. L'applicazione Atlassian Cloud prevede di trovare un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi di token SAML.

    Per impostazione predefinita, viene eseguito il mapping del valore **Identificatore utente** a user.userprincipalname. Modificare questo valore per poter eseguire il mapping a user.mail. È anche possibile scegliere un altro valore appropriato in base alla configurazione dell'organizzazione, ma nella maggior parte dei casi l'indirizzo di posta elettronica andrà bene. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire questa procedura:

    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermailedit.png)

    b. Nell'elenco **Attributo di origine** selezionare **user.mail**.

    c. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Atlassian Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-atlassian-cloud-single-sign-on"></a>Configurare l'accesso Single Sign-On di Atlassian Cloud

1. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, accedere al portale di Atlassian con le credenziali di amministratore.

2. È necessario verificare il dominio prima di passare alla configurazione dell'accesso Single Sign-On. Per altre informazioni, vedere il documento [Atlassian domain verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) (Verifica del dominio di Atlassian).

3. Nel riquadro sinistro selezionare **SAML single sign-on** (Single Sign-On SAML). Se non è ancora stato fatto, sottoscrivere Atlassian Identity Manager.

    ![Configura accesso Single Sign-On](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. Nella finestra **Add SAML configuration** (Aggiungi configurazione SAML) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Nella casella **Identity provider Entity ID** (ID entità del provider di identità) incollare l'ID di entità SAML copiato dal portale di Azure.

    b. Nella casella **Identity provider SSO URL** (URL SSO del provider di identità) incollare l'URL del servizio Single Sign-On SAML copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure in un file TXT, copiare il valore (senza le righe *Begin Certificate* ed *End Certificate*) e quindi incollarlo nella casella **Public X509 certificate** (Certificato X509 pubblico).

    d. Fare clic su **Salva configurazione**.

5. Per assicurarsi di avere configurato gli URL corretti, aggiornare le impostazioni di Azure AD seguendo questa procedura:

    ![Configura accesso Single Sign-On](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Nella finestra SAML copiare l'**ID identità SP** e quindi nel portale di Azure, in **URL e dominio** Atlassian Cloud, incollarlo nella casella **Identificatore**.

    b. Nella finestra SAML copiare l'**URL del servizio consumer di asserzione SP** e quindi nel portale di Azure, in **URL e dominio** Atlassian Cloud, incollarlo nella casella **URL di risposta**. L'URL di accesso corrisponde all'URL del tenant di Atlassian Cloud.

    > [!NOTE]
    > I clienti esistenti, dopo avere aggiornato i valori **SP Identity ID** (ID identità SP) e **SP Assertion Consumer Service URL** (URL del servizio consumer di asserzione SP) nel portale di Azure, devono selezionare **Yes, update configuration** (Sì, aggiorna la configurazione). I nuovi clienti possono ignorare questo passaggio.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Atlassian Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Atlassian Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-atlassian-cloud-test-user"></a>Creare un utente di test di Atlassian Cloud

Per consentire agli utenti di Azure AD di accedere ad Atlassian Cloud, effettuare il provisioning manuale degli account utente in Atlassian Cloud seguendo questa procedura:

1. Nel riquadro **Administration** (Amministrazione) selezionare **Users** (Utenti).

    ![Collegamento Users (Utenti) di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Per creare un utente in Atlassian Cloud, selezionare **Invite user** (Invita l'utente).

    ![Creare un utente di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Nella casella **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente e quindi assegnare l'accesso all'applicazione.

    ![Creare un utente di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Per inviare un invito di posta elettronica all'utente, selezionare **Invite users** (Invita gli utenti). Un invito di posta elettronica viene inviato all'utente e, dopo avere accettato l'invito, l'utente è attivo nel sistema.

> [!NOTE]
> È anche possibile creare gli utenti in blocco selezionando il pulsante **Bulk Create** (Creazione in blocco) nella sezione **Users** (Utenti).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Atlassian Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Atlassian Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   