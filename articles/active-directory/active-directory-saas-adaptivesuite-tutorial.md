---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adaptive Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adaptive Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 5d7ba2f4c7d814e3aaa1bf804ddc5030380ccb2d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Esercitazione: Integrazione di Azure Active Directory con Adaptive Suite

Questa esercitazione descrive come integrare Adaptive Suite con Azure Active Directory (Azure AD).

L'integrazione di Adaptive Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adaptive Suite
- È possibile abilitare gli utenti per l'accesso automatico ad Adaptive Suite (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adaptive Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Adaptive Suite abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Adaptive Suite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adaptive-suite-from-the-gallery"></a>Aggiunta di Adaptive Suite dalla raccolta
Per configurare l'integrazione di Adaptive Suite in Azure AD, è necessario aggiungere Adaptive Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Adaptive Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Adaptive Suite**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. Nel pannello dei risultati selezionare **Adaptive Suite** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adaptive Suite con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Adaptive Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adaptive Suite.

Per stabilire la relazione di collegamento, in Adaptive Suite assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adaptive Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Adaptive Suite](#creating-an-adaptive-suite-test-user)**: per avere una controparte di Britta Simon in Adaptive Suite collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adaptive Suite.

**Per configurare l'accesso Single Sign-On di Azure AD con Adaptive Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Adaptive Suite** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Nella sezione **URL e dominio Adaptive Suite** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > È possibile ottenere questo valore dalla pagina **SAML SSO Settings** di Adaptive Suite.
    >  

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Adaptive Suite** fare clic su **Configura Adaptive Suite** per aprire la finestra **Configura accesso**. Copiare **l'ID entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Adaptive Suite come amministratore.

8. Passare alla pagina **Admin**.
   
    ![Amministratore](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Amministratore")

9. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Manage SAML SSO Settings** (Gestisci impostazioni SSO SAML).
   
    ![Gestire le impostazioni SSO SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Gestire le impostazioni SSO SAML")

10. Nella pagina **SAML SSO Settings** eseguire queste operazioni:
   
    ![Impostazioni SSO SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "Impostazioni SSO SAML")

    a. Nella casella di testo **Identity provider name** digitare un nome per la configurazione.
    
    b. Incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **Identity provider Entity ID** (ID entità del provider di identità).
  
    c. Incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nella casella di testo **Identity Provider SSO URL** (URL SSO del provider di identità).
  
    d. Incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nella casella di testo **Custom logout URL** (URL di disconnessione personalizzato).
  
    e. Per caricare il certificato scaricato, fare clic su **Scegli file**.
  
    f. Selezionare le opzioni seguenti per:
    * **SAML user id** (ID utente SAML), selezionare **User's Adaptive Insights user name** (Nome utente dell'utente di Adaptive Insights).
    * **SAML user id location** (Posizione ID utente SAML), selezionare **User id in NameID of Subject** (ID utente in NameID of Subject).
    * **SAML NameID format** (Formato NameID SAML), selezionare **Email address** (Indirizzo di posta elettronica).
    * **Abilita SAML** selezionare **Allow SAML SSO and direct Adaptive Insights login** (Consenti SSO SAML e accesso diretto ad Adaptive Insights).
    
    g. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Creazione di un utente di test di Adaptive Suite

Per consentire agli utenti di Azure AD di accedere ad Adaptive Suite, è necessario effettuarne il provisioning in Adaptive Suite.  

* Nel caso di Adaptive Suite, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:** 

1. Accedere al sito aziendale di **Adaptive Suite** come amministratore.
2. Passare alla pagina **Admin**.
   
   ![Amministratore](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Amministratore")
3. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Add User** (Aggiungi utente).
   
   ![Aggiungere un utente](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Aggiungere un utente")
4. Nella sezione **Nuovo utente** seguire questa procedura:
   
   ![Invio](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Invio")   

   a. Nelle caselle di testo corrispondenti **Name**, **Login**, **Email**, **Password** digitare il nome, l'account di accesso, l'indirizzo di posta elettronica e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning.
  
   b. Selezionare un valore in **Role**.
  
   c. Fare clic su **Submit**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Adaptive Suite per eseguire il provisioning degli account utente di Azure AD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adaptive Suite.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Adaptive Suite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Adaptive Suite**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Microsoft Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Adaptive Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adaptive Suite.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

