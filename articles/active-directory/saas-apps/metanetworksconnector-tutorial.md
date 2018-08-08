---
title: 'Esercitazione: Integrazione di Azure Active Directory con Meta Networks Connector | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e27ba7d5b245d8857f0c07bfe2923afe9d7e3a0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267532"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Esercitazione: Integrazione di Azure Active Directory con Meta Networks Connector

Questa esercitazione descrive come integrare Meta Networks Connector con Azure Active Directory (Azure AD).

L'integrazione di Meta Networks Connector con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Meta Networks Connector.
- È possibile abilitare l'accesso automatico degli utenti a Meta Networks Connector (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Meta Networks Connector, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Meta Networks Connector abilitata per Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Meta Networks Connector dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Aggiunta di Meta Networks Connector dalla raccolta
Per configurare l'integrazione di Meta Networks Connector in Azure AD, è necessario aggiungere Meta Networks Connector dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Meta Networks Connector dalla raccolta, completare i passaggi seguenti:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Meta Networks Connector**, selezionare **Meta Networks Connector** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Meta Networks Connector nell'elenco dei risultati](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Meta Networks Connector con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in Meta Networks Connector. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Meta Networks Connector.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Meta Networks Connector, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Meta Networks Connector](#create-a-meta-networks-connector-test-user)**: per avere una controparte di Britta Simon in Meta Networks Connector collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione Meta Networks Connector.

**Per configurare l'accesso Single Sign-On di Azure AD con Meta Networks Connector, completare i passaggi seguenti:**

1. Nella pagina di integrazione dell'applicazione **Meta Networks Connector** nel portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

3. Nella sezione **URL e dominio di Meta Networks Connector** completare i passaggi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura viene descritta più avanti nell'esercitazione.

5. L'applicazione Meta Networks Connector prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:

    | Nome attributo | Valore attributo | NAMESPACE|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nella casella di testo **Spazio dei nomi**, digitare il valore dello spazio dei nomi indicato per la riga.

    e. Fare clic su **Ok**

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)

8. Nella sezione **Configurazione di Meta Networks Connector** fare clic su **Configura Meta Networks Connector** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)

9. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_general_400.png)

10. Aprire una nuova scheda del browser e accedere all'account amministratore di Meta Networks Connector.

    > [!NOTE]
    > Meta Networks Connector è un sistema sicuro. Di conseguenza, prima di accedere al portale, è necessario ottenere un indirizzo IP pubblico incluso nell'elenco degli indirizzi consentiti sul lato del portale. Per ottenere l'indirizzo IP pubblico, seguire il collegamento specificato [qui](https://whatismyipaddress.com/). Inviare l'indirizzo IP al [team di supporto clienti di Meta Networks Connector](mailto:support@metanetworks.com) perché venga inserito nell'elenco degli indirizzi IP consentiti.

11. Passare ad **Amministratore** e selezionare **Impostazioni**.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/configure3.png)

12. Assicurarsi che le opzioni **Log Internet Traffic** (Registra traffico Internet) e **Force VPN MFA** (Forza MFA VPN) siano disattivate.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/configure1.png)

13. Passare ad **Amministratore** e selezionare **SAML**.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/configure4.png)

14. Nella pagina **DETTAGLI** completare i passaggi seguenti:

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/configure2.png)

    a. Copiare il valore del campo **SSO URL** (URL di Single Sign-On) nella casella di testo **URL di accesso** della sezione **URL e dominio Meta Networks Connector**.

    b. Copiare il valore del campo **Recipient URL** (URL destinatario) nella casella di testo **URL di risposta** della sezione **URL e dominio Meta Networks Connector**.

    c. Copiare il valore del campo **Audience URI (SP Entity ID)** (URI destinatario - ID entità provider di servizi) nella casella di testo **Identificatore (ID entità)** della sezione **URL e dominio Meta Networks Connector**.

    d. Abilitare SAML

15. Nella scheda **GENERALE** completare i passaggi seguenti:

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/configure5.png)

    a. Nella casella di testo **URL di accesso Single Sign-On del provider di identità** incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    b. Nella casella di testo **Autorità di certificazione del provider di identità** incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Certificato X.509**.

    d. Abilitare il **provisioning JIT (Just-in-Time)**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/metanetworksconnector-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/metanetworksconnector-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/metanetworksconnector-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/metanetworksconnector-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-meta-networks-connector-test-user"></a>Creare un utente di test di Meta Networks Connector

Questa sezione descrive come creare un utente di nome Britta Simon in Meta Networks Connector. Meta Networks Connector supporta il provisioning JIT (Just-In-Time), che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a Meta Networks Connector.

>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Meta Networks Connector.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Meta Networks Connector, completare i passaggi seguenti:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Meta Networks Connector**.

    ![Collegamento a Meta Networks Connector nell'elenco delle applicazioni](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Meta Networks Connector nel pannello di accesso, si accederà automaticamente all'applicazione Meta Networks Connector.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

