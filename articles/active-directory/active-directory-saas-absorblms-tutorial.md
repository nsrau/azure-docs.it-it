---
title: 'Esercitazione: Integrazione di Azure Active Directory con Absorb LMS | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 5119772dd7c3d55ed4e0ad44e71bd67b2e923409
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Absorb LMS

Questa esercitazione descrive come integrare Absorb LMS con Azure Active Directory (Azure AD).

L'integrazione di Absorb LMS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Absorb LMS.
- È possibile abilitare gli utenti per l'accesso automatico a Absorb LMS (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure Active Directory, vedere: [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Absorb LMS, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Absorb LMS abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Absorb LMS dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-absorb-lms-from-the-gallery"></a>Aggiunta di Absorb LMS dalla raccolta
Per configurare l'integrazione di Absorb LMS in Azure AD, è necessario aggiungere Absorb LMS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Absorb LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Absorb LMS**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_search.png)

5. Nel pannello dei risultati selezionare **Absorb LMS** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Absorb LMS usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Absorb LMS che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Absorb LMS.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore dell'attributo **Username** in Absorb LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Absorb LMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Absorb LMS](#creating-an-absorb-lms-test-user)**: per avere una controparte di Britta Simon in Absorb LMS collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Absorb LMS.

**Per configurare Single Sign-On di Azure AD con Absorb LMS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Absorb LMS** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Nella sezione **URL e dominio Absorb LMS**, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.myabsorb.com/Account/SAML`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.myabsorb.com/Account/SAML`

4. Selezionare **Mostra impostazioni URL avanzate** se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.myabsorb.com/`.
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. In questo caso, è consigliabile usare in Identificatore, URL di risposta e URL di accesso il valore univoco della stringa. Per ottenere questi valori, contattare il [team di supporto clienti di Absorb LMS](https://www.absorblms.com/support). 

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Absorb LMS Configuration** (Configurazione Absorb LMS) fare clic su **Configure Absorb LMS** (Configura Absorb LMS) per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di Absorb LMS come amministratore.

9. Fare clic sull'**icona dell'account** nell'interfaccia di amministrazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Fare clic su **Impostazioni portale**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11.    Fare clic sulla scheda **Utenti** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Per accedere ai campi di configurazione dell'accesso Single Sign-On, eseguire la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Selezionare la **modalità** appropriata.

    > [!NOTE]
    > Modalità: sono supportate le modalità avviate da IdP e da SP.

    b. Aprire il certificato scaricato dal portale di Azure nel blocco note, rimuovere i tag **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** e quindi incollare il contenuto rimanente nella casella di testo **Key** (Chiave).
    
    c. Nel campo **Id Property** (Proprietà ID), selezionare l'attributo appropriato configurato come identificatore utente in Azure AD. Se, ad esempio, in Azure AD è stato selezionato userprinciplename, qui è necessario selezionare Username.

    d. Nel campo **Login URL** (URL di accesso), incollare il valore dell'**URL servizio Single Sign-On SAML** copiato in precedenza dalla finestra **Configura accesso** del portale di Azure.

    e. Nel campo **Logout URL** (URL di disconnessione) incollare l'**URL di disconnessione** copiato in precedenza dalla finestra **Configura accesso** del portale di Azure.

13. Abilitare **Only Allow SSO Login** (Consenti solo accesso SSO).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.

### <a name="creating-an-absorb-lms-test-user"></a>Creazione di un utente test Absorb LMS

Per consentire agli utenti di Azure AD accedere ad Absorb LMS, è necessario eseguire il provisioning dell'account utente in Absorb LMS.  
In Absorb LMS il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Absorb LMS come amministratore.

2. Fare clic sulla scheda **Utenti** scheda.

    ![Invita persone](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Fare clic su **Utenti** nella scheda **Utenti**.

    ![Invita persone](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Dal menu a discesa **Aggiungi nuovo** selezionare **Utente**.

    ![Invita persone](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Nella pagina **Aggiungi utente** eseguire la procedura seguente:

    ![Invita persone](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Nella casella di testo **Nome** digitare Britta.

    b. Nella casella di testo **Cognome** digitare Simon.
    
    c. Nella casella di testo **Nome utente** digitare Britta Simon.

    d. Nella casella di testo **Password** digitare la password dell'account di Britta Simon.

    e. Nella casella di testo **Conferma password** digitare di nuovo la stessa password.
    
    f. Rendere la password **attiva**.    

6. Fare clic su **Salva**.
 
### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Absorb LMS.

![Assegna utente][200] 

**Per assegnare Britta Simon a Absorb LMS, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Absorb LMS**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Absorb LMS nel pannello di accesso, si accederà automaticamente all'applicazione Absorb LMS. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


