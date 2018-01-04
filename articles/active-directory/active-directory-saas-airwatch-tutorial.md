---
title: 'Esercitazione: Integrazione di Azure Active Directory con AirWatch | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a0e314aaf59a70107058829ea84dde362f097274
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Esercitazione: Integrazione di Azure Active Directory con AirWatch

Questa esercitazione descrive come integrare AirWatch con Azure Active Directory (Azure AD).

L'integrazione di AirWatch con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a AirWatch
- È possibile abilitare gli utenti per l'accesso automatico a AirWatch (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con AirWatch, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di AirWatch abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di AirWatch dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-airwatch-from-the-gallery"></a>Aggiunta di AirWatch dalla raccolta
Per configurare l'integrazione di AirWatch in Azure AD, è necessario aggiungere AirWatch dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere AirWatch dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **AirWatch**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. Nel pannello dei risultati selezionare **AirWatch** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AirWatch con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di AirWatch che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AirWatch.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in AirWatch.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AirWatch, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di AirWatch](#creating-a-airwatch-test-user)**: per avere una controparte di Britta Simon in AirWatch collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione AirWatch.

**Per configurare l'accesso Single Sign-On di Azure AD con AirWatch, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **AirWatch** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Nella sezione **URL e dominio AirWatch** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Nella casella di testo **Identificatore** digitare il valore `AirWatch`.

    > [!NOTE] 
    > Poiché non è il valore reale, aggiornarlo con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di AirWatch](http://www.air-watch.com/company/contact-us/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Nella sezione **Configurazione di AirWatch** fare clic su **Configura AirWatch** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. In un'altra finestra del Web browser accedere al sito aziendale di AirWatch come amministratore.

8. Nel riquadro di spostamento sinistro fare clic su **Accounts** e quindi su **Administrators**.
   
   ![Amministratori](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Amministratori")

9. Espandere il menu **Settings** e quindi fare clic su **Directory Services**.
   
   ![Impostazioni](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Impostazioni")

10. Fare clic sulla scheda **User** (Utente), digitare il proprio nome di dominio nella casella di testo **Base DN** (Nome distinto di base) e quindi fare clic su **Save** (Salva).
   
   ![Utente](./media/active-directory-saas-airwatch-tutorial/ic791922.png "Utente")

11. Fare clic sulla scheda **Server** .
   
   ![Server](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Server")

12. Eseguire la procedura seguente:
    
    ![Caricamento](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Caricamento")   
    
    a. Per **Directory Type** selezionare **None**.

    b. Selezionare **Use SAML For Authentication**.

    c. Per caricare il certificato scaricato, fare clic su **Upload**.

13. Nella sezione **Request** seguire questa procedura:
    
    ![Richiesta](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Richiesta")  

    a. Per **Request Binding Type** selezionare **POST**.

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in Airwatch** del portale di Azure copiare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) e quindi incollarlo nella casella di testo **Identity Provider Single Sign On URL** (URL di accesso Single Sign-On del provider di identità).

    c. Per **NameID format** selezionare **Email address**.

    d. Fare clic su **Save**.

14. Fare di nuovo clic sulla scheda **User** .
    
    ![Utente](./media/active-directory-saas-airwatch-tutorial/ic791926.png "Utente")

15. Nella sezione **Attribute** seguire questa procedura:
    
    ![Attributo](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Attributo")

    a. Nella casella di testo **Object Identifier** digitare **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. Nella casella di testo **Username** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. Nella casella di testo **Display Name** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. Nella casella di testo **First Name** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Nella casella di testo **Last Name** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Nella casella di testo **Email** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Fare clic su **Save**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-airwatch-test-user"></a>Creazione di un utente di test di AirWatch

Per consentire agli utenti di Azure AD di accedere a AirWatch, è necessario effettuarne il provisioning in AirWatch.

* Nel caso di AirWatch, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **AirWatch** come amministratore.
2. Nel riquadro di spostamento a sinistra fare clic su **Accounts** e quindi su **Users**.
   
   ![Utenti](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Utenti")
3. Dal menu **Users** scegliere **List View** e quindi fare clic su **Add \> Add User**.
   
   ![Aggiungere un utente](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Aggiungere un utente")
4. Nella finestra di dialogo **Add / Edit User** seguire questa procedura:

   ![Aggiungere un utente](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Aggiungere un utente")   
   1. Nelle caselle di testo **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** e **Email Address** digitare il nome utente, la password, la conferma password, il nome e il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AirWatch per eseguire il provisioning degli account utente di Azure AD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a AirWatch.

![Assegna utente][200] 

**Per assegnare Britta Simon a AirWatch, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **AirWatch**.

    ![Configure Single Sign-On](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

