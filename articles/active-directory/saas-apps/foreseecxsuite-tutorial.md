---
title: 'Esercitazione: Integrazione di Azure Active Directory con ForeSee CX Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ForeSee CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2364152cc897ac02c3fcdff68423c64737bb1a78
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187667"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Esercitazione: Integrazione di Azure Active Directory con ForeSee CX Suite

Questa esercitazione descrive come integrare ForeSee CX Suite con Azure Active Directory (Azure AD).

L'integrazione di ForeSee CX Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ForeSee CX Suite.
- È possibile abilitare gli utenti per l'accesso automatico a ForeSee CX Suite (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ForeSee CX Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione a ForeSee CX Suite abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ForeSee CX Suite dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Aggiunta di ForeSee CX Suite dalla raccolta
Per configurare l'integrazione di ForeSee CX Suite in Azure AD, è necessario aggiungere ForeSee CX Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ForeSee CX Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **ForeSee CX Suite**, selezionare **ForeSee CX Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ForeSee CX Suite nell'elenco risultati](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ForeSee CX Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di ForeSee CX Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ForeSee CX Suite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ForeSee CX Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di ForeSee CX Suite](#create-a-foresee-cx-suite-test-user)**: per avere una controparte di Britta Simon in ForeSee CX Suite collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ForeSee CX Suite.

**Per configurare Single Sign-On di Azure AD con ForeSee CX Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ForeSee CX Suite** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. Nella sezione **gruppo di dominio e gli URL di ForeSee CX**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di ForeSee CX Suite](./media/foreseecxsuite-tutorial/upload.png)

    a. Fare clic su **Carica il file di metadati**.

    ![Informazioni su URL e dominio per Single Sign-On di ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Dopo aver completato il caricamento del **file di metadati del provider di servizi**, il valore dell'**identificatore** popola automaticamente la casella di testo della sezione **gruppo di dominio e gli URL di ForeSee CX Suite**, come illustrato di seguito:

    ![Informazioni su URL e dominio per Single Sign-On di ForeSee CX Suite](./media/foreseecxsuite-tutorial/urlupload.png)

1. Se non si dispone del **file di metadati del provider di servizi**, seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. Nella casella di testo **URL accesso** digitare l'URL: `https://cxsuite.foresee.com/`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. è necessario aggiornare questo valore con l'ID effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di ForeSee CX Suite](mailto:support@foresee.com).

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Per configurare l'accesso Single Sign-On sul lato **ForeSee CX Suite** è necessario inviare il file **XML di metadati** scaricato [al team di supporto di ForeSee CX Suite](mailto:support@foresee.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-foresee-cx-suite-test-user"></a>Creare un utente di test con ForeSee CX Suite

In questa sezione viene creato un utente chiamato Britta Simon in ForeSee CX Suite. Rivolgersi al [team di supporto di ForeSee CX Suite](mailto:support@foresee.com) per aggiungere gli utenti o il dominio necessari per essere inclusi nella piattaforma ForeSee CX Suite. Se il dominio viene aggiunto dal team, verrà automaticamente eseguito il provisioning degli utenti per la piattaforma ForeSee CX Suite. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ForeSee CX Suite.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a ForeSee CX Suite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

1. Nell'elenco di applicazioni selezionare **ForeSee CX Suite**.

    ![Collegamento ForeSee CX Suite nell'elenco Applicazioni](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ForeSee CX Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ForeSee CX Suite.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

