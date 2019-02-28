---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Webex | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ef0638afd162dc179a2a7dec54b60bfb1f89144
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869769"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex

Questa esercitazione descrive come integrare Cisco Webex con Azure Active Directory (Azure AD).
L'integrazione di Cisco Webex con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cisco Webex.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Cisco Webex con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Webex, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Cisco Webex abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Webex supporta l'accesso SSO avviato da **SP**

## <a name="adding-cisco-webex-from-the-gallery"></a>Aggiunta di Cisco Webex dalla raccolta

Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere Cisco Webex dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cisco Webex dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Cisco Webex**, selezionare **Cisco Webex** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Cisco Webex nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cisco Webex mediante un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Webex, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Cisco Webex](#configure-cisco-webex-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Cisco Webex](#create-cisco-webex-test-user)**: per avere una controparte di Britta Simon in Cisco Webex collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Cisco Webex, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Cisco Webex** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL, ad esempio: `https://web.ciscospark.com/#/signin`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://idbroker.webex.com/<Org Id>`

    > [!NOTE]
    > Il valore di questo identificatore non è reale. è necessario aggiornare questo valore con l'ID effettivo. Se sono disponibili i metadati del provider di servizi, caricarli nella sezione **Configurazione SAML di base**. A questo punto il valore di **Identificatore (ID entità)** verrà popolato automaticamente.

5. L'applicazione Cisco Webex prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aggiungere gli attributi.

    ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Cisco Webex prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | NOME |  Source Attribute|
    | ---------------|--------- |
    | UID | user.userprincipalname |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Cisco Webex** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-cisco-webex-single-sign-on"></a>Configurare l'accesso Single Sign-On di Cisco Webex

1. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.

2. Selezionare **Settings** (Impostazioni) e quindi nella sezione **Authentication** (Autenticazione) fare clic su **Modify** (Modifica).

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Selezionare **Integrate a 3rd-party identity provider. (Advanced)** (Integra provider di terze parti - Avanzate) e passare alla schermata successiva.

4. Nella pagina **Import Idp Metadata** (Importa metadati Idp) trascinare il file di metadati di Azure AD nella pagina o usare l'opzione di esplorazione dei file per individuare e caricare il file di metadati di Azure AD. Selezionare quindi **Require certificate signed by a certificate authority in Metadata (more secure)** (Richiedi certificato firmato da un'autorità di certificazione in metadati - più sicura) e fare clic su **Next** (Avanti).

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Selezionare **Test SSO Connection** (Test connessione SSO) e, quando viene aperta una nuova scheda del browser, eseguire l'autenticazione con Azure AD effettuando l'accesso.

6. Tornare alla scheda **Cisco Cloud Collaboration Management** del browser. Se il test ha avuto esito positivo, selezionare l'opzione **This test was successful. Enable Single Sign-On  (Test riuscito. Abilita Single Sign-On), quindi fare clic su **Next** (Avanti).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Cisco Webex**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cisco Webex**.

    ![Collegamento di Cisco Webex nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-cisco-webex-test-user"></a>Creare un utente di test per Cisco Webex

In questa sezione viene creato un utente chiamato Britta Simon in Cisco Webex. In questa sezione viene creato un utente chiamato Britta Simon in Cisco Webex.

1. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.

2. Fare clic su **Users** (Utenti) e quindi su **Manage Users** (Gestisci utenti).
   
    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Nella finestra **Manage User** (Gestisci utenti) selezionare **Manually add or modify users** (Aggiungi o modifica manualmente gli utenti) e fare clic su **Next** (Avanti).

4. Selezionare **Names and Email address** (Nomi e indirizzi di posta elettronica). Compilare quindi la casella di testo come indicato di seguito:

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **britta.simon@contoso.com**.

5. Fare clic sul segno più per aggiungere Britta Simon. Quindi fare clic su **Next**.

6. Nella finestra **Add Services for Users** (Aggiungi servizi per utenti) fare clic su **Save** (Salva) e quindi su **Finish** (Fine).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Cisco Webex nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Webex per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
