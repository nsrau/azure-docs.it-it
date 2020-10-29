---
title: 'Esercitazione: Integrazione di Azure Active Directory con TigerConnect Secure Messenger | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516376"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Esercitazione: Integrazione di Azure Active Directory con TigerConnect Secure Messenger

Questa esercitazione descrive come integrare TigerConnect Secure Messenger con Azure Active Directory (Azure AD).

L'integrazione di TigerConnect Secure Messenger con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TigerConnect Secure Messenger.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TigerConnect Secure Messenger con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per dettagli sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con TigerConnect Secure Messenger, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione di TigerConnect Secure Messenger abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato TigerConnect Secure Messenger con Azure AD.

* TigerConnect Secure Messenger supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato TigerConnect Secure Messenger è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Aggiunta di TigerConnect Secure Messenger dalla raccolta

Per configurare l'integrazione di TigerConnect Secure Messenger in Azure AD, è necessario aggiungere TigerConnect Secure Messenger dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **TigerConnect Secure Messenger** nella casella di ricerca.
1. Selezionare **TigerConnect Secure Messenger** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TigerConnect Secure Messenger usando un utente di test di nome **Britta Simon** . Per il funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in TigerConnect Secure Messenger.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TigerConnect Secure Messenger, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** per avere una controparte di Britta Simon in TigerConnect Secure Messenger collegata all'utente di Azure AD di nome Britta Simon.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TigerConnect Secure Messenger, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **TigerConnect Secure Messenger** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella **URL di accesso** immettere un URL:

       `https://home.tigertext.com`

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Il valore **Identificatore (ID entità)** non è reale. è necessario aggiornare questo valore con l'ID effettivo. Per ottenere il valore, contattare il [team di supporto di TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). È anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , selezionare **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate e salvarlo nel computer.

    ![Opzione per il download del file XML dei metadati federazione](common/metadataxml.png)

1. Nella sezione **Configura TigerConnect Secure Messenger** copiare gli URL appropriati in base ai requisiti.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione B.Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TigerConnect Secure Messenger.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **TigerConnect Secure Messenger** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Configurare l'accesso SSO per TigerConnect Secure Messenger

Per configurare l'accesso Single Sign-On sul lato TigerConnect Secure Messenger, è necessario inviare il file di XML metadati federazione scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). Il team di TigerConnect Secure Messenger assicurerà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Creare un utente di test di TigerConnect Secure Messenger

In questa sezione viene creato un utente di nome Britta Simon in TigerConnect Secure Messenger. Collaborare con il [team di supporto di TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) per aggiungere l'utente Britta Simon a TigerConnect Secure Messenger. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona **TigerConnect Secure Messenger** nel portale App personali, si dovrebbe accedere automaticamente alla sottoscrizione TigerConnect Secure Messenger per la quale è stato impostato l'accesso Single Sign-On. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare TigerConnect Secure Messenger con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)