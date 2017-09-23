---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Sign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b413772de1af1fbb128d29b81e5831cfd6a39ab4
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Sign

Questa esercitazione descrive come integrare Adobe Sign con Azure Active Directory (Azure AD).

L'integrazione di Adobe Sign con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Adobe Sign
- È possibile abilitare gli utenti per l'accesso automatico a Adobe Sign (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Sign, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Adobe Sign abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Adobe Sign dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adobe-sign-from-the-gallery"></a>Aggiunta di Adobe Sign dalla raccolta
Per configurare l'integrazione di Adobe Sign in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Adobe Sign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Adobe Sign**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Nel pannello dei risultati selezionare **Adobe Sign** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Sign usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Adobe Sign che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Sign.

Per stabilire la relazione di collegamento, in Adobe Sign assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Sign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Adobe Sign](#creating-an-adobe-sign-test-user)**: per avere una controparte di Britta Simon in Adobe Sign collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Sign.

**Per configurare l'accesso Single Sign-On di Azure AD con Adobe Sign, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Adobe Sign** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Nella sezione **Adobe Sign Domain and URLs** (URL e dominio Adobe Sign) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.echosign.com/`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. Nella sezione **Adobe Sign Configuration** (Configurazione Adobe Sign) fare clic su **Configure Adobe Sign** (Configura Adobe Sign) per aprire la finestra **Configure sign-on** (Configura accesso). Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png) 


7. In un'altra finestra del Web browser accedere al sito aziendale di Adobe Sign come amministratore.

8. Nel menu in alto fare clic su **Account** e quindi nel riquadro di spostamento a sinistra fare clic su **SAML Settings** (Impostazioni SAML) in **Account Settings** (Impostazioni account).
   
   ![Account](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "Account")

9. Nella sezione relativa alle impostazioni SAML, seguire questa procedura:
   
   ![Impostazioni SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "Impostazioni SAML")
   
   a. Per **SAML Mode** (Modalità SAML), selezionare **SAML Mandatory** (SAML obbligatorio).
   
   b. Selezionare **Allow EchoSign Account Administrators to log in using their EchoSign Credentials**.
   
   c. In **User Creation** (Creazione utente), selezionare **Automatically add users authenticated through SAML** (Aggiungi automaticamente gli utenti autenticati tramite SAML).

10. Continuare e seguire questa procedura:

       ![Impostazioni SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789522.png "Impostazioni SAML")

    a. Incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **IdP Entity ID** (ID entità IdP).
    
    b. Incollare **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nella casella di testo **IdP Login URL** (ID accesso IdP).
   
    c. Incollare l'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **IdP Logout URL** (ID disconnessione IdP).

    d. Aprire **Certificate(Base64)** scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **IdP Certificate** (Certificato IdP)

    e. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Creazione di un utente di test di Adobe Sign

Per consentire agli utenti di Azure AD di accedere a Adobe Sign, è necessario eseguirne il provisioning in Adobe Sign. Nel caso di Adobe Sign, il provisioning è un'attività manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Adobe Sign per eseguire il provisioning degli account utente di AAD. 

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Adobe Sign** come amministratore.

2. Nel menu in alto fare clic su **Account** e quindi nel riquadro di spostamento a sinistra fare clic su **Users & Groups** (Utenti e gruppi) e su **Create a new user** (Crea nuovo utente).
   
   ![Account](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "Account")
   
3. Nella sezione **Create New User** seguire questa procedura:
   
   ![Crea utente](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "Crea utente")
   
   a. Digitare l'**indirizzo di posta elettronica**, il **nome** e il **cognome** di un account utente AAD valido di cui si vuole eseguire il provisioning.
   
   b. Fare clic su **Create User**.

>[!NOTE]
>Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Sign.

![Assegna utente][200] 

**Per assegnare Britta Simon a Adobe Sign, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Adobe Sign**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Quando si fa clic sul riquadro Adobe Sign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Sign.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png


