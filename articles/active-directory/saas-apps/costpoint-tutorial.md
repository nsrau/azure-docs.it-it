---
title: 'Esercitazione: Integrazione di Azure Active Directory con Costpoint | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879581"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Esercitazione: Integrare Costpoint con Azure Active Directory

Questa esercitazione descrive come integrare Costpoint con Azure Active Directory (Azure AD). Integrando Costpoint con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Costpoint.
* Abilitare gli utenti per l'accesso automatico a Costpoint con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Costpoint abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Costpoint supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="adding-costpoint-from-the-gallery"></a>Aggiunta di Costpoint dalla raccolta

Per configurare l'integrazione di Costpoint in Azure AD, è necessario aggiungere Costpoint dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Costpoint** nella casella di ricerca.
1. Selezionare **Costpoint** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Costpoint usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Costpoint.

Per configurare e testare l'accesso SSO di Azure AD con Costpoint, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Costpoint](#configure-costpoint)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Costpoint](#create-costpoint-test-user)** per avere una controparte di B. Simon in Costpoint collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Costpoint** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    > [!NOTE]
    > Si otterrà il file di metadati del provider di servizi in base alla procedura descritta più avanti nella sezione **Generare i metadati di Costpoint** dell'esercitazione.
 
    1. Fare clic su **Carica il file di metadati**.
    
    1. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.
    
    1. Dopo il caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nelle caselle di testo della sezione Costpoint

        > [!Note]
        > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze. Verificare che i valori di **Identificatore (ID entità)** e **URL di risposta (URL del servizio consumer di asserzione)** siano impostati correttamente e che il valore di **URL di controllo di accesso** corrisponda a un URL di Costpoint valido che termina con **/LoginServlet.cps**.

    1. Fare clic su **Impostare URL aggiuntivi**.

    1. Nella casella di testo **Stato dell'inoltro** digitare un valore nel formato seguente:`system=[your system], (for example, **system=DELTEKCP**)`

1. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:
    
    Nella casella di testo **URL di accesso** digitare un URL: `https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta e lo stato dell'inoltro effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Costpoint](https://www.deltek.com/about/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di copia per copiare il valore di **URL dei metadati di federazione dell'app** e salvarlo nel Blocco note.

   ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Generare i metadati di Costpoint

La configurazione SSO SAML di Costpoint è illustrata nella guida **DeltekCostpoint711Security.pdf**. In tale guida fare riferimento alla sezione **SAML Single Sign-on Setup -> Configure SAML Single Sign-on between Costpoint and Azure AD** (Configurazione dell'accesso Single Sign-On di SAML -> Configura l'accesso Single Sign-On di SAML tra Costpoint e Azure AD). Seguire le istruzioni e generare il file **XML dei metadati di federazione di Costpoint SP**. Usare questo file in **Configurazione SAML di base** nel portale di Azure.

![Utilità di configurazione di Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> È possibile richiedere la guida **DeltekCostpoint711Security.pdf** al [team di supporto clienti di Costpoint](https://www.deltek.com/about/contact-us). Se non si dispone di questo file, contattare il team per riceverlo.

### <a name="configure-costpoint"></a>Configurare Costpoint

Tornare a **Costpoint Configuration Utility** (Utilità di configurazione Costpoint) e incollare il valore dell'**URL dei metadati di federazione dell'app** nella casella di testo **IdP Federation Metadata XML** (XML metadati federazione IdP), quindi continuare con le istruzioni dalla guida **DeltekCostpoint711Security.pdf** per completare la configurazione SAML di Costpoint. 

![Utilità di configurazione di Costpoint](./media/costpoint-tutorial/config01.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Costpoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Costpoint**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-costpoint-test-user"></a>Creare l'utente di test di Costpoint

In questa sezione verrà creato un utente in Costpoint. Presupporre che l'**ID utente** sia **B.SIMON** e il nome sia **B.Simon**. Collaborare con il [team di supporto clienti di Costpoint](https://www.deltek.com/about/contact-us) per aggiungere gli utenti alla piattaforma Costpoint. L'utente deve essere creato e attivato prima di usare l'accesso Single Sign-On.
 
Dopo aver creato l'utente, l'opzione da selezionare come **Metodo di autenticazione** dell'utente deve essere **Active Directory**, la casella di controllo **Single Sign-on SAML** deve essere selezionata e il nome utente di Azure Active Directory deve essere **Active Directory o ID certificato** (come illustrato di seguito).

![Utente Costpoint](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Costpoint nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Costpoint per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)