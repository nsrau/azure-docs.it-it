---
title: 'Esercitazione: Integrazione di Azure Active Directory con Druva| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: b23e73c47b9a00893e036b67826e4b7ead819a1d
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Esercitazione: Integrazione di Azure Active Directory con Druva

Questa esercitazione descrive come integrare Druva con Azure Active Directory (Azure AD).

L'integrazione di Druva con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Druva.
- È possibile abilitare gli utenti per l'accesso automatico a Druva (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Druva, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Druva abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Druva dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-druva-from-the-gallery"></a>Aggiunta di Druva dalla raccolta
Per configurare l'integrazione di Druva in Azure AD, è necessario aggiungere Druva dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Druva dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Druva**, selezionare **Druva** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Druva nell'elenco risultati](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Druva in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Druva che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Druva.

Per stabilire la relazione di collegamento, in Druva assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Druva, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Druva](#create-a-druva-test-user)**: per avere una controparte di Britta Simon in Druva collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Druva.

**Per configurare l'accesso Single Sign-On di Azure AD con Druva, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Druva** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. Nella sezione **URL e dominio Druva** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://cloud.druva.com/home`

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

5. L'applicazione Druva prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:

    | Nome attributo      | Valore attributo      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Immettere il valore generato del token |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga. In una fase successiva dell'esercitazione verrà illustrato il valore generato del token.
    
    d. Fare clic su **OK**.    

7. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di Druva** fare clic su **Configura Druva** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di Druva come amministratore.

10. Passare a **Manage \> Settings**.

    ![Impostazioni](./media/active-directory-saas-druva-tutorial/ic795091.png "Impostazioni")

11. Nella finestra di dialogo Single Sign-On Settings seguire questa procedura:

    ![Impostazioni di Single Sign-On](./media/active-directory-saas-druva-tutorial/ic795092.png "Impostazioni di Single Sign-On")
    
    a. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **ID Provider Login URL** (URL accesso provider di identità).
    
    b. Incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **URL di disconnessione provider di identità**.
    
     c. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **ID Provider Certificate**
     
     d. Per aprire la pagina **Settings** fare clic su **Save**.

12. Nella pagina **Settings** fare clic su **Generate SSO Token**.

    ![Impostazioni](./media/active-directory-saas-druva-tutorial/ic795093.png "Impostazioni")

13. Nella finestra di dialogo **Single Sign-on Authentication Token** eseguire la procedura seguente:

    ![Token SSO](./media/active-directory-saas-druva-tutorial/ic795094.png "Token SSO")
    
    a. Fare clic su **Copia**, incollare il valore copiato nella casella di testo **Valore** nella sezione **Aggiungi attributo**.
    
    b. Fare clic su **Close**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-druva-test-user"></a>Creare un utente test Druva

Per consentire agli utenti di Azure AD di accedere a Druva, è necessario eseguirne il provisioning in Druva. Nel caso di Druva, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Druva** come amministratore.

2. Passare a **Manage (Gestisci) \> Users (Utenti)**.
   
   ![Gestione utenti](./media/active-directory-saas-druva-tutorial/ic795097.png "Gestione utenti")

3. Fare clic su **Create New**.
   
   ![Gestione utenti](./media/active-directory-saas-druva-tutorial/ic795098.png "Gestione utenti")

4. Nella finestra di dialogo Create New User seguire questa procedura:
   
   ![Crea nuovo utente](./media/active-directory-saas-druva-tutorial/ic795099.png "Crea nuovo utente")
   
   a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.
   
   b. Nella casella di testo **Name** (Nome) immettere il nome dell'utente, ad esempio **BrittaSimon**.
   
   c. Fare clic su **Create User**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Druva per eseguire il provisioning degli account utente di Azure Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Druva.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Druva, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Druva**.

    ![Collegamento Druva nell'elenco Applicazioni](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Druva nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Druva.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png


