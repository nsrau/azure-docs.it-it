---
title: 'Esercitazione: Integrazione di Azure Active Directory con ScreenSteps | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Esercitazione: Integrazione di Azure Active Directory con ScreenSteps

Questa esercitazione descrive come integrare ScreenSteps con Azure Active Directory (Azure AD).

L'integrazione di ScreenSteps con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ScreenSteps.
- È possibile abilitare gli utenti per l'accesso automatico a ScreenSteps (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ScreenSteps, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di ScreenSteps abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di ScreenSteps dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-screensteps-from-the-gallery"></a>Aggiunta di ScreenSteps dalla raccolta
Per configurare l'integrazione di ScreenSteps in Azure AD, è necessario aggiungere ScreenSteps dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere ScreenSteps dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **ScreenSteps**, selezionare **ScreenSteps** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ScreenSteps nell'elenco risultati](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ScreenSteps usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di ScreenSteps che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ScreenSteps.

Per stabilire la relazione di collegamento, in ScreenSteps assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ScreenSteps, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di ScreenSteps](#create-a-screensteps-test-user)**: per avere una controparte di Britta Simon in ScreenSteps collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ScreenSteps.

**Per configurare Single Sign-On di Azure AD con ScreenSteps, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ScreenSteps** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Nella sezione **URL e dominio ScreenSteps** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo, come illustrato più avanti nell'esercitazione. 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di ScreenSteps** fare clic su **Configura ScreenSteps** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione di ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di ScreenSteps come amministratore.

8. Click **Account Settings** (Impostazioni account).

    ![Gestione degli account](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Gestione degli account")

9. Fare clic su **Single Sign-On**.

    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/ic778524.png "Autenticazione remota")

10. Fare clic su **Create Single Sign-on Endpoint** (Crea endpoint Single Sign-On).

    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/ic778525.png "Autenticazione remota")

11. Nella sezione **Create Single Sign-on Endpoint** (Crea endpoint Single Sign-On) seguire questa procedura:

    ![Creare un endpoint di autenticazione](./media/active-directory-saas-screensteps-tutorial/ic778526.png "Creare un endpoint di autenticazione")
    
    a. Nella casella di testo **Title** digitare un titolo.
    
    b. Nell'elenco **Mode** selezionare **SAML**.
    
    c. Fare clic su **Crea**.

12. **Modificare** il nuovo endpoint.

    ![Modifica endpoint](./media/active-directory-saas-screensteps-tutorial/ic778528.png "Modifica endpoint")

13. Nella sezione **Edit Single Sign-on Endpoint** (Modifica endpoint Single Sign-On) seguire questa procedura:

    ![Endpoint di autenticazione remota](./media/active-directory-saas-screensteps-tutorial/ic778527.png "Endpoint di autenticazione remota")

    a. Fare clic su **Upload new SAML Certificate file** (Carica il nuovo file di certificato SAML) e quindi caricare il certificato scaricato dal portale di Azure.
    
    b. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Remote Login URL** (URL accesso remoto).
    
    c. Incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **Log out URL** (URL di disconnessione).
    
    d. Selezionare un **gruppo** a cui assegnare gli utenti quando viene effettuato il provisioning.
    
    e. Fare clic su **Update**.

    f. Copiare il valore di **SAML Consumer URL** (URL consumer SAML) negli Appunti e incollarlo nella casella di testo **URL di accesso** in **URL e dominio ScreenSteps**.
    
    g. Tornare a **Edit Single Sign-on Endpoint** (Modifica endpoint Single Sign-On).
    
    h. Fare clic sul pulsante **Make default for account** (Rendi predefinito per account) per usare questo endpoint per tutti gli utenti che accedono a ScreenSteps. In alternativa, è possibile scegliere il pulsante **Add to Site** (Aggiungi a sito) per usare questo endpoint per siti specifici in **ScreenSteps**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-screensteps-test-user"></a>Creare un utente test di ScreenSteps

In questa sezione viene creato un utente di nome Britta Simon in ScreenSteps. Collaborare con il [team di supporto clienti di ScreenSteps](https://www.screensteps.com/contact) per aggiungere gli utenti nella piattaforma ScreenSteps. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a ScreenSteps.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a ScreenSteps, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **ScreenSteps**.

    ![Collegamento di ScreenSteps nell'elenco delle applicazioni](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ScreenSteps nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ScreenSteps.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png


