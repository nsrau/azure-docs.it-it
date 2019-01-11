---
title: 'Esercitazione: Integrazione di Azure Active Directory con Tableau Server | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: c727cddf41c269c214b541134cd9f688017ee687
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789722"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Server

Questa esercitazione descrive come integrare Tableau Server con Azure Active Directory (Azure AD).

L'integrazione di Tableau Server con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Tableau Server.
- È possibile abilitare gli utenti per l'accesso automatico a Tableau Server (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Tableau Server, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Tableau Server abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Tableau Server dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Aggiunta di Tableau Server dalla raccolta

Per configurare l'integrazione di Tableau Server in Azure AD, è necessario aggiungere Tableau Server dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tableau Server dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Tableau Server**, selezionare **Tableau Server** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Tableau Server nell'elenco risultati](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Server usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Tableau Server corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Server.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Server, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Tableau Server](#configure-tableau-server-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creare un utente di test di Tableau Server](#create-tableau-server-test-user)**: per avere una controparte di Britta Simon in Tableau Server collegata alla rappresentazione dell'utente in Azure AD.
5. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Tableau Server.

**Per configurare l'accesso Single Sign-On di Azure AD con Tableau Server, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Tableau Server** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial-general-301.png)

3. L'applicazione Tableau Server prevede un'attestazione personalizzata **username** che deve essere definita come indicato di seguito. Questa attestazione viene usata come attestazione di identificatore utente invece dell'attestazione di identificatore univoco dell'utente. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi e attestazioni utente**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente e attestazioni** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo | Spazio dei nomi |
    | ---------------| --------------- | ----------- |   
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Immettere il valore **Nome spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Save**.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

6. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://azure.<domain name>.link`
    
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://azure.<domain name>.link`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornare i valori con l'URL e l'identificatore effettivi indicati nella pagina di configurazione di Tableau Server, come illustrato più avanti in questa esercitazione.

7. Nella sezione **Certificato di firma SAML** della pagina **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **XML dei metadati della federazione** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>Configurare il Single Sign-On di Tableau Server 

1. Per configurare l'accesso SSO sull'applicazione, è necessario accedere al tenant di Tableau Server come amministratore.

2. Nella scheda **CONFIGURATION** (Configurazione) selezionare **User Identity & Access**(Identità utente e accesso), quindi selezionare la scheda **Authentication Method** (Metodo di autenticazione).

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Nella pagina **CONFIGURATION** (Configurazione) seguire questa procedura:

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Per **Authentication Method** (Metodo di autenticazione) selezionare SAML.
    
    b. Selezionare la casella di controllo **Enable SAML Authentication for the server** (Abilita autenticazione SAML per il server).

    c. Tableau Server return URL (URL restituito di Tableau Server): l'URL a cui accedono gli utenti di Tableau Server, ad esempio http://tableau_server. L'uso di http://localhost non è consigliato. Gli URL con barra finale (ad esempio http://tableau_server/)) non sono supportati. Copiare l'**URL restituito di Tableau Server** e incollarlo nella casella di testo **URL di accesso** in Azure AD nella sezione **URL e dominio Tableau Server**.

    d. SAML entity ID (ID entità SAML): l'ID entità identifica in modo univoco l'installazione di Tableau Server nel provider di identità. Se si desidera, qui è possibile immettere di nuovo l'URL di Tableau Server, ma non deve essere l'URL di Tableau Server in uso. Copiare l'**ID di entità SAML** e incollarlo nella casella di testo **Identificatore** in Azure AD nella sezione **URL e dominio Tableau Server**.

    e. Fare clic su **Download XML Metadata File** (Scarica file di metadati XML) e aprire il file nell'applicazione editor di testo. Cercare l'URL del servizio consumer di asserzione con Http Post e Indice 0 e copiarlo. Incollarlo quindi nella casella di testo **URL di risposta** di Azure AD nella sezione **URL e dominio Tableau Server**.

    f. Individuare il file di metadati della federazione scaricato dal portale di Azure e quindi caricarlo in **SAML Idp metadata file**(File metadati IdP SAML).

    g. Immettere i nomi per gli attributi usati dal provider di identità per contenere nomi utente, nomi visualizzati e indirizzi di posta elettronica.

    h. Fare clic su **Save**

    >[!NOTE] 
    >Il cliente deve caricare eventuali certificati nella configurazione SSO SAML di Tableau Server e il passaggio verrà ignorato nel flusso SSO.
    >Per assistenza nella configurazione di SAML in Tableau Server, vedere questo articolo: [Configure SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm) (Configurazione di SAML).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create-aaduser-01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create-aaduser-02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
  
### <a name="create-tableau-server-test-user"></a>Creare un utente test di Tableau Server

Questa sezione descrive come creare un utente chiamato Britta Simon in Tableau Server. In Tableau Server è necessario eseguire il provisioning di tutti gli utenti. 

Il valore username dell'utente deve corrispondere al valore configurato nell'attributo personalizzato **username**in Azure AD. Con un mapping corretto, l'integrazione per la [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)dovrebbe funzionare.

>[!NOTE]
>Se si deve creare manualmente un utente, è necessario contattare l'amministratore di Tableau Server dell'organizzazione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Server.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Tableau Server**.

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Server nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Tableau Server.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
