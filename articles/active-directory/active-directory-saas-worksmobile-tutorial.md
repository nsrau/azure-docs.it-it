---
title: 'Esercitazione: Integrazione di Azure Active Directory con WORKS MOBILE | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e WORKS MOBILE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8d1b3a49f15861d886822fa1a7328301e97ce37e
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Esercitazione: Integrazione di Azure Active Directory con WORKS MOBILE

Questa esercitazione descrive come integrare WORKS MOBILE con Azure Active Directory (Azure AD).

L'integrazione di WORKS MOBILE con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a WORKS MOBILE
- È possibile abilitare gli utenti per l'accesso automatico a WORKS MOBILE (SSO) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con WORKS MOBILE, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di WORKS MOBILE abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
>

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di WORKS MOBILE dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-works-mobile-from-the-gallery"></a>Aggiungere WORKS MOBILE dalla raccolta
Per configurare l'integrazione di WORKS MOBILE in Azure AD, è necessario aggiungere WORKS MOBILE dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere WORKS MOBILE dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]
2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]
4. Nella casella di ricerca digitare **WORKS MOBILE**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_001.png)

5. Nel pannello dei risultati selezionare **WORKS MOBILE** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con WORKS MOBILE con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di WORKS MOBILE che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in WORKS MOBILE.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in WORKS MOBILE.

Per configurare e testare l'accesso SSO di Azure AD con WORKS MOBILE, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di WORKS MOBILE](#creating-a-works-mobile-test-user)**: per avere una controparte di Britta Simon in WORKS MOBILE collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di gestione di Azure e viene configurato l'accesso SSO nell'applicazione WORKS MOBILE.

**Per configurare l'accesso SSO di Azure AD con WORKS MOBILE, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **WORKS MOBILE** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]
2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_01.png)
3. Nella sezione **URL e dominio WORKS MOBILE** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_02.png)
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<your-subdomain>.worksmobile.com/jp/myservice`
  2. Nella casella di testo **Identificatore** digitare il valore `worksmobile.com`.

    >[!NOTE] 
    >Questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. In questo caso, è consigliabile di usare il valore univoco della stringa nell'identificatore. Per ottenere questi valori, contattare il [team di supporto di WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com). 
    >
    >

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_03.png)     
5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_300.png)
6. Nella sezione **Certificato di firma SAML** selezionare **Make new certificate active** (Rendi attivo il nuovo certificato) e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_04.png)
7. Nella finestra popup **Rollover certificate** (Certificato di rollover) fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)
8. Nella sezione **Certificato di firma SAML** fare clic su **Certificate (Raw)** (Certificato (base)) e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_05.png) 

9. Nella sezione **Configurazione di WORKS MOBILE** fare clic su **Configura WORKS MOBILE** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_07.png)

10. Per configurare l'accesso SSO per l'applicazione, contattare [team di supporto di WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) e fornire quanto segue: • **File di certificato** scaricato • **URL del servizio Single Sign-On SAML** • **ID entità SAML** • **URL di disconnessione**
  
### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 
2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 
3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 
4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 
  1. Nella casella di testo **Nome** digitare **BrittaSimon**.
  2. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.
  3. Selezionare **Mostra password** e prendere nota del valore della **Password**.
  4. Fare clic su **Crea**. 

### <a name="create-a-works-mobile-test-user"></a>Creare un utente test WORKS MOBILE

In questa sezione viene creato un utente di nome Britta Simon in WORKS MOBILE. Collaborare con il [team di supporto di WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) per aggiungere gli utenti alla piattaforma WORKS MOBILE.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure, concedendole l'accesso a WORKS MOBILE.

![Assegna utente][200] 

**Per assegnare Britta Simon a WORKS MOBILE, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Selezionare **WORKS MOBILE**dall'elenco delle applicazioni.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.
6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.
7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro WORKS MOBILE nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione WORKS MOBILE.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png
