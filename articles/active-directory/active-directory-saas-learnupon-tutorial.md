---
title: 'Esercitazione: Integrazione di Azure Active Directory con LearnUpon | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: b6ac8acc244e9029be01ede5e0865c280171217d
ms.contentlocale: it-it
ms.lasthandoff: 06/24/2017

---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Esercitazione: Integrazione di Azure Active Directory con LearnUpon

Questa esercitazione descrive come integrare LearnUpon con Azure Active Directory (Azure AD).

L'integrazione di LearnUpon con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LearnUpon
- È possibile abilitare gli utenti per l'accesso automatico a LearnUpon (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LearnUpon, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di LearnUpon abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di LearnUpon dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-learnupon-from-the-gallery"></a>Aggiunta di LearnUpon dalla raccolta
Per configurare l'integrazione di LearnUpon in Azure AD, è necessario aggiungere LearnUpon dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LearnUpon dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **LearnUpon**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. Nel pannello dei risultati selezionare **LearnUpon** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LearnUpon in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di LearnUpon che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LearnUpon.

Per stabilire la relazione di collegamento, in LearnUpon assegnare il valore del **nome utente** di Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test LearnUpon](#creating-a-learnupon-test-user)** : per avere una controparte di Britta Simon in LearnUpon collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LearnUpon.

**Per configurare Single Sign-On di Azure AD con LearnUpon, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LearnUpon** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. Nella sezione **URL e dominio LearnUpon** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Si noti che questo non è il valore reale. È necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto di LearnUpon](https://www.learnupon.com/features/support/).



4. Nella sezione **Certificato di firma SAML** fare clic su **Certificate (Raw)** (Certificato (base)) e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di LearnUpon** fare clic su **Configura LearnUpon** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Aprire un'altra istanza del browser e accedere a LearnUpon con un account amministratore. 

8. Fare clic sulla scheda **Impostazioni** .
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Fare clic su **Single Sign On - SAML** e quindi fare clic su **General Settings** (Impostazioni generali) per configurare le impostazioni SAML.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. Nella sezione **General Settings** (Impostazioni generali), seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selezionare **Enabled**.

    b. Per **Version** selezionare **2.0**.

    c. Per **Skip conditions** (Ignorare le condizioni) selezionare **No**.

    d. Nella casella di testo **SAML Token Post param name** (Nome parametro POST di richiesta token SAML) digitare il nome del parametro POST di richiesta all'URL del consumer SAML indicato in precedenza che contiene l'asserzione SAML da verificare e autenticare, ad esempio **SAMLResponse**.

    e. Nella casella di testo **Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione dell'identificatore degli utenti (indirizzo di posta elettronica) nell'asserzione SAML, ad esempio **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
  
    f. Nella casella di testo **Identify Provider Location** (Identificare il percorso del provider) digitare il valore che indica il percorso a cui verranno indirizzati gli utenti quando fanno clic sull'icona caricata dalla schermata di accesso del portale di Azure.
  
    g. Nella casella di testo **Sign out URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
    
    h. Fare clic su **Manage finger prints**(Gestione impronte digitali) e quindi caricare l'impronta digitale del certificato scaricato.

11. Fare clic su **User Settings**(Impostazioni utente), e seguire questa procedura:
   
     ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. Nella casella di testo **First Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione del nome degli utenti dell'asserzione SAML, ad esempio **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. Nella casella di testo **Last Name Identifier Format** (Formato identificatore cognome) digitare il valore che indica la posizione del cognome degli utenti dell'asserzione SAML, ad esempio **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-learnupon-test-user"></a>Creazione di un utente test LearnUpon

Questa sezione descrive come creare un utente chiamato Britta Simon in LearnUpon. LearnUpon supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a LearnUpon verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il [team di supporto di LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LearnUpon.

![Assegna utente][200] 

**Per assegnare Britta Simon a LearnUpon, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **LearnUpon**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LearnUpon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LearnUpon.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png


