---
title: 'Esercitazione: Integrazione di Azure Active Directory con Absorb LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f5f20fee648cf26b4db49b9a8f5f5e5bcecd453e
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

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
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Absorb LMS dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-absorb-lms-from-the-gallery"></a>Aggiunta di Absorb LMS dalla raccolta
Per configurare l'integrazione di Absorb LMS in Azure AD, è necessario aggiungere Absorb LMS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Absorb LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Absorb LMS**, selezionare **Absorb LMS** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Absorb LMS nell'elenco risultati](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Absorb LMS usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Absorb LMS che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Absorb LMS.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore dell'attributo **Username** in Absorb LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Absorb LMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Absorb LMS](#create-an-absorb-lms-test-user)**: per avere una controparte di Britta Simon in Absorb LMS collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Absorb LMS.

**Per configurare Single Sign-On di Azure AD con Absorb LMS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Absorb LMS** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Nella sezione **URL e dominio Absorb LMS** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.myabsorb.com/Account/SAML`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.myabsorb.com/Account/SAML`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Absorb LMS](https://www.absorblms.com/support). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Absorb LMS Configuration** (Configurazione Absorb LMS) fare clic su **Configure Absorb LMS** (Configura Absorb LMS) per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione** dalla **sezione Riferimento rapido**.

    ![Configurazione di Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di Absorb LMS come amministratore.

9. Fare clic sull'**icona dell'account** nell'interfaccia di amministrazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Fare clic su **Impostazioni portale**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. Fare clic sulla scheda **Utenti** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Per accedere ai campi di configurazione dell'accesso Single Sign-On, eseguire la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Selezionare **Identity Provider Initiated** (Avviato dal provider di identità).

    b. Aprire il certificato scaricato dal portale di Azure nel Blocco note, rimuovere i tag **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** e quindi incollare il contenuto rimanente nella casella di testo **Key** (Chiave).
    
    c. Nel campo **Id Property** (Proprietà ID), selezionare l'attributo appropriato configurato come identificatore utente in Azure AD. Se, ad esempio, in Azure AD è stato selezionato userprinciplename, qui è necessario selezionare Username.

    d. Nell'**URL di accesso** incollare l'**URL di accesso utente** dalla pagina **Proprietà** dell'applicazione del portale di Azure.

    e. Nel campo **URL disconnessione** incollare il valore di **URL di disconnessione** copiato dalla finestra **Configura accesso** del portale di Azure.

13. Abilitare **Only Allow SSO Login** (Consenti solo accesso SSO).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.

### <a name="create-an-absorb-lms-test-user"></a>Creare un utente di test di Absorb LMS

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
 
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Absorb LMS.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Absorb LMS, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Absorb LMS**.

    ![Collegamento di Absorb LMS nell'elenco delle applicazioni](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

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


