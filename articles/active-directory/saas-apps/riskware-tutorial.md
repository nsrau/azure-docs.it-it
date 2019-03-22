---
title: 'Esercitazione: Integrazione di Azure Active Directory con Riskware | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842533"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Esercitazione: Integrazione di Azure Active Directory con Riskware

Questa esercitazione descrive come integrare Riskware con Azure Active Directory (Azure AD).

L'integrazione di Riskware con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Riskware.
- È possibile abilitare gli utenti per l'accesso automatico a Riskware (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Riskware, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata Riskware per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Riskware dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-riskware-from-the-gallery"></a>Aggiunta di Riskware dalla raccolta
Per configurare l'integrazione di Riskware in Azure AD, è necessario aggiungere Riskware dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Riskware dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Riskware**, selezionare **Riskware** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Riskware nell'elenco risultati](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Riskware in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Riskware che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Riskware.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Riskware, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Riskware](#create-a-riskware-test-user)**: per avere una controparte di Britta Simon in Riskware collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Riskware.

**Per configurare Single Sign-On di Azure AD con Riskware, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Riskware** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. Nella sezione **URL e dominio Riskware** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Riskware](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente:

    | Environment| Modello URL|
    |--|--|
    | Test di accettazione utente|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL:
    
    | Environment| Modello URL|
    |--|--|
    | Test di accettazione utente| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Il valore dell'URL di accesso non è reale. è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto client di Riskware](mailto:support@pansoftware.com.au).

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/riskware-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Riskware** fare clic su **Configura Riskware** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di Riskware come amministratore.

1. In alto a destra, fare clic su **Manutenzione** per aprire la pagina di manutenzione.

    ![Manutenzione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Nella pagina di manutenzione, fare clic su **Autenticazione**.

    ![Autenticazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Nella pagina **Configurazione di autenticazione** seguire questa procedura:

    ![Configurazione di autenticazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selezionare **Tipo** come **SAML** per l'autenticazione.

    b. Nella casella di testo **Codice** digitare il codice come AZURE_UAT.

    c. Nella casella di testo **Descrizione**, digitare una descrizione, ad esempio Configurazione AZURE per SSO.

    d. Nella casella di testo **Pagina Single Sign-On** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Nella casella di testo **Pagina di disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    f. Nella casella di testo **Campo modulo Post** digitare il nome del campo presente nella risposta Post contenente SAML come SAMLResponse.

    g. Nella casella di testo **nome del tag identità XML** scrivere l'attributo che contiene l'identificatore univoco nella risposta SAML come NameID.

    h. Aprire nel Blocco note il  **metadati Xml**  scaricato dal portale di Azure, copiarne il certificato dal file di metadati e quindi incollarlo nella casella di testo **Certificato**

    i. Nella casella di testo **URL consumer**, incollare il valore di **URL di risposta**, che si ottiene dal team di supporto.

    j. Nella casella di testo **Autorità di certificazione**, incollare il valore di **Identificatore**, che si ottiene dal team di supporto.

    > [!Note]
    > Per ottenere tali valori contattare il [team di supporto client di Riskware](mailto:support@pansoftware.com.au)

    k. Selezionare la casella di controllo **Usa POST**.

    l. Selezionare la casella di controllo **Usa richiesta SAML**.

    m. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/riskware-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/riskware-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/riskware-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-riskware-test-user"></a>Creare un utente test Riskware

Per consentire agli utenti di Azure AD di accedere a Riskware, è necessario effettuarne il provisioning in Riskware. Nel caso di Riskware il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Riskware come amministratore della sicurezza.

1. In alto a destra, fare clic su **Manutenzione** per aprire la pagina di manutenzione. 

    ![Manutenzione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Nella pagina di manutenzione, fare clic su **Persone**.

    ![Persone di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selezionare la scheda **Dettagli** ed eseguire questa procedura:

    ![Dettagli di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selezionare **Tipo di persona**, ad esempio, dipendente.

    b. Nella casella di testo **Nome** immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Cognome** immettere il cognome dell'utente, ad esempio **Simon**.

1. Nella scheda **Sicurezza** eseguire la procedura seguente:

    ![Protezione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Nella sezione **Autenticazione**, selezionare la modalità di **Autenticazione**, che è stata predisposta come Configurazione di AZURE per SSO.

    b. Sotto **dettagli di accesso** nella sezione la **ID utente** casella di testo immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    c. Nella casella di testo **Password** digitare la password dell'utente.

1. Nella scheda **Organizzazione** eseguire questa procedura:

    ![Organizzazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selezionare l'opzione come organizzazione **Level1**.

    b. Nella sezione**Area di lavoro primaria della persona** nella casella di testo **Percorso** digitare il percorso.

    c. Nella sezione **Dipendente**, impostare **Stato dipendente** come generico.

1. Fare clic su **Save**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Riskware.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Riskware, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco applicazioni, selezionare **Riskware**.

    ![Collegamento di Riskware nell'elenco delle applicazioni](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Riskware nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Riskware.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

