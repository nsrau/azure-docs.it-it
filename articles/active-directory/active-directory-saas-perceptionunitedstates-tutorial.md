---
title: 'Esercitazione: Integrazione di Azure Active Directory con Perception United States (Non-UltiPro) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Perception United States (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: d94d233a12e51bf851a791fda481b91c513d64b7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Esercitazione: Integrazione di Azure Active Directory con Perception United States (Non-UltiPro)

Questa esercitazione descrive come integrare Perception United States (Non-UltiPro) con Azure Active Directory (Azure AD).

L'integrazione di Perception United States (Non-UltiPro) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Perception United States (Non-UltiPro).
- È possibile abilitare gli utenti per l'accesso automatico a Perception United States (Non-UltiPro) (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Perception United States (Non-UltiPro), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Perception United States (Non-UltiPro) abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Perception United States (Non-UltiPro) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Aggiunta di Perception United States (Non-UltiPro) dalla raccolta
Per configurare l'integrazione di Perception United States (Non-UltiPro) in Azure AD, è necessario aggiungere Perception United States (Non-UltiPro) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Perception United States (Non-UltiPro) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Perception United States (Non-UltiPro)**, selezionare **Perception United States (Non-UltiPro)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Perception United States (Non-UltiPro) nell'elenco risultati](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Perception United States (Non-UltiPro) usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Perception United States (Non-UltiPro) corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Perception United States (Non-UltiPro).

Per stabilire la relazione di collegamento, in Perception United States (Non-UltiPro) assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Perception United States (Non-UltiPro), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Perception United States (Non-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**: per avere una controparte di Britta Simon in Perception United States (Non-UltiPro) collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Perception United States (Non-UltiPro).

**Per configurare l'accesso Single Sign-On di Azure AD con Perception United States (Non-UltiPro), seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Perception United States (Non-UltiPro)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Nella sezione **URL e dominio Perception United States (Non-UltiPro)** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://perception.kanjoya.com/sp`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Poiché non è reale, È necessario aggiornarlo con l'URL di risposta reale, descritto più avanti nell'esercitazione.
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Perception United States (Non-UltiPro)** fare clic su **Configura Perception United States (Non-UltiPro)** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    a. Per l'applicazione **Perception United States (Non-UltiPro)** è necessario il valore dell'**ID di entità SAML** copiato, che deve essere con codifica URI. Per ottenere il valore con codifica URI, usare il collegamento seguente: **http://www.url-encode-decode.com/**.

    b. Dopo aver ottenuto il valore con codifica URI, combinarlo con l'**URL di risposta** come indicato di seguito.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Incollare il valore indicato sopra nella casella di testo **URL di risposta** nella sezione **URL e dominio Perception United States (Non-UltiPro)**.

    ![Configurazione di Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. In un'altra finestra del browser accedere al sito aziendale di Perception United States (Non-UltiPro) come amministratore.

8. Sulla barra degli strumenti principale fare clic su **Account Settings** (Impostazioni account).

    ![Utente di Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Nella pagina **Account Settings** (Impostazioni account) seguire questa procedura:

    ![Utente di Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Nella casella di testo **Company Name** (Nome società) digitare il nome della **società**.
    
    b. Nella casella di testo **Account Name** (Nome account) digitare il nome dell'**account**.

    c. Nella casella di testo **Default Reply-To Email** (Indirizzo di posta elettronica predefinito per le risposte) digitare un **indirizzo**valido.

    d. Per **SSO Identity Provider** (Provider di identità SSO) selezionare **SAML 2.0**.

10. Nella pagina **SSO Configuration** (Configurazione SSO) seguire questa procedura:

    ![Configurazione SSO di Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Per **SAML NameID Type** (Tipo ID nome SAML) selezionare **EMAIL** (POSTA ELETTRONICA).

    b. Nella casella di testo **SSO Configuration Name** (Nome configurazione SSO) digitare il nome della **configurazione**.
    
    c. Nella casella di testo **Identity Provider Name** (Nome provider di identità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure. 

    d. Nella casella di testo **SAML Domain** (Dominio SAML) immettere il dominio, come **@contoso.com**.

    e. Fare clic su **Upload Again** (Carica di nuovo) per caricare il file **XML metadati**.

    f. Fare clic su **Update**.


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Creare un utente di test di Perception United States (Non-UltiPro)

In questa sezione viene creato un utente di nome Britta Simon in Perception United States (Non-UltiPro). Collaborare con il [team di supporto clienti di Perception United States (Non-UltiPro)](http://www.ultimatesoftware.com/Contact/ContactUs) per aggiungere gli utenti nella piattaforma Perception United States (Non-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Perception United States (Non-UltiPro).

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Perception United States (Non-UltiPro)y, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Perception United States (Non-UltiPro)**.

    ![Collegamento di Perception United States (Non-UltiPro) nell'elenco delle applicazioni](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Perception United States (Non-UltiPro) nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Perception United States (Non-UltiPro).
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_203.png

