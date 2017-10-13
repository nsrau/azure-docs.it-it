---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: f0688cb75584ada805b944d2ef5409d66ab37339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho

Questa esercitazione descrive come integrare Zoho con Azure Active Directory (Azure AD).

L'integrazione di Zoho con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zoho.
- È possibile abilitare gli utenti per l'accesso automatico a Zoho (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zoho, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Zoho abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Zoho dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zoho-from-the-gallery"></a>Aggiunta di Zoho dalla raccolta
Per configurare l'integrazione di Zoho in Azure AD, è necessario aggiungere Zoho dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zoho dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Zoho**, selezionare **Zoho** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zoho nell'elenco risultati](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zoho usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Zoho corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zoho.

Per stabilire la relazione di collegamento, in Zoho assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zoho, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Zoho](#create-a-zoho-test-user)**: per avere una controparte di Britta Simon in Zoho collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zoho.

**Per configurare l'accesso Single Sign-On di Azure AD con Zoho, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zoho** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Nella sezione **URL e dominio Zoho** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Zoho](https://www.zoho.com/mail/contact.html). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Zoho** fare clic su **Configura Zoho** per aprire la finestra **Configura accesso**. Copiare **URL di disconnessione, URL di modifica password e URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configurazione di Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Zoho Mail come amministratore.

8. Passare a **Control panel**.
   
    ![Pannello di controllo](./media/active-directory-saas-zoho-mail-tutorial/ic789607.png "Pannello di controllo")

9. Fare clic sulla scheda **SAML Authentication** .
   
    ![Autenticazione SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789608.png "Autenticazione SAML")

10. Nella sezione **SAML Authentication Details** seguire questa procedura:
   
    ![Dettagli dell'autenticazione SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789609.png "Dettagli dell'autenticazione SAML")
   
    a. Nella casella di testo **Login URL** (URL di accesso) incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
   
    b. Nella casella di testo **Logout URL** (URL di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.
   
    c. Nella casella di testo **Change Password URL** (URL di modifica password) incollare l'**URL di modifica password** copiato dal portale di Azure.
       
    d. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PublicKey** (Chiave pubblica).
   
    e. In **Algorithm** (Algoritmo) selezionare **RSA**.
   
    f. Fare clic su **OK**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-zoho-test-user"></a>Creare un utente di test di Zoho

Per consentire agli utenti di Azure AD di accedere a Zoho Mail, è necessario eseguirne il provisioning in Zoho Mail. Nel caso di Zoho Mail, il provisioning è un'attività manuale.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zoho Mail per eseguire il provisioning degli account utente Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di **Zoho Mail** come amministratore.

2. Passare a **Control Panel \> Mail & Docs**.

3. Passare a **User Details \> Add User** (Dettagli utente > Aggiungi utente).
   
    ![Aggiungere un utente](./media/active-directory-saas-zoho-mail-tutorial/ic789611.png "Aggiungere un utente")

4. Nella finestra di dialogo **Add users** seguire questa procedura:
   
    ![Aggiungere un utente](./media/active-directory-saas-zoho-mail-tutorial/ic789612.png "Aggiungere un utente")
   
    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email ID** (ID posta elettronica) digitare l'ID di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    d. Nella casella di testo **Password** digitare la password dell'utente.
   
    e. Fare clic su **OK**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zoho.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Zoho, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Zoho**.

    ![Collegamento di Zoho nell'elenco delle applicazioni](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zoho nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zoho.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_203.png

