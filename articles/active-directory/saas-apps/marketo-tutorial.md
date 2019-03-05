---
title: 'Esercitazione: Integrazione di Azure Active Directory con Marketo | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 6eb7c294b884b94586458f1e6e3dc283549ee31e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984289"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Esercitazione: Integrazione di Azure Active Directory con Marketo

Questa esercitazione descrive come integrare Marketo con Azure Active Directory (Azure AD).
L'integrazione di Marketo con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Marketo.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Marketo con i loro account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Marketo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Marketo abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Marketo supporta l'accesso SSO avviato da **IDP**

## <a name="adding-marketo-from-the-gallery"></a>Aggiunta di Marketo dalla raccolta

Per configurare l'integrazione di Marketo in Azure AD, è necessario aggiungere Marketo dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Marketo dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Marketo**, selezionare **Marketo** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Marketo nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Marketo usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Marketo.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Marketo, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Marketo](#configure-marketo-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Marketo](#create-marketo-test-user)**: per avere una controparte di Britta Simon in Marketo collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Marketo, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Marketo**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Marketo](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://saml.marketo.com/sp`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Marketo](http://investors.marketo.com/contactus.cfm). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Marketo** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-marketo-single-sign-on"></a>Configurare l'accesso Single Sign-On di Marketo

1. Per ottenere l'ID Munchkin dell'applicazione, accedere a Marketo usando le credenziali di amministratore ed eseguire le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic sul **collegamento Munchkin**.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiare l'ID Munchkin visualizzato sullo schermo e completare il valore per l'URL di risposta nella configurazione guidata di Azure AD.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Per configurare l'accesso Single Sign-On nell'applicazione, seguire questa procedura:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic su **Single Sign-On**.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Per abilitare le impostazioni SAML fare clic sul pulsante **Edit** (Modifica).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Impostazioni dell'accesso Single Sign-On **abilitate**.
   
    f. Incollare l'**identificatore di Azure AD** nella casella di testo **Issuer ID** (ID emittente).
   
    g. Nella casella di testo **Entity ID** (ID entità) immettere l'URL come `http://saml.marketo.com/sp`.
   
    h. Selezionare User ID Location (Percorso ID utente) come **elemento Name Identifier** (Identificatore nome).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se l'identificatore utente non è un valore UPN, modificarlo nella scheda degli attributi.
   
    i. Caricare il certificato scaricato dalla configurazione guidata di Azure AD. Fare clic su **Save** (Salva) per salvare le impostazioni.
   
    j. Modificare le impostazioni delle pagine di reindirizzamento.
   
    k. Incollare l'**URL di accesso** nella casella **Login URL** (URL di accesso).
   
    l. Incollare l'**URL di disconnessione** nella casella **Logout URL** (URL di disconnessione).
   
    m. In **Error URL** (URL errore) copiare l'**URL istanza** di Marketo e fare clic su **Save** (Salva) per salvare le impostazioni.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Per abilitare l'accesso Single Sign-On per gli utenti, completare le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu **Security** (Sicurezza) e fare clic su **Login Settings** (Impostazioni di accesso).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Selezionare l'opzione **Require SSO** (Richiedi SSO) e usare **Save** (Salva) per salvare le impostazioni.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Marketo.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Marketo**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni, selezionare **Marketo**.

    ![Collegamento a Marketo nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-marketo-test-user"></a>Creare un utente di test di Marketo

In questa sezione viene creato un utente chiamato Britta Simon in Marketo. Seguire questa procedura per creare un utente nella piattaforma Marketo.

1. Accedere all'app Marketo usando le credenziali di amministratore.

2. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Passare al menu **Security** (Sicurezza) e fare clic su **Users & Roles** (Utenti e ruoli).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Fare clic sul collegamento **Invite New User** (Invita nuovo utente) nella scheda Users (Utenti).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. La procedura guidata (Invita nuovo utente) immetterà i valori per le informazioni seguenti.
   
    a. Immettere l'indirizzo di **Email** (Posta elettronica) dell'utente nella casella di testo.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Immettere il valore per **First Name** (Nome) nella casella di testo.
   
    c. Immettere il valore per **Last Name** (Cognome) nella casella di testo.
   
    d. Fare clic su **Avanti**

6. Nella scheda **Permissions** (Autorizzazioni) selezionare i **ruoli utente** e fare clic su **Next** (Avanti).
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Fare clic sul pulsante **Send** (Invia) per inoltrare l'invito all'utente.
   
    ![Configure Single Sign-On](./media/marketo-tutorial/tutorial_marketo_18.png)

8. L'utente riceve la notifica tramite posta elettronica e deve fare clic sul collegamento e modificare la password per attivare l'account. 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Marketo nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Marketo per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

