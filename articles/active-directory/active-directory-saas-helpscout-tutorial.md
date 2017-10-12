---
title: 'Esercitazione: Integrazione di Azure Active Directory con Help Scout | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Esercitazione: Integrazione di Azure Active Directory con Help Scout

Questa esercitazione descrive come integrare Help Scout con Azure Active Directory (Azure AD).

L'integrazione di Help Scout con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Help Scout.
- È possibile abilitare gli utenti per l'accesso automatico a Help Scout (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Help Scout, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Help Scout abilitata per l'accesso Single Sign-On.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Help Scout dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-help-scout-from-the-gallery"></a>Aggiunta di Help Scout dalla raccolta
Per configurare l'integrazione di Help Scout in Azure AD, è necessario aggiungere Help Scout dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere Help Scout dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Help Scout**, selezionare **Help Scout** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Help Scout nell'elenco risultati](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Help Scout in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente di Help Scout che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Help Scout.

Help Scout usa indirizzi di posta elettronica per gli account di accesso, pertanto per stabilire la relazione di collegamento, usare lo stesso **indirizzo di posta elettronica** come **nome utente** in Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Help Scout, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Help Scout](#create-a-help-scout-test-user)**: per avere una controparte di Britta Simon in Help Scout collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Help Scout.

**Per configurare Single Sign-On di Azure AD con Help Scout, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Help Scout** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Nella sezione **URL e dominio Help Scout** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. L'**Identificatore** è il **"Audience URI (ID entità di Service Provider)"**(URI destinatario - ID entità provider di servizi) dalla Help Scout, inizia con `urn:`

    b. L'**URL di risposta** è il **"Post-back URL (Assertion Consumer Service URL)"** (URL postback - URL del servizio consumer di asserzione) dalla Guida Scout, inizia con `https://` 

    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. È necessario aggiornare questi valori dall'URL di risposta e dall'identificatore effettivi. Si ottengono questi valori dalla scheda **Single Sign-On** nella sezione Autenticazione, che è illustrata più avanti nell'esercitazione.

4. Se si desidera configurare l'applicazione in modalità avviata da **SP**, selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://secure.helpscout.net/members/login/`
     
5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. Nella sezione **Configurazione di Help Scout** fare clic su **Configura Help Scout** per aprire la finestra **Configura accesso**. Copiare **l'URL del servizio Single Sign-On SAML** dalla **sezione Riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di Help Scout come amministratore.

9. Dopo aver eseguito l'accesso, fare clic su **"Manage"** (Gestisci) dal menu principale e quindi selezionare **"Company"** (Azienda) nel menu a discesa.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Selezionare **"Authentication"** (Autenticazione) dal menu a sinistra. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Nella sezione relativa alle impostazioni SAML, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Copiare il valore **Post-back URL (Assertion Consumer Service URL)** (URL postback - URL del servizio consumer di asserzione) e incollare il valore nella casella **URL di risposta** nel portale di Azure, nella sezione Guida Scout **Dominio e URL**.
    
    b. Copiare il valore **Audience URI (ID entità di Service Provider)** (URI destinatario - ID entità provider di servizi) e incollare il valore nella casella **Identificatore** nel portale di Azure, nella sezione Guida Scout **Dominio e URL**.

12. Attiva **Abilita SAML** ed esegui la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. Nella casella di testo **URL di Single Sign-On** incollare il valore di **URL del servizio di Single Sign-On**, che è stato copiato dal portale di Azure.
    
    b. Fare clic sul pulsante **Browser** per caricare il **Certificato (Base64)** scaricato dal portale di Azure.

    c. Immettere i domini di posta elettronica domini, ad esempio `contoso.com` nella casella di testo **Email Domains** (Domini di posta elettronica). È possibile separare più domini con una virgola. In qualsiasi momento un utente di Scout Guida o un amministratore che entra in tale dominio specifico nella [pagina di accesso della Guida Scout](https://secure.helpscout.net/members/login/) verrà indirizzato al provider di identità per l'autenticazione con le proprie credenziali.

    d. Infine, è possibile attivare o disattivare **Force SAML Sign-on** (Forza accesso SAML) se si desidera che gli utenti accedano a Guida Scout solo tramite questo metodo. Se si desidera lasciare l'opzione per poter accedere con le proprie credenziali di Scout Guida, è possibile lasciarla disattivata. Anche se questa opzione è abilitata, il proprietario dell'account sarà sempre in grado di accedere a Scout Guida con la password dell'account.

    e. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-help-scout-test-user"></a>Creare un utente test di Help Scout

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Help Scout. Help Scout supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Help Scout, ne viene creato uno nuovo quando si prova ad accedere a Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a Help Scout.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Help Scout, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Help Scout**.

    ![Collegamento di Help Scout nell'elenco delle applicazioni](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Help Scout nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Help Scout.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

