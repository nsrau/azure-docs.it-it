---
title: 'Esercitazione: Integrazione di Azure Active Directory con Vidyard | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 683cdc43c8d671c14142d0770cd2594ad862b7ac
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196243"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Esercitazione: Integrazione di Azure Active Directory con Vidyard

Questa esercitazione descrive come integrare Vidyard con Azure Active Directory (Azure AD).

L'integrazione di Vidyard con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Vidyard.
- È possibile abilitare gli utenti per l'accesso automatico a Vidyard (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Vidyard, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Vidyard abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Vidyard dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-vidyard-from-the-gallery"></a>Aggiunta di Vidyard dalla raccolta
Per configurare l'integrazione di Vidyard in Azure AD, è necessario aggiungere Vidyard dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Vidyard dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Vidyard**, selezionare **Vidyard** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Vidyard nell'elenco risultati](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Vidyard usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Vidyard che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Vidyard.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Vidyard, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Vidyard](#create-a-vidyard-test-user)**: per avere una controparte di Britta Simon in Vidyard collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Vidyard.

**Per configurare l'accesso Single Sign-On di Azure AD con Vidyard, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Vidyard** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Nella sezione **URL e dominio Vidyard** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/vidyard-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Vidyard** fare clic su **Configura Vidyard** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di Vidyard come amministratore.

1. Nel dashboard di Vidyard selezionare **Group** (Gruppo) > **Security** (Sicurezza)

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure1.png)

1. Fare clic su **New Profile** (Nuovo Profilo).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure2.png)

1. Nella sezione **SAML Configuration** (Configurazione SAML) seguire questa procedura:

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Immettere il nome del profilo generale nella casella di testo **Profile Name** (Nome profilo).

    b. Copiare il valore di **SSO User Login Page** (Pagina di accesso utente) e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio Vidyard** del portale di Azure.

    c. Copiare il valore di **ACS URL** (URL ACS) e incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio Vidyard** del portale di Azure.

    d. Copiare il valore di **Issuer/Metadata URL** (URL dei metadati/autorità di certificazione) e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio Vidyard** del portale di Azure.

    e. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).

    f. Nella casella di testo **SAML Endpoint URL** (URL endpoint SAML) incollare il valore di **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    g. Fare clic su **Conferma**.

1. Nella scheda Single Sign-On selezionare **Assign** (Assegna) accanto a un profilo esistente

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Dopo aver creato un profilo SSO, è possibile assegnarlo a qualsiasi gruppo per i quali gli utenti richiederanno l'accesso tramite Azure. Se l'utente non esiste all'interno del gruppo a cui sono stati assegnati, Vidyard creerà automaticamente un account utente e assegnerà loro un ruolo in tempo reale.

1. Selezionare il gruppo dell'organizzazione, visibile in **Groups Available to Assign** (Gruppi disponibili per l'assegnazione).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure5.png)

1. È possibile visualizzare i gruppi assegnati in **Groups Currently Assigned** (Gruppi attualmente assegnati). Selezionare un ruolo per il gruppo in base alla propria organizzazione e fare clic su **Confirm** (Conferma).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Per altre informazioni, vedere [questo documento](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/vidyard-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/vidyard-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/vidyard-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-vidyard-test-user"></a>Creare un utente di test con Vidyard

Questa sezione descrive come creare un utente chiamato Britta Simon in Vidyard. Vidyard supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a Vidyard viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Per creare un utente manualmente, contattare il  [team di supporto di Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Vidyard.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Vidyard, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Vidyard**.

    ![Collegamento di Vidyard nell'elenco delle applicazioni](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Vidyard nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Vidyard.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

