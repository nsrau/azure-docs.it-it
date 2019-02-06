---
title: 'Esercitazione: Integrazione di Azure Active Directory con moconavi | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 3009cb42ac477b18d45ab5968d6f5793ce1cd36c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165898"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Esercitazione: Integrazione di Azure Active Directory con moconavi

Questa esercitazione descrive come integrare moconavi con Azure Active Directory (Azure AD).

L'integrazione di moconavi con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a moconavi.
- È possibile abilitare gli utenti per l'accesso automatico a moconavi (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con moconavi, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione moconavi abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di moconavi dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-moconavi-from-the-gallery"></a>Aggiunta di moconavi dalla raccolta
Per configurare l'integrazione di moconavi in Azure AD, è necessario aggiungere moconavi dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere moconavi dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **moconavi**, selezionare **moconavi** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![moconavi nell'elenco risultati](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con moconavi con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di moconavi che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in moconavi.

Per configurare e testare l'accesso Single Sign-On di Azure AD con moconavi, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di moconavi](#create-a-moconavi-test-user)**: per avere una controparte di Britta Simon in moconavi collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione moconavi.

**Per configurare Single Sign-On di Azure AD con moconavi, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **moconavi** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Nella sezione **URL e dominio moconavi** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di moconavi](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<yourserverurl>/moconavi-saml2`

    C. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di moconavi](mailto:support@recomot.co.jp).

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/moconavi-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **moconavi**, è necessario inviare il file di **metadati XML** scaricato al [team di supporto di moconavi](mailto:support@recomot.co.jp). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/moconavi-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/moconavi-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/moconavi-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-moconavi-test-user"></a>Creare un utente di test di moconavi

In questa sezione viene creato un utente di nome Britta Simon in moconavi. Collaborare con il  [team di supporto di moconavi](mailto:support@recomot.co.jp)  per aggiungere gli utenti alla piattaforma moconavi. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a moconavi.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a moconavi seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **moconavi**.

    ![Collegamento di moconavi nell'elenco delle applicazioni](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

1. Installare moconavi da Microsoft Store.

2. Avviare moconavi.

3. Fare clic sul pulsante **Connect setting** (Impostazione di connessione).

    ![Test dell'accesso Single Sign-On](./media/moconavi-tutorial/testing1.png)

4. Immettere `https://mcs-admin.moconavi.biz/gateway` nella casella di testo **Connect to URL** (Connetti all'URL) e quindi fare clic sul pulsante **Done** (Fatto).

    ![Test dell'accesso Single Sign-On](./media/moconavi-tutorial/testing2.png)

5. Nella schermata riportata di seguito seguire questa procedura:

    ![Test dell'accesso Single Sign-On](./media/moconavi-tutorial/testing3.png)

    a. Immettere la **chiave di autenticazione di input**:`azureAD` nella casella di testo **Input Authentication Key** (Chiave di autenticazione di input).

    b. Immettere l'**ID utente di input**: `your ad account` nella casella di testo **Input User ID** (ID utente di input).

    c. Fare clic su **LOGIN** (ACCEDI).

6. Immettere la password di Azure AD nella casella di testo **Password** e quindi fare clic sul pulsante **Login** (Accedi).

    ![Test dell'accesso Single Sign-On](./media/moconavi-tutorial/testing4.png)

7. L'autenticazione di Azure AD ha esito positivo quando viene visualizzato il menu.

    ![Test dell'accesso Single Sign-On](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

