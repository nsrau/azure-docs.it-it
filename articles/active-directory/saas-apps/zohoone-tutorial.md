---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho One | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f17a297d3099d51b3a58a6654560a67f9a4192a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208317"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho One

Questa esercitazione descrive come integrare Zoho One con Azure Active Directory (Azure AD).

L'integrazione di Zoho One con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zoho One.
- È possibile abilitare gli utenti per l'accesso automatico a Zoho One (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zoho One, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Zoho One abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Zoho One dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zoho-one-from-the-gallery"></a>Aggiunta di Zoho One dalla raccolta
Per configurare l'integrazione di Zoho One in Azure AD, è necessario aggiungere Zoho One dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zoho One dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Zoho One**, selezionare **Zoho One** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zoho One nell'elenco risultati](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zoho One usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Zoho One corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zoho One.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zoho One, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Zoho One](#create-a-zoho-one-test-user)**: per avere una controparte di Britta Simon in Zoho One collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zoho One.

**Per configurare l'accesso Single Sign-On di Azure AD con Zoho One, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zoho One** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Nella sezione **URL e dominio Zoho One** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zoho One](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `one.zoho.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Selezionare **Mostra impostazioni URL avanzate**

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL:`https://one.zoho.com`

1. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > I precedenti valori **URL di risposta** e **URL di accesso** non sono reali. È necessario aggiornare tale valore con i valori reali di URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione. 

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di Zoho One** fare clic su **Configura Zoho One** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione Zoho One](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di Zoho One come amministratore.

1. Nella scheda **Organizzazione**, fare clic su **Installazione** nella sezione **Autenticazione SAML**.

    ![Organizzazione Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. Nella pagina popup eseguire i passaggi seguenti:

    ![Accesso Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Nella casella di testo **URL di accesso** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    b. Nella casella di testo **URL di disconnessione** incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Sfoglia** per caricare il **Certificato (Base64)** scaricato dal portale di Azure.

    d. Fare clic su **Save**.

1. Dopo aver salvato le impostazioni di autenticazione SAML, copiare il valore dell'**Identificatore SAML** e usarlo nell'**URL di risposta** nel portale di Azure, nella sezione **Dominio e URL Zoho One** .

    ![SAML Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Passare alla scheda **Domini** e quindi fare clic su **Aggiungi dominio**.

    ![Dominio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Nella pagina **Aggiungi dominio** seguire la procedura seguente:

    ![Aggiungi dominio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Nella casella di testo **Nome di dominio**, inserire il dominio, ad esempio **contoso.com**.

    b. Fare clic su **Aggiungi**.

    >[!Note]
    >Dopo aver aggiunto il dominio seguire [questi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passaggi per verificarlo. Una volta che il dominio è verficato, usare il nome di dominio in **URL di accesso** nella sezione **Dominio e URL di Zoho One** nel portale di Azure.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/zohoone-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/zohoone-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/zohoone-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-zoho-one-test-user"></a>Creare un utente di test di Zoho One

Per consentire agli utenti di Azure AD di accedere a Zoho One, è necessario eseguire il provisioning degli utenti in Zoho One. Nel caso di Zoho One il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Zoho One come amministratore della sicurezza.

1. Nella scheda **Utenti**, fare clic su **logo utente**.

    ![Utente Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Nella pagina **Aggiungi utente** eseguire la procedura seguente:

    ![Aggiungi Utente Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Nella casella di testo **Nome** digitare il nome dell'utente, ad esempio **Britta Simon**.
    
    b. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    >[!Note]
    >Selezionare il dominio verificato dall'elenco di domini.

    c. Fare clic su **Aggiungi**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zoho One.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Zoho One, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Zoho One**.

    ![Collegamento di Zoho One nell'elenco delle applicazioni](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zoho One nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zoho One.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

