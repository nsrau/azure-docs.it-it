---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Palo Alto Networks - GlobalProtect | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8faa1d0ecfec629487c9cddee6a01fb9f809393f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165984"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Palo Alto Networks - GlobalProtect

Questa esercitazione descrive come integrare Palo Alto Networks - GlobalProtect con Azure Active Directory (Azure AD). Integrando Palo Alto Networks - GlobalProtect con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Palo Alto Networks - GlobalProtect.
* Abilitare gli utenti per l'accesso automatico a Palo Alto Networks - GlobalProtect (Single Sign-On) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Palo Alto Networks - GlobalProtect abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Palo Alto Networks - GlobalProtect supporta l'accesso SSO avviato da **provider di servizi**
* Palo Alto Networks - GlobalProtect supporta il provisioning utenti **JIT**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Aggiunta di Palo Alto Networks - GlobalProtect dalla raccolta

Per configurare l'integrazione di Palo Alto Networks - GlobalProtect in Azure AD, è necessario aggiungere Palo Alto Networks - GlobalProtect dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Palo Alto Networks - GlobalProtect** nella casella di ricerca.
1. Selezionare **Palo Alto Networks - GlobalProtect** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Palo Alto Networks - GlobalProtect

Configurare e testare l'accesso SSO di Azure AD con Palo Alto Networks - GlobalProtect usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - GlobalProtect.

Per configurare e testare l'accesso SSO di Azure AD con Palo Alto Networks - GlobalProtect, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Palo Alto Networks - GlobalProtect](#configure-palo-alto-networks---globalprotect-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Palo Alto Networks - GlobalProtect](#create-palo-alto-networks---globalprotect-test-user)** : per avere una controparte di B.Simon in Palo Alto Networks - GlobalProtect collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - GlobalProtect** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<Customer Firewall URL>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Palo Alto Networks - GlobalProtect** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - GlobalProtect.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - GlobalProtect**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Configurare l'accesso Single Sign-On di Palo Alto Networks - GlobalProtect

1. Aprire l'interfaccia utente di amministrazione del firewall Palo Alto Networks come amministratore in un'altra finestra del browser.

2. Fare clic su **Device** (Dispositivo).

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selezionare **SAML Identity Provider** (Provider di identità SAML) nella barra di spostamento a sinistra e fare clic su Import (Importa) per importare il file di metadati.

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Eseguire le operazioni seguenti nella finestra di importazione

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Nella casella di testo **Nome profilo** specificare un nome, ad esempio Azure AD Global Protect.

    b. In **Identity Provider Metadata** (Metadati provider di identità) fare clic su **Browse** (Sfoglia) e selezionare il file metadata.xml scaricato dal portale di Azure.

    c. Fare clic su **OK**.

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Creare l'utente di test di Palo Alto Networks - GlobalProtect

In questa sezione viene creato un utente di nome B.Simon in Palo Alto Networks - GlobalProtect. Palo Alto Networks - GlobalProtect supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Palo Alto Networks - GlobalProtect, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Palo Alto Networks - GlobalProtect nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Palo Alto Networks - GlobalProtect per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Palo Alto Networks - GlobalProtect con Azure AD](https://aad.portal.azure.com/)