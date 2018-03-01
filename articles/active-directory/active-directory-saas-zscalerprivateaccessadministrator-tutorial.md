---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access Administrator | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Private Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: bf0b7cbd8047dfdbc1a4503775e6d36f8e8a67c1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access Administrator

Questa esercitazione descrive come integrare Zscaler Private Access Administrator con Azure Active Directory (Azure AD).

L'integrazione di Zscaler Private Access Administrator con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zscaler Private Access Administrator.
- È possibile abilitare gli utenti per l'accesso automatico a Zscaler Private Access Administrator (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler Private Access Administrator sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione attiva con accesso Single Sign-on per Zscaler Private Access Administrator

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Zscaler Private Access Administrator dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Aggiunta di Zscaler Private Access Administrator dalla raccolta
Per configurare l'integrazione di Zscaler Private Access Administrator in Azure AD è necessario aggiungere Zscaler Private Access Administrator dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Zscaler Private Access Administrator dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Zscaler Private Access Administrator**, selezionare **Zscaler Private Access Administrator** dal pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Private Access Administrator nell'elenco dei risultati](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Zscaler Private Access Administrator che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Private Access Administrator.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Zscaler Private Access Administrator](#create-a-zscaler-private-access-administrator-test-user)**: per avere una controparte di Britta Simon in Zscaler Private Access Administrator collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zscaler Private Access Administrator.

**Per configurare l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zscaler Private Access Administrator** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Nella sezione **URL e dominio Zscaler Private Access Administrator**, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per domini e URL Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Selezionare **Mostra impostazioni URL avanzate**

    d. Nella casella di testo **RelayState** digitare un valore: `idpadminsso`

4.  Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contattare il [team di supporto di Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) per ottenere questi valori.
 
5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. In un'altra finestra del Web browser accedere a Zscaler Private Access Administrator come amministratore.

8. Nella parte superiore, fare clic su **Amministrazione**, passare alla sezione **AUTENTICAZIONE**, quindi fare clic su **Configurazione IdP**.

    ![Zscaler Private Access Administrator admin](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. Nell'angolo superiore destro, fare clic su **Aggiungi configurazione IdP**. 

    ![Zscaler Private Access Administrator addidp](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Nella pagina **Aggiungi configurazione IdP**, eseguire la procedura seguente:
 
    ![Zscaler Private Access Administrator idpselect](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Fare clic su **Seleziona file** per caricare il file di metadati scaricati da Azure AD nel campo **Caricamento file di metadati IdP**.

    b. Vengono letti i **metadati IdP** da Azure AD e vengono compilate tutte le informazioni dei campi, come mostrato di seguito.

    ![Zscaler Private Access Administrator idpconfig](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selezionare **Single Sign-On** come **Amministratore**.

    d. Selezionare il dominio dal campo **Domini**.
    
    e. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Creare un utente di test di Zscaler Private Access Administrator

Per poter accedere a Zscaler Private Access Administrator, gli utenti di Azure AD devono eseguirvi il provisioning. Nel caso di Zscaler Private Access Administrator, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito della società Zscaler Private Access Administrator come amministratore.

2. Nella parte superiore, fare clic su **Amministrazione**, passare alla sezione **AUTENTICAZIONE**, quindi fare clic su **Configurazione IdP**.

    ![Zscaler Private Access Administrator admin](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Fare clic su **Amministratori** sul lato sinistro del menu.

    ![Amministratore di Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Nell'angolo superiore destro, fare clic su **Aggiungi amministratore**:

    ![Aggiunta di un amministratore in Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Nella pagina **Aggiungi amministratore** eseguire la procedura seguente:

    ![Amministratore utente in Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Nella casella di testo **Nome utente**, immettere l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

    b. Nella casella di testo **Password**, digitare la password.

    c. Nella casella di testo **Conferma password**, digitare di nuovo la password.

    d. Per **Ruolo**, selezionare **Zscaler Private Access Administrator**.

    e. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

    f. Nella casella di testo **Telefono**, digitare il numero di telefono.

    g. Nella casella di testo **Fuso orario**, selezionare il fuso orario.

    h. Fare clic su **Save**.  

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Zscaler Private Access Administrator.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Zscaler Private Access Administrator, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access Administrator**.

    ![Collegamento a Zscaler Private Access Administrator nell'elenco delle applicazioni](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zscaler Private Access Administrator nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler Private Access Administrator.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

