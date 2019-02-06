---
title: 'Esercitazione: Integrazione di Azure Active Directory con OrgChart Now | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 65f11b5a65adf86b4115b54b49b10c57ebf21a98
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154117"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Esercitazione: Integrazione di Azure Active Directory con OrgChart Now

Questa esercitazione descrive come integrare OrgChart Now con Azure Active Directory (Azure AD).

L'integrazione di OrgChart Now con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OrgChart Now.
- È possibile abilitare gli utenti per l'accesso automatico a OrgChart Now (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OrgChart Now, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di OrgChart Now abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di OrgChart Now dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-orgchart-now-from-the-gallery"></a>Aggiunta di OrgChart Now dalla raccolta
Per configurare l'integrazione di OrgChart Now in Azure AD, è necessario aggiungere OrgChart Now dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OrgChart Now dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **OrgChart Now**, selezionare **OrgChart Now** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![OrgChart Now nell'elenco risultati](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OrgChart Now in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di OrgChart Now che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OrgChart Now.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OrgChart Now, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di OrgChart Now](#create-an-orgchart-now-test-user)**: per avere una controparte di Britta Simon in OrgChart Now collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OrgChart Now.

**Per configurare Single Sign-On di Azure AD con OrgChart Now, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OrgChart Now** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Nella sezione **URL e dominio OrgChart Now** per configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    Nella casella di testo **Identificatore** digitare un URL: `https://sso2.orgchartnow.com`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` è l'ID entità SAML copiato dalla sezione Riferimento rapido, descritta più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di OrgChart Now** fare clic su **Configura OrgChart Now** per aprire la finestra **Configura accesso**. Copiare l'**ID entità SAML** dalla **sezione Riferimento rapido** e usarlo per completare **URL di accesso** nella sezione **URL e dominio OrgChart Now**.

    ![Configurazione di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Per configurare l'accesso Single Sign-On sul lato **OrgChart Now**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di OrgChart Now](mailto:ocnsupport@officeworksoftware.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-an-orgchart-now-test-user"></a>Creare un nuovo utente di test di OrgChart Now

Per consentire agli utenti di Azure AD di accedere a OrgChart Now, è necessario effettuarne il provisioning in OrgChart Now. 

1. OrgChart Now supporta il provisioning JIT, che è abilitato per impostazione predefinita. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a OrgChart Now. La funzionalità di provisioning dell'utente JIT crea solo un utente **di sola lettura** quando una richiesta SSO proviene da un IDP riconosciuto e non viene trovato l'indirizzo di posta elettronica nell'asserzione SAML. Per questa funzionalità di provisioning automatico, è necessario creare un gruppo di accesso denominato **General** in OrgChart Now. Seguire questa procedura per creare un gruppo di accesso:

    a. Andare all'opzione **Manage Groups** (Gestisci gruppi) dopo avere fatto clic sull'icona a forma di **ingranaggio** nell'angolo in alto a destra dell'interfaccia utente.

    ![Gruppi di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selezionare l'icona **Add** (Aggiungi) e assegnare al gruppo il nome **General**, quindi fare clic su **OK**. 

    ![Aggiunta in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selezionare le cartelle a cui si vuole che abbiano accesso gli utenti generali o di sola lettura:

    ![Cartelle di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloccare** le cartelle in modo che solo gli utenti amministratori possano modificarle e quindi fare clic su **OK**.

    ![Blocco in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Per creare utenti **amministratori** e utenti **in lettura/scrittura**, è necessario creare manualmente un utente per ottenere l'accesso al livello di privilegio tramite SSO. Per eseguire il provisioning di un account utente, seguire questa procedura:

    a. Accedere a OrgChart Now come amministratore della sicurezza.

    b.  Fare clic su **Settings** (Impostazioni) nell'angolo in alto a destra e quindi passare a **Manage Users** (Gestisci utenti).

    ![Impostazioni di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Fare clic su **Add** (Aggiungi) e seguire questa procedura:

    ![Gestione in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Nella casella di testo **User ID** (ID utente) immettere l'ID utente, ad esempio **brittasimon@contoso.com**.

    * Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    * Fare clic su **Aggiungi**.
    
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OrgChart Now.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a OrgChart Now, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **OrgChart Now**.

    ![Collegamento di OrgChart Now nell'elenco delle applicazioni](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OrgChart Now nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OrgChart Now.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

