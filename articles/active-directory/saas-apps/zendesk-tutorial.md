---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zendesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11370e651c338de3d139a4bb70c9658090052469
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901781"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Esercitazione: Integrazione di Azure Active Directory con Zendesk

Questa esercitazione descrive come integrare Zendesk con Azure Active Directory (Azure AD).
L'integrazione di Zendesk con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Zendesk.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Zendesk con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zendesk, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Zendesk abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zendesk supporta l'accesso SSO avviato da **SP**

* Zendesk supporta il provisioning utenti [**automatico**](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Aggiunta di Zendesk dalla raccolta

Per configurare l'integrazione di Zendesk in Azure AD, è necessario aggiungerla dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zendesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zendesk**, selezionare **Zendesk** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zendesk usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zendesk.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zendesk, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Zendesk](#configure-zendesk-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Zendesk](#create-zendesk-test-user)**: per avere una controparte di Britta Simon in Zendesk collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zendesk, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Zendesk** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zendesk](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.zendesk.com`

   b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto del client Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Slack prevede un formato specifico per le asserzioni SAML. Non vi sono attributi SAML obbligatori ma, facoltativamente, è possibile gestirli dalla sezione **Attributi utente** della pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo appropriato.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    > [!NOTE]
    > Usare gli attributi dell'estensione per aggiungere attributi che non sono in Azure AD per impostazione predefinita. Fare clic su [User attributes that can be set in SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) (Attributi utente che possono essere impostati in SAML) per ottenere l'elenco completo degli attributi accettati da **Zendesk**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML** copiare il valore di **Identificazione personale** nella sezione **Certificato di firma SAML** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-certificatethumbprint.png)

8. Nella sezione **Configura Zendesk** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

9. Esistono due modi per configurare Zendesk: automaticamente e manualmente.
  
10. Per automatizzare la configurazione all'interno di Zendesk, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Zendesk** (Configura Zendesk) per passare direttamente all'applicazione Zendesk. Da qui, fornire le credenziali di amministratore per accedere a Zendesk. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi della sezione **Configurare l'accesso Single Sign-On per Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configurare l'accesso Single Sign-On per Zendesk

1. Se si vuole configurare manualmente Zendesk, aprire una nuova finestra del Web browser, accedere al sito aziendale di Zendesk come amministratore e seguire questa procedura:

2. Fare clic su **Admin**.

3. Nel riquadro di spostamento sinistro fare clic su **Impostazioni** e quindi su **Sicurezza**.

4. Nella pagina **Sicurezza** eseguire la procedura seguente:

    ![Sicurezza](././media/zendesk-tutorial/ic773089.png "Sicurezza")

    ![Single Sign-On](././media/zendesk-tutorial/ic773090.png "Single Sign-On")

    a. Fare clic sulla scheda **Amministratore e agenti**.

    b. Selezionare **Single sign-on (SSO) e SAML** e quindi **SAML**.

    c. Nella casella di testo **URL SSO SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **URL disconnessione remota** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure.

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
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zendesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zendesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Zendesk**.

    ![Collegamento di Zendesk nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-zendesk-test-user"></a>Creare l'utente di test di Zendesk

Questa sezione descrive come creare un utente chiamato Britta Simon in Zendesk. Zendesk supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](Zendesk-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

> [!NOTE]
> All'accesso viene eseguito automaticamente il provisioning degli account dell'**utente finale**. Il provisioning degli account **Agente** e **Amministratore** devono essere eseguiti manualmente in **Zendesk** prima dell'accesso.

1. Accedere al tenant di **Zendesk** .

2. Selezionare la scheda **Customer List** .

3. Selezionare la scheda **User** e fare clic su **Add**.

    ![Aggiungere un utente](././media/zendesk-tutorial/ic773632.png "Aggiungere un utente")
4. Digitare il **Nome** e l'**Indirizzo di posta elettronica** di un account Azure AD esistente da sottoporre a provisioning, quindi fare clic su **Salva**.

    ![Nuovo utente](././media/zendesk-tutorial/ic773633.png "Nuovo utente")

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zendesk per eseguire il provisioning degli account utente Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Zendesk nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zendesk per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configura provisioning utenti](zendesk-provisioning-tutorial.md)