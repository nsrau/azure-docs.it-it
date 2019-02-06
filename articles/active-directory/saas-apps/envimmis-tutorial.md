---
title: 'Esercitazione: Integrazione di Azure Active Directory con Envi MMIS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: b85dc27f6b6a23be6dc89a0f0a7cf9f78681446d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197450"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Esercitazione: Integrazione di Azure Active Directory con Envi MMIS

Questa esercitazione descrive come integrare Envi MMIS con Azure Active Directory (Azure AD).

L'integrazione di Envi MMIS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Envi MMIS.
- È possibile abilitare gli utenti per l'accesso automatico a Envi MMIS (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Envi MMIS, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Envi MMIS abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Envi MMIS dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-envi-mmis-from-the-gallery"></a>Aggiunta di Envi MMIS dalla raccolta
Per configurare l'integrazione di Envi MMIS in Azure AD, è necessario aggiungere Envi MMIS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Envi MMIS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Envi MMIS**, selezionare **Envi MMIS** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Envi MMIS nell'elenco risultati](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Envi MMIS usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Envi MMIS che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Envi MMIS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Envi MMIS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di Envi MMIS](#create-an-envi-mmis-test-user)**: per avere una controparte di Britta Simon in Envi MMIS collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Envi MMIS.

**Per configurare Single Sign-On di Azure AD con Envi MMIS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Envi MMIS** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

1. Nella sezione **URL e dominio Envi MMIS** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Envi MMIS](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Envi MMIS](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Envi MMIS](mailto:support@ioscorp.com).

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/tutorial_general_400.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di Envi MMIS come amministratore.

1. Fare clic sulla scheda **My Domain** (Dominio personale).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure1.png)

1. Fare clic su **Modifica**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure2.png)

1. Selezionare la casella di controllo **Use remote authentication** (Usa autenticazione remota) e quindi selezionare **HTTP Redirect** (Reindirizzamento HTTP) dall'elenco a discesa **Authentication Type** (Tipo di autenticazione).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure3.png)

1. Selezionare la scheda **Resources** (Risorse) e quindi fare clic su **Upload Metadata** (Carica metadati).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure4.png)

1. Nel popup **Upload Metadata** (Carica metadati) seguire questa procedura:

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure5.png)

    a. Selezionare l'opzione **File** dall'elenco a discesa **Upload From** (Carica da).

    b. Caricare il file di metadati scaricato dal portale di Azure selezionando l'**icona di selezione file**.

    c. Fare clic su **OK**.

1. Dopo avere caricato il file di metadati scaricato, i campi verranno popolati automaticamente. Fare clic su **Update** (Aggiorna).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/envimmis-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/envimmis-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/envimmis-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/envimmis-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-an-envi-mmis-test-user"></a>Creare un utente test di Envi MMIS

Per consentire agli utenti di Azure AD di accedere a Envi MMIS, è necessario eseguire il provisioning degli utenti in Envi MMIS.  
Nel caso di Envi MMIS, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Envi MMIS come amministratore.

1. Fare clic sulla scheda **User Access** (Accesso utente).

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user1.png)

1. Fare clic sul pulsante **Add User** (Aggiungi utente).

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user2.png)

1. Nella sezione **Add User** seguire questa procedura:

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user3.png)

    a. Digitare il nome utente dell'account Britta Simon nella casella di testo **User Name** (Nome utente) come **brittasimon@contoso.com**.
    
    b. Nella casella di testo **First Name** (Nome) digitare il nome di BrittaSimon come **Britta**.

    c. Nella casella di testo **Last Name** (Cognome) digitare il cognome di BrittaSimon come **Simon**.

    d. Immettere la posizione dell'utente nella sezione **Title** (Titolo) della casella di testo.
    
    e. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon come **brittasimon@contoso.com**.

    f. Nella casella di testo **SSO User Name** (Nome utente SSO) digitare il nome utente dell'account Britta Simon come **brittasimon@contoso.com**.

    g. Fare clic su **Save**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Envi MMIS.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Envi MMIS, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Envi MMIS**.

    ![Collegamento di Envi MMIS nell'elenco delle applicazioni](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Envi MMIS nel pannello di accesso, si accederà automaticamente all'applicazione Envi MMIS.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png

