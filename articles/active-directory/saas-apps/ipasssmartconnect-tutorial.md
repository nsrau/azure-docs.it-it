---
title: 'Esercitazione: integrazione di Azure Active Directory con iPass SmartConnect | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a68a208496904fcc8bfe13a227c61bf313214f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198168"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Esercitazione: integrazione di Azure Active Directory con iPass SmartConnect

Questa esercitazione descrive come integrare iPass SmartConnect con Azure Active Directory (Azure AD).

L'integrazione di iPass SmartConnect con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a iPass SmartConnect.
- È possibile abilitare gli utenti per l'accesso automatico a iPass SmartConnect (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iPass SmartConnect, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di iPass SmartConnect abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di iPass SmartConnect dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Aggiunta di iPass SmartConnect dalla raccolta
Per configurare l'integrazione di iPass SmartConnect in Azure AD, è necessario aggiungere iPass SmartConnect dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere iPass SmartConnect dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **iPass SmartConnect**, selezionare **iPass SmartConnect** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![iPass SmartConnect nell'elenco risultati](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iPass SmartConnect in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di iPass SmartConnect che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iPass SmartConnect.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iPass SmartConnect, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di iPass SmartConnect](#create-an-ipass-smartconnect-test-user)**: per avere una controparte di Britta Simon in iPass SmartConnect collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione iPass SmartConnect.

**Per configurare l'accesso Single Sign-On di Azure AD con iPass SmartConnect, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **iPass SmartConnect** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Nella sezione **URL e dominio iPass SmartConnect**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, non è necessario eseguire alcun passaggio.

    ![Informazioni su URL e dominio per Single Sign-On di iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Selezionare Mostra impostazioni URL avanzate e completare il passaggio seguente se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per Single Sign-On di iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Nella casella di testo URL di accesso digitare un URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. L'applicazione iPass SmartConnect prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/ipasssmartconnect-tutorial/attribute.png)

1. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per espandere gli attributi. Seguire questa procedura per ogni attributo visualizzato:

    | Nome attributo | Valore attributo | Valore spazio dei nomi|
    | ---------------| --------------- |----------------|
    | firstName | user.givenname |   |
    | lastName | user.surname | |
    | email | user.userprincipalname | |
    | username | user.userprincipalname | |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuoto il valore dello spazio dei nomi per tale riga.

    e. Fare clic su **OK**.

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Per configurare l'accesso Single Sign-On sul lato **iPass SmartConnect**, è necessario inviare il file **XML metadati** scaricato e il **nome di dominio** al [team di supporto di iPass SmartConnect](mailto:help@ipass.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-an-ipass-smartconnect-test-user"></a>Creare un utente di test di iPass SmartConnect

In questa sezione viene creato un utente chiamato Britta Simon in iPass SmartConnect. Rivolgersi al  [team di supporto di iPass SmartConnect](mailto:help@ipass.com)  per aggiungere gli utenti o il dominio necessario per essere inclusi nella piattaforma iPass SmartConnect. Se il dominio viene aggiunto dal team, verrà automaticamente effettuato il provisioning degli utenti per la piattaforma iPass SmartConnect. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iPass SmartConnect.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a iPass SmartConnect, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

1. Nell'elenco di applicazioni selezionare **iPass SmartConnect**.

    ![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

**Per testare l'applicazione nel flusso avviato da SP, seguire questa procedura:**

a. Scaricare il client iPass SmartConnect per Windows [qui](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installare il client e avviarlo.

c. Fare clic su **Get Started** (Inizia).

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Immettere il nome utente di Azure con il dominio. Fare clic su **Continue** (Continua). Si verrà reindirizzati alla pagina di accesso di Azure

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Al termine dell'autenticazione, verrà avviata l'attivazione del client. Il client verrà attivato.

**Per testare l'applicazione nel flusso avviato dal provider di identità, seguire questa procedura:**

a. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Fare clic sull'app iPass SmartConnect.

c. Viene aperta la pagina SSA. Fare clic su **Download App for Windows** (Scarica app per Windows) per installare il client iPass SmartConnect.

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing4.png)

d. Dopo l'installazione, il client, al primo avvio, avvierà automaticamente l'attivazione dopo l'accettazione dei termini e delle condizioni.

e. Se l'attivazione non viene avviata, fare clic sul pulsante Activate (Attiva) nella pagina SSA per avviare l'attivazione.

f. Il client verrà attivato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

