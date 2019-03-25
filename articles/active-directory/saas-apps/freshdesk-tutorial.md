---
title: 'Esercitazione: Integrazione di Azure Active Directory con FreshDesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4bdeecc5682eb09ac0a65d3834ad1770258c4e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57874728"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con FreshDesk

Questa esercitazione descrive come integrare FreshDesk con Azure Active Directory (Azure AD).
L'integrazione di FreshDesk con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a FreshDesk.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a FreshDesk con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con FreshDesk, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di FreshDesk abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FreshDesk supporta l'accesso SSO avviato da **SP**

## <a name="adding-freshdesk-from-the-gallery"></a>Aggiunta di FreshDesk dalla raccolta

Per configurare l'integrazione di FreshDesk in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FreshDesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **FreshDesk**, selezionare **FreshDesk** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![FreshDesk nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con FreshDesk usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FreshDesk.

Per configurare e testare l'accesso Single Sign-On di Azure AD con FreshDesk, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di FreshDesk](#configure-freshdesk-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di FreshDesk](#create-freshdesk-test-user)**: per avere una controparte di Britta Simon in FreshDesk collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con FreshDesk, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **FreshDesk** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di FreshDesk](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione FreshDesk prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito di **Identificatore univoco dell'utente** è **user.userprincipalname** ma FreshDesk presuppone che ne sia stato eseguito il mapping all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione. 

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | NOME | Source Attribute |
    | ---------------| --------------- |
    | Identificatore univoco dell'utente | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Aprire il **Prompt dei comandi** ed eseguire i comandi seguenti:

    a. Immettere il valore `certutil.exe -dump FreshDesk.cer` al prompt dei comandi.

    > [!NOTE]
    > In questo comando **FreshDesk.cer** è il certificato scaricato dal portale di Azure.

    b. Copiare il valore **Hash certificato (sha256)** e incollarlo nel Blocco note. 

9. Nella sezione **Configura FreshDesk** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-freshdesk-single-sign-on"></a>Configurare l'accesso Single Sign-On per FreshDesk

1. In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.

2. Selezionare l'**icona Impostazioni** e nella sezione **Sicurezza** seguire questa procedura:

    ![Single Sign-On](./media/freshdesk-tutorial/IC776770.png "Single Sign-On")
  
    a. Per **Single Sign On (SSO)** selezionare **On**.

    b. Selezionare **SAML SSO**.

    c. Nella casella di testo **SAML Login URL** (URL di accesso SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Nella casella di testo **Security Certificate Fingerprint** (Impronta digitale certificato di protezione) incollare il valore **Hash certificato (sha256)** ottenuto in precedenza.
  
    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a FreshDesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **FreshDesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **FreshDesk**.

    ![Collegamento di FreshDesk nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-freshdesk-test-user"></a>Creare l'utente di test di FreshDesk

Per consentire agli utenti di Azure AD di accedere a FreshDesk, è necessario eseguirne il provisioning in FreshDesk.  
Nel caso di FreshDesk, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Freshdesk** .

2. Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/freshdesk-tutorial/IC776772.png "Amministratore")

3. Nella scheda **General Settings** fare clic su **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. Fare clic su **New Agent**.

    ![New Agent](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. Nella finestra di dialogo Agent Information seguire questa procedura:

    ![Agent Information](./media/freshdesk-tutorial/IC776775.png "Agent Information")

    a. Nella casella di testo **Email** , digitare l’indirizzo di posta elettronica dell'account Azure AD di cui si vuole eseguire il provisioning.

    b. Nella casella di testo **Full Name** , digitare il nome dell'account Azure AD di cui si vuole eseguire il provisioning.

    c. Nella casella di testo **Title** , digitare il titolo dell'account Azure AD di cui si vuole eseguire il provisioning.

    d. Fare clic su **Save**.

    >[!NOTE]
    >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.
    >
    >[!NOTE]
    >È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Freshdesk per eseguire il provisioning degli account utente di AAD in FreshDesk.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di FreshDesk nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione FreshDesk per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

