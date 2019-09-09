---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ADP | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ADP

Questa esercitazione descrive come integrare ADP con Azure Active Directory (Azure AD). Integrando ADP con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad ADP.
* Abilitare gli utenti per l'accesso automatico ad ADP con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ADP abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ADP supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-adp-from-the-gallery"></a>Aggiunta di ADP dalla raccolta

Per configurare l'integrazione di ADP in Azure AD, è necessario aggiungere ADP dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ADP** nella casella di ricerca.
1. Selezionare **ADP** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ADP

Configurare e testare l'accesso SSO di Azure AD con ADP usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ADP.

Per configurare e testare l'accesso SSO di Azure AD con ADP, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di ADP](#configure-adp-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di ADP](#create-adp-test-user)** : per avere una controparte di B.Simon in ADP collegata alla relativa rappresentazione in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura: 

    ![Proprietà del servizio Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    b. Copiare l'**URL accesso utente** che dovrà essere incollato nella sezione **Configura URL di accesso**, descritta più avanti nell'esercitazione.

    c. Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    d. Impostare il valore del campo **Visibile agli utenti** su **No**.

1. Nella pagina di integrazione dell'applicazione **ADP** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Nella casella di testo **Identificatore (ID entità)** digitare un URL: `https://fed.adp.com`

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura ADP** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ADP.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ADP**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adp-sso"></a>Configurare l'accesso Single Sign-On di ADP

Per configurare l'accesso Single Sign-On sul lato **ADP**, è necessario caricare il file **XML dei metadati** scaricato nel [sito Web di ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Questo processo potrebbe richiedere qualche giorno.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurare i servizi ADP per l'accesso federato

>[!Important]
> I dipendenti che richiedono l'accesso federato ai servizi ADP devono essere assegnati all'app di servizio ADP e, successivamente, gli utenti devono essere riassegnati al servizio ADP specifico.
Al momento della ricezione della conferma dal rappresentante ADP, configurare i servizi ADP e assegnare/gestire gli utenti per controllare l'accesso al servizio ADP specifico.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ADP** nella casella di ricerca.
1. Selezionare **ADP** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.
1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura:  

    ![Proprietà collegate per l'accesso Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    b.  Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    c.  Impostare il valore del campo **Visibile agli utenti** su **Sì**.

1. Nella pagina di integrazione dell'applicazione **ADP** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **Collegato** come **Modalità**. Per collegare l'applicazione ad **ADP**.

    ![Single Sign-On collegato](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Nella sezione **Configura URL di accesso** seguire questa procedura:

    ![Proprietà Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Incollare l'**URL di accesso utente** copiato in precedenza dalla scheda **Proprietà** (dall'app ADP principale).
                                                             
    b. Le 5 app seguenti supportano **URL di stato dell'inoltro** diversi. È necessario aggiungere manualmente il valore appropriato dell'**URL di stato dell'inoltro** per una particolare applicazione all'**URL di accesso utente**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salvare** le modifiche.

10. Dopo aver ricevuto conferma dal rappresentante ADP, iniziare i test con uno o due utenti.

    a. Assegnare alcuni utenti all'app del servizio ADP per testare l'accesso federato.

    b. Il test ha esito positivo quando gli utenti accedono all'app del servizio ADP nella raccolta e possono accedere al rispettivo servizio ADP.
 
11. Alla conferma dell'esito positivo del test, assegnare il servizio federato ADP a singoli utenti o gruppi di utenti, come illustrato più avanti nell'esercitazione, e distribuirlo ai dipendenti.

### <a name="create-adp-test-user"></a>Creare l'utente di test di ADP

Questa sezione descrive come creare un utente di nome B.Simon in ADP. Collaborare con il [team di supporto di ADP](https://www.adp.com/contact-us/overview.aspx) per aggiungere gli utenti nell'account ADP. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ADP nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ADP per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare ADP con Azure AD](https://aad.portal.azure.com)
