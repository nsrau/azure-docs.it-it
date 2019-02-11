---
title: 'Esercitazione: Integrazione di Azure Active Directory con HubSpot | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.openlocfilehash: 2bd47c7955625eb43de40e61515bf7814a8c8355
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815830"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Esercitazione: Integrazione di Azure Active Directory con HubSpot

Questa esercitazione illustra come integrare HubSpot con Azure Active Directory (Azure AD).

L'integrazione di HubSpot con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a HubSpot.
- È possibile abilitare gli utenti per l'accesso automatico a HubSpot (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con HubSpot, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di HubSpot abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di HubSpot dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-hubspot-from-the-gallery"></a>Aggiunta di HubSpot dalla raccolta

Per configurare l'integrazione di HubSpot in Azure AD, è necessario aggiungere HubSpot dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere HubSpot dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **HubSpot**. Nel pannello dei risultati selezionare **HubSpot** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con HubSpot usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di HubSpot che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HubSpot.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HubSpot, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **Creazione di un utente test di HubSpot**: per avere una controparte di Britta Simon in HubSpot collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione HubSpot.

**Per configurare l'accesso Single Sign-On di Azure AD con HubSpot, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **HubSpot** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_general_301.png)

3. Se è necessario passare alla modalità **SAML** da qualsiasi altra modalità, fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_general_300.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_general_302.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione. Per ottenere questi valori, contattare il [team di supporto di HubSpot](https://help.hubspot.com/).

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://app.hubspot.com/login`

6. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Nella sezione **Configura HubSpot** fare clic sul pulsante di copia per copiare i valori **URL di accesso** e **Identificatore Azure AD**.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Aprire una nuova scheda del browser e accedere all'account di amministratore di HubSpot.

9. Fare clic sull'**icona delle impostazioni** nell'angolo in alto a destra della pagina.

    ![Configure Single Sign-On](./media/hubspot-tutorial/config1.png)

10. Fare clic su **Account Defaults** (Impostazioni predefinite account).

    ![Configure Single Sign-On](./media/hubspot-tutorial/config2.png)

11. Scorrere verso il basso fino alla sezione **Security** (Sicurezza), quindi fare clic su **Set up** (Configura).

    ![Configure Single Sign-On](./media/hubspot-tutorial/config3.png)

12. Nella sezione **Set Up Single Sign-On** (Configura l'accesso Single Sign-On) seguire questa procedura:

    ![Configure Single Sign-On](./media/hubspot-tutorial/config4.png)

    a. Fare clic sul pulsante di **copia** per copiare il valore di **Audience URl - Service Provider Entity ID)** (URI destinatario - ID entità provider servizi) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** nel portale di Azure.

    b. Fare clic sul pulsante di **copia** per copiare il valore di **Sign on URL, ACS, Recipient, or Redirect** (URL di accesso, ACS, destinatario o reindirizzamento) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** nel portale di Azure.

    c. Nella casella di testo **Identity Provider Identifier or Issuer URL** (Identificatore provider identità o URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **URL di accesso Single Sign-On del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Aprire il file  **Certificato (Base64)**  scaricato in Blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella  **Certificato X.509**.

    f. Fare clic su **Verifica**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](./media/hubspot-tutorial/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](./media/hubspot-tutorial/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-hubspot-test-user"></a>Creazione di un utente di test di HubSpot

Per consentire agli utenti di Azure AD di accedere a HubSpot, è necessario effettuarne il provisioning in HubSpot.
Nel caso di HubSpot, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **HubSpot** come amministratore.

2. Fare clic sull'**icona delle impostazioni** nell'angolo in alto a destra della pagina.

    ![Configure Single Sign-On](./media/hubspot-tutorial/config1.png)

3. Fare clic su **Users & Teams** (Utenti e team).

    ![Configure Single Sign-On](./media/hubspot-tutorial/user1.png)

4. Fare clic su **Create User** (Crea utente).

    ![Configure Single Sign-On](./media/hubspot-tutorial/user2.png)

5. Immettere l'indirizzo e-mail dell'utente come **brittasimon@contoso.com** nella casella di testo **Add email addess(es)** (Aggiungi indirizzi e-mail) e fare clic su **Next** (Avanti).

    ![Configure Single Sign-On](./media/hubspot-tutorial/user3.png)

6. Nella sezione **Create users** (Crea utenti) scorrere ogni scheda e selezionare le opzioni e le autorizzazioni appropriate per il nome utente e fare clic su **Next** (Avanti).

    ![Configure Single Sign-On](./media/hubspot-tutorial/user4.png)

7. Fare clic su **Send** (Invia) per inviare l'invito all'utente.

    ![Configure Single Sign-On](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Dopo aver accettato l'invito l'utente viene attivato.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HubSpot.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **HubSpot**.

    ![Configure Single Sign-On](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro HubSpot nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione HubSpot.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
