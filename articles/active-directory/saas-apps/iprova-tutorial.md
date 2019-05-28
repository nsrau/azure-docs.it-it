---
title: 'Esercitazione: Integrazione di Azure Active Directory con iProva | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5b66b3cf2ec038107293da7a821fcb75273fc9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718344"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Esercitazione: Integrazione di Azure Active Directory con iProva

Questa esercitazione descrive come integrare iProva con Azure Active Directory (Azure AD).
L'integrazione di iProva con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a iProva.
* È possibile abilitare gli utenti per l'accesso automatico a iProva (Single Sign-On) con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iProva sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di iProva abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* iProva supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-iprova-from-the-gallery"></a>Aggiunta di iProva dalla raccolta

Per configurare l'integrazione di iProva in Azure AD, è necessario aggiungere iProva dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere iProva dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **iProva**, selezionare **iProva** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![iProva nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iProva in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iProva.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iProva, è necessario completare i blocchi predefiniti seguenti:

1. **[Recuperare le informazioni di configurazione da iProva](#retrieve-configuration-information-from-iprova)** : operazioni preliminari per i passaggi successivi.
2. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
3. **[Configurare l'accesso Single Sign-On di iProva](#configure-iprova-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
4. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
5. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Creare un utente di test di iProva](#create-iprova-test-user)** : per avere una controparte di Britta Simon in iProva collegata alla rappresentazione dell'utente in Azure AD.
7. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="retrieve-configuration-information-from-iprova"></a>Recuperare le informazioni di configurazione da iProva

In questa sezione si recuperano da iProva le informazioni per configurare l'accesso Single Sign-On di Azure AD.

1. Aprire un Web browser e passare alla pagina **SAML2 info** (Informazioni su SAML2) in iProva usando l'URL nel formato seguente:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Visualizzare la pagina delle informazioni SAML2 in iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Lasciare aperta la scheda del browser mentre si procede con i passaggi successivi in un'altra scheda del browser.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con iProva, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [iProva](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iProva](common/sp-identifier-reply.png)

    a. Compilare la casella **Identificatore** con il valore visualizzato con l'etichetta **EntityID** (ID entità) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    b. Compilare la casella **URL di risposta** con il valore visualizzato con l'etichetta **Reply URL** (URL di risposta) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    c. Compilare la casella **URL di accesso** con il valore visualizzato con l'etichetta **Sign-on URL** (URL di accesso) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

5. L'applicazione iProva prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine| Spazio dei nomi  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nella casella di testo **Spazio dei nomi**, digitare il valore dello spazio dei nomi indicato per la riga.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurare l'accesso Single Sign-On in iProva

1. Accedere a iProva usando l'account **Administrator**.

2. Aprire il menu **Go to** (Vai a).

3. Selezionare **Application management** (Gestione applicazioni).

4. Selezionare **General** (Generale) nel pannello **System settings** (Impostazioni di sistema).

5. Selezionare **Modifica**.

6. Scorrere verso il basso fino a **Access control** (Controllo di accesso).

    ![Impostazioni di controllo di accesso di iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Individuare l'impostazione **Users are automatically logged on with their network accounts** (Gli utenti vengono connessi automaticamente con i propri account di rete) e modificarla in **Yes, authentication via SAML** (Sì, autenticazione tramite SAML). Vengono ora visualizzate le opzioni aggiuntive.

8. Selezionare **Set up** (Configura).

9. Selezionare **Avanti**.

10. iProva chiede se si vogliono scaricare i dati della federazione da un URL oppure caricarli da un file. Selezionare l'opzione **From URL** (Da URL).

    ![Scaricare i metadati di Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Incollare l'URL dei metadati salvato nell'ultimo passaggio della sezione "Configurare l'accesso Single Sign-On di Azure AD".

12. Selezionare il pulsante a forma di freccia per scaricare i metadati da Azure AD.

13. Al termine del download verrà visualizzato il messaggio di conferma **Valid Federation Data file downloaded** (File di dati di federazione valido scaricato).

14. Selezionare **Avanti**.

15. Ignorare per il momento l'opzione **Test login** (Accesso di test) e selezionare **Next** (Avanti).

16. Nell'elenco a discesa **Claim to use** (Attestazione da usare) selezionare **windowsaccountname**.

17. Selezionare **Fine**.

18. Tornare alla schermata **Edit general settings** (Modifica impostazioni generali). Scorrere verso il basso fino alla parte inferiore della pagina e fare clic su **OK** per salvare la configurazione.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione, l'utente Britta Simon viene abilitato per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a iProva.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **iProva**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **iProva**.

    ![Collegamento iProva nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-iprova-test-user"></a>Creare l'utente di test di iProva

1. Accedere a iProva usando l'account **Administrator**.

2. Aprire il menu **Go to** (Vai a).

3. Selezionare **Application management** (Gestione applicazioni).

4. Selezionare **Users** (Utenti) nel pannello **Users and user groups** (Utenti e gruppi di utenti).

5. Selezionare **Aggiungi**.

6. Nella casella **Username** (Nome utente) digitare il nome dell'utente, ad esempio `BrittaSimon@contoso.com`.

7. Nella casella **Full name** (Nome completo) immettere nome e cognome dell'utente, ad esempio **BrittaSimon**.

8. Selezionare l'opzione **No password (use single sign-on)** (Nessuna password - usa Single Sign-On).

9. Nella casella **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `BrittaSimon@contoso.com`.

10. Scorrere fino alla fine della pagina e selezionare **Finish** (Fine).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di iProva nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione iProva per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)