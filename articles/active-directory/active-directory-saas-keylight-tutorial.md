---
title: 'Esercitazione: Integrazione di Azure Active Directory con LockPath Keyligh | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 74d9d80a1ce35f1766261038a985cc6f0cc39fd3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Esercitazione: Integrazione di Azure Active Directory con LockPath Keylight

Questa esercitazione descrive come integrare LockPath Keylight con Azure Active Directory (Azure AD).

L'integrazione di LockPath Keylight con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LockPath Keylight
- È possibile abilitare gli utenti per l'accesso automatico a LockPath Keylight (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LockPath Keylight, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di LockPath Keylight abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di LockPath Keylight dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Aggiunta di LockPath Keylight dalla raccolta
Per configurare l'integrazione di LockPath Keylight in Azure AD, è necessario aggiungere LockPath Keylight dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere LockPath Keylight dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **LockPath Keylight**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. Nel pannello dei risultati selezionare **LockPath Keylight** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LockPath Keylight in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di LockPath Keylight corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LockPath Keylight.

Per stabilire la relazione di collegamento, in LockPath Keylight assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con LockPath Keylight, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di LockPath Keylight](#creating-a-lockpath-keylight-test-user)**: per avere una controparte di Britta Simon in LockPath Keylight collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LockPath Keylight.

**Per configurare l'accesso Single Sign-On di Azure AD con LockPath Keylight, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LockPath Keylight** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. Nella sezione **URL e dominio LockPath Keylight** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.keylightgrc.com/`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company name>.keylightgrc.com`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di LockPath Keylight](https://www.lockpath.com/contact/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (base)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. Nella sezione **Configurazione di LockPath Keylight** fare clic su **Configura LockPath Keylight** per visualizzare la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Per abilitare l'accesso Single Sign-On in LockPath Keylight, seguire questa procedura:
   
    a. Accedere al proprio account LockPath Keylight come amministratore.
    
    b. Nel menu in alto fare clic su **Person** (Persona) e selezionare **Keylight Setup** (Configurazione Keylight).
   
    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Nella visualizzazione albero a sinistra fare clic su **SAML**.
   
    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) fare clic su **Edit** (Modifica).
   
    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png) 

8. Nella pagina della finestra di dialogo **Edit SAML Settings (Modifica impostazioni SAML)** seguire questa procedura:
   
    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Impostare **SAML authentication** (Autenticazione SAML) su **Active** (Attiva).

    b. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Identity Provider Login URL** (URL accesso provider di identità).

    c. Incollare il valore dell'**URL del servizio Single Sign-Out** copiato dal portale di Azure nella casella di testo **Identity Provider Logout URL** (URL disconnessione provider di identità).

    d. Fare clic su **Choose File** (Scegli file) per selezionare il certificato LockPath Keylight scaricato e quindi fare clic su **Open** (Apri) per caricare il certificato.

    e. Impostare **SAML User Id location** (Posizione ID utente SAML) su **NameIdentifier element of the subject statement** (Elemento NameIdentifier dell'istruzione Subject).
    
    f. Specificare il **provider del servizio Keylight** usando il modello seguente: **https://&lt;CompanyName&gt;.keylightgrc.com**.
    
    g. Impostare **Auto-provision users** (Utenti che eseguono il provisioning automatico) su **Active** (Attivo).

    h. Impostare **Auto-provision account type** (Tipo di account di provisioning automatico) su **Full User** (Utente completo).

    i. Impostare **Auto-provision security role** (Ruolo di sicurezza del provisioning automatico) selezionando **Standard User with SAML** (Utente standard con SAML).
    
    j. Impostare **Auto-provision security config** (Configurazione di sicurezza del provisioning automatico) selezionando **Standard User Configuration** (Configurazione utente standard).
     
    k. Nella casella di testo **Email attribute** (Attributo posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Nella casella di testo **First name attribute** (Attributo nome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Nella casella di testo **Last name attribute** (Attributo cognome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Creazione di un utente test di LockPath Keylight

In questa sezione si crea un utente chiamato Britta Simon in LockPath Keylight. LockPath Keylight supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a LockPath Keylight se l'utente non esiste ancora, viene creato un nuovo utente. 

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il [team di supporto clienti di LockPath Keylight](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LockPath Keylight.

![Assegna utente][200] 

**Per assegnare Britta Simon a LockPath Keylight, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **LockPath Keylight**.

    ![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LockPath Keylight nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LockPath Keylight. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

