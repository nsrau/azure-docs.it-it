---
title: 'Esercitazione: Integrazione di Azure Active Directory con Veracode | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: d49349c5ae08e67d91e30967f3644623211823ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Esercitazione: Integrazione di Azure Active Directory con Veracode

Questa esercitazione descrive come integrare Veracode con Azure Active Directory (Azure AD).

L'integrazione di Veracode con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Veracode.
- È possibile abilitare gli utenti per l'accesso automatico a Veracode (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Veracode, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Veracode abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere Veracode dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-veracode-from-the-gallery"></a>Aggiungere Veracode dalla raccolta
Per configurare l'integrazione di Veracode in Azure AD, è necessario aggiungere Veracode dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Veracode dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Veracode**, selezionare **Veracode** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Veracode nell'elenco risultati](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Veracode usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Veracode corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Veracode.

Per stabilire la relazione di collegamento, in Veracode assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Veracode, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Veracode](#create-a-veracode-test-user)**: per avere una controparte di Britta Simon in Veracode collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Veracode.

**Per configurare l'accesso Single Sign-On di Azure AD con Veracode, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Veracode** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. Nella sezione **URL e dominio Veracode** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Veracode tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

    L'applicazione Veracode prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Attributi](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "Attributi")

6. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    | Nome attributo | Valore attributo |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
    
    ![Attributi](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "Attributi")
    
    ![Attributi](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "Attributi")
    
    b. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    
    c. Nella casella di testo **Valore attributo** , selezionare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di Veracode** fare clic su **Configura Veracode** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido**.

    ![Configurazione di Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di Veracode come amministratore.

10. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi su **Admin**.
   
    ![Amministrazione](./media/active-directory-saas-veracode-tutorial/ic802911.png "Amministrazione")

11. Fare clic sulla scheda **SAML** .

12. Nella sezione **Impostazioni SAML dell’organizzazione** seguire questa procedura:
   
    ![Amministrazione](./media/active-directory-saas-veracode-tutorial/ic802912.png "Amministrazione")
   
    a.  Nella casella di testo **Issuer** (Autorità emittente) incollare il valore dell'**ID entità SAML** copiato dal portale di Azure.
    
    b. Per caricare il certificato scaricato dal portale di Azure, fare clic su **Choose File** (Scegli file).
   
    c. Selezionare **Abilita la registrazione automatica**.

13. Nella sezione **Self Registration Settings** (Impostazioni di registrazione automatica) seguire questa procedura e quindi fare clic su **Save** (Salva):
   
    ![Amministrazione](./media/active-directory-saas-veracode-tutorial/ic802913.png "Amministrazione")
   
    a. In **New User Activation** (Attivazione nuovo utente) selezionare **No Activation Required** (Nessuna attivazione richiesta).
   
    b. In **User Data Updates** (Aggiornamenti dati utenti) selezionare **Preference Veracode User Data** (Dati utenti Veracode di preferenza).
   
    c. In **Dettagli sull’attributo SAML**selezionare le opzioni seguenti:
      * **User Roles**
      * **Policy Administrator**
      * **Reviewer**
      * **Security Lead**
      * **Executive**
      * **Submitter**
      * **Creator**
      * **All Scan Types**
      * **Team Memberships**
      * **Default Team**

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-veracode-test-user"></a>Creare un utente di test di Veracode
Per consentire agli utenti di Azure AD di accedere a Veracode, è necessario eseguirne il provisioning in Veracode. Nel caso di Veracode, il provisioning è un'attività automatica. Non è necessario eseguire alcuna operazione. Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente di Veracode per effettuare il provisioning degli account utente di Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Veracode.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Veracode, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Veracode**.

    ![Collegamento di Veracode nell'elenco delle applicazioni](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Veracode nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Veracode.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

