---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rollbar | Documenti Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: bb8a81327163513ab721d2ad72da19173b59bc1f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Esercitazione: Integrazione di Azure Active Directory con Rollbar

In questa esercitazione informazioni su come integrare Rollbar con Azure Active Directory (Azure AD).

Integrazione Rollbar con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso al Rollbar.
- È possibile abilitare gli utenti per automaticamente ottenere firmato Rollbar (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Rollbar, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Un Rollbar single sign-on abilitato sottoscrizione

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Rollbar dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-rollbar-from-the-gallery"></a>Aggiunta di Rollbar dalla raccolta
Per configurare l'integrazione di Rollbar in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite Rollbar dalla raccolta.

**Per aggiungere Rollbar dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Rollbar**selezionare **Rollbar** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Rollbar nell'elenco dei risultati](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con Rollbar in base a un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in Rollbar a un utente in Azure AD. In altre parole, una relazione di collegamento tra un utente AD Azure e l'utente correlato in Rollbar deve essere stabilito.

In Rollbar, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure AD single sign-on con Rollbar, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test Rollbar](#create-a-rollbar-test-user)**  - disporre di un equivalente di Britta Simon in Rollbar collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione Rollbar.

**Per configurare Azure AD single sign-on con Rollbar, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **Rollbar** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. Nel **Rollbar dominio e gli URL** sezione, eseguire la procedura seguente se si desidera configurare l'applicazione in **IDP** modalità iniziata da:

    ![Rollbar informazioni domini e gli URL single sign-on](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://saml.rollbar.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://rollbar.com/<accountname>/saml/sso/azure/`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Rollbar informazioni domini e gli URL single sign-on](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Contatto [team di supporto Rollbar Client](mailto:support@rollbar.com) per ottenere questi valori. 

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. In una finestra del web browser, accedere al sito della società Rollbar come amministratore.

8. Fare clic su di **le impostazioni del profilo** nell'angolo superiore destro e quindi fare clic su **le impostazioni del nome di Account**.
    
    ![Configurazione](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Fare clic su **Provider di identità** in sicurezza.

    ![Configurazione](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. Nel **Provider di identità SAML** sezione, eseguire la procedura seguente:
    
    ![Configurazione](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Selezionare **AZURE** dal **Provider di identità SAML** elenco a discesa.

    b. Aprire il file di metadati nel blocco note, copiarne il contenuto negli Appunti e quindi incollarlo il **SAML Metadata** casella di testo.

    c. Fare clic su **Save**.

11. Dopo aver scelto di salvataggio pulsante, la schermata sarà simile al seguente. In questa sezione, eseguire la procedura seguente:
    
    ![Configurazione](./media/active-directory-saas-rollbar-tutorial/configure3.png)

    a. Selezionare **richiedono account di accesso tramite il Provider di identità SAML** casella di controllo.

    b. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-rollbar-test-user"></a>Creare un utente test Rollbar

Per consentire agli utenti di Azure AD di accedere a Rollbar, è necessario eseguirne il provisioning in Rollbar. Nel caso di Rollbar, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito della società Rollbar come amministratore.

2. Fare clic su di **le impostazioni del profilo** nell'angolo superiore destro e quindi fare clic su **le impostazioni del nome di Account**.

    ![Utente](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Fare clic su **Users**.
    
    ![Aggiungere un dipendente](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Fare clic su **invitare i membri del Team**.

    ![Invita persone](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. Nella casella di testo, immettere il nome dell'utente come  **brittasimon@contoso.com**  e fare clic su **Add/invito**.

    ![Invita persone](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. Utente riceve un invito e dopo aver accettato si potrà create nel sistema.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso a Rollbar.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon Rollbar, procedere come segue:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Rollbar**.

    ![Il collegamento Rollbar nell'elenco delle applicazioni](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Rollbar nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione Rollbar.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

