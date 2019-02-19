---
title: 'Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Symantec Web Security Service (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf4d2d18971948e15cca2e1b91d9d55a73cefe4b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187917"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS)

Questa esercitazione descrive come integrare l'account Symantec Web Security Service con l'account Azure Active Directory (Azure AD) in modo che WSS possa autenticare un utente finale di cui è stato effettuato il provisioning in Azure AD usando l'autenticazione SAML e applicare regole dei criteri a livello di utente o gruppo.

L'integrazione di Symantec Web Security Service (WSS) con Azure AD offre i vantaggi seguenti:

- Gestire tutti gli utenti finali e i gruppi usati dall'account WSS dal portale di Azure AD.

- Consentire agli utenti finali di autenticarsi in WSS usando le credenziali di Azure AD.

- Abilitare l'applicazione delle regole dei criteri a livello di utente e gruppo definite nell'account WSS.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Symantec Web Security Service (WSS), sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Symantec Web Security Service (WSS) abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Symantec Web Security Service (WSS) supporta l'accesso SSO avviato da **IDP**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Aggiunta di Symantec Web Security Service (WSS) dalla raccolta

Per configurare l'integrazione di Symantec Web Security Service (WSS) in Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Symantec Web Security Service (WSS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Symantec Web Security Service (WSS)**, selezionare **Symantec Web Security Service (WSS)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Symantec Web Security Service (WSS) nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS) usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Symantec Web Security Service (WSS).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **Configurare l'accesso Single Sign-On per Symantec Web Security Service (WSS)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)**: per avere una controparte di Britta Simon in Symantec Web Security Service (WSS) collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS), seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Symantec Web Security Service (WSS)** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Nella casella di testo **URL di risposta** digitare un URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contattare il [team di supporto clienti di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se per qualche motivo i valori **Identificatore** e **URL di risposta** non funzionano. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configurare l'accesso Single Sign-On per Symantec Web Security Service (WSS)

Per configurare l'accesso Single Sign-On sul lato Symantec Web Security Service (WSS), fare riferimento alla documentazione online di WSS. Il file **XML dei metadati della federazione** scaricato dovrà essere importato nel portale di WSS. Per assistenza per la configurazione nel portale di WSS, contattare il [team di supporto di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Symantec Web Security Service (WSS).

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Symantec Web Security Service (WSS)**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Symantec Web Security Service (WSS)**.

    ![Collegamento di Symantec Web Security Service (WSS) nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Creare l'utente di test di Symantec Web Security Service (WSS)

In questa sezione viene creato un utente di nome Britta Simon in Symantec Web Security Service (WSS). Il nome utente corrispondente può essere creato manualmente nel portale di WSS oppure è possibile attendere alcuni minuti (circa 15) affinché venga eseguita la sincronizzazione con il portale di WSS degli utenti/gruppi di cui è stato effettuato il provisioning in Azure AD. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. È anche necessario effettuare il provisioning nel portale di Symantec Web Security Service (WSS) dell'indirizzo IP pubblico del computer dell'utente finale che verrà usato per esplorare i siti Web.

> [!NOTE]
> Fare clic [qui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) per ottenere l'indirizzo IP pubblico del computer.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione verrà testata la funzionalità Single Sign-On dopo aver configurato l'account WSS per l'uso di Azure AD per l'autenticazione SAML.

Dopo aver configurato il Web browser per inoltrare il traffico a WSS, quando si apre il Web browser e si prova a esplorare un sito, si viene reindirizzati alla pagina di accesso di Azure. Immettere le credenziali dell'utente finale di test di cui è stato effettuato il provisioning in Azure AD (ovvero BrittaSimon) e la password associata. Una volta eseguita l'autenticazione, sarà possibile esplorare il sito Web scelto. Se si crea una regola dei criteri sul lato WSS per impedire a BrittaSimon l'esplorazione di un sito specifico, quando si prova a esplorare tale sito come utente BrittaSimon viene visualizzata la pagina di blocco di WSS.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

