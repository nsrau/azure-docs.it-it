---
title: 'Esercitazione: Integrazione di Azure Active Directory con Proxyclick | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52d3e058c7e604a31493bf3b1972b39c9922fd90
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163516"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Esercitazione: Integrazione di Azure Active Directory con Proxyclick

Questa esercitazione descrive come integrare Proxyclick con Azure Active Directory (Azure AD).

L'integrazione di Proxyclick con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Proxyclick.
- È possibile abilitare gli utenti per l'accesso automatico a Proxyclick (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Proxyclick, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Proxyclick abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Proxyclick dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-proxyclick-from-the-gallery"></a>Aggiunta di Proxyclick dalla raccolta
Per configurare l'integrazione di Proxyclick in Azure AD, è necessario aggiungere Proxyclick dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Proxyclick dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Proxyclick**, selezionare **Proxyclick** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Proxyclick nell'elenco risultati](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Proxyclick in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Proxyclick che corrisponde a un utente di Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Proxyclick.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Proxyclick, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di Proxyclick](#create-a-proxyclick-test-user)**: per avere una controparte di Britta Simon in Proxyclick collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Proxyclick.

**Per configurare Single Sign-On di Azure AD con Proxyclick, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Proxyclick** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Nella sezione **URL e dominio Proxyclick**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://saml.proxyclick.com/init/<companyId>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://saml.proxyclick.com/consume/<companyId>`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/proxyclick-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Proxyclick** fare clic su **Configura Proxyclick** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. In un'altra finestra del browser Web accedere al sito aziendale di Proxyclick come amministratore.

1. Selezionare **Account e Impostazioni**.

    ![Configurazione di Proxyclick](./media/proxyclick-tutorial/configure1.png)

1. Scorrere in basso su **INTEGRAZIONI** e selezionare **SAML**.

    ![Configurazione di Proxyclick](./media/proxyclick-tutorial/configure2.png)

1. Nella sezione **SAML** seguire questa procedura:

    ![Configurazione di Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Copiare il valore dell'**URL SAML Consumer** e incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio Proxyclick** del portale di Azure.

    b. Copiare il valore **URL di reindirizzamento SAML SSO** e incollarlo nelle caselle di testo **URL di accesso** e **Identificatore** nella sezione **Dominio e URL di Proxyclick** nel portale di Azure.

    c. Selezionare **Metodo di richiesta SAML** come **Reindirizza HTTP**.

    d. Nella casella di testo **Autorità emittente** incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    e. Nella casella di testo **SAML 2.0 Endpoint URL** incollare il valore dell'**URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    f. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato**.

    g. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/proxyclick-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-proxyclick-test-user"></a>Creare un utente test Proxyclick

Per consentire agli utenti di Azure AD di accedere a Proxyclick, è necessario eseguirne il provisioning in Proxyclick. Nel caso di Proxyclick, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Proxyclick come amministratore.

1. Fare clic su**Colleghi**dalla barra di navigazione in alto.

    ![Aggiungere un dipendente](./media/proxyclick-tutorial/user1.png)

1. Fare clic su **Aggiungi collega**

    ![Aggiungere un dipendente](./media/proxyclick-tutorial/user2.png)

1. Nella sezione **Aggiungi un collega** seguire questa procedura:

    ![Aggiungere un dipendente](./media/proxyclick-tutorial/user3.png)

    a. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    b. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio Simon.

    d. Fare clic su **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Proxyclick.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Proxyclick, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Proxyclick**.

    ![Collegamento di Proxyclick nell'elenco delle applicazioni](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Proxyclick nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Proxyclick.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

