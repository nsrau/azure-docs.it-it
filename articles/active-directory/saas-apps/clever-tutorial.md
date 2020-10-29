---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Clever | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Clever.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: d232048cbc5592b9b0fcacea6ee44e00e8d671e2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455834"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Clever

Questa esercitazione descrive come integrare Clever con Azure Active Directory (Azure AD). Integrando Clever con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Clever.
* Abilitare gli utenti per l'accesso automatico a Clever con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Clever abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Clever supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Concur, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-clever-from-the-gallery"></a>Aggiunta di Clever dalla raccolta

Per configurare l'integrazione di Clever in Azure AD, è necessario aggiungere Clever dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Clever** nella casella di ricerca.
1. Selezionare **Clever** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-clever"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Clever

Configurare e testare l'accesso SSO di Azure AD con Clever usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Clever.

Per configurare e testare l'accesso SSO di Azure AD con Clever, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Clever](#configure-clever-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Clever](#create-clever-test-user)** : per avere una controparte di B.Simon in Clever collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Clever** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://clever.com/in/<companyname>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://clever.com/oauth/saml/metadata.xml`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://clever.com/<companyname>`
    
    > [!NOTE]
    >  Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. Per ottenere tale valore, contattare il [team di supporto clienti di Clever](https://clever.com/about/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l' **URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Clever.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Clever** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-clever-sso"></a>Configurare l'accesso Single Sign-On di Clever

1. In un'altra finestra del Web browser accedere al sito aziendale di Clever come amministratore.

1. Nella barra degli strumenti fare clic su **Instant Login** .

    ![Accesso immediato](./media/clever-tutorial/ic798984.png "Instant Login")

    > [!NOTE]
    > Prima di poter testare l'accesso Single Sign-On, è necessario contattare il [team di supporto clienti di Clever](https://clever.com/about/contact/) per abilitare l'accesso SSO di Microsoft 365 nel back-end.

1. Nella pagina **Instant Login** seguire questa procedura:
 
    ![Configurazione dell'accesso SSO nella pagina Instant Login](./media/clever-tutorial/ic798985.png "Instant Login")

    a. Digitare un valore in **URL di accesso** .

    >[!NOTE]
    >Per **Login URL** usare un valore personalizzato. Per ottenere questo valore, contattare il [team di supporto clienti di Clever](https://clever.com/about/contact/).

    b. Per **Identity System** (Sistema di identità) scegliere **ADFS** .

    c. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell' **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Fare clic su **Salva** .

### <a name="create-clever-test-user"></a>Creare l'utente di test di Clever

Per consentire agli utenti di Azure AD di accedere a Clever, è necessario effettuarne il provisioning in Clever.

Nel caso di Clever, collaborare con il [team di supporto clienti di Clever](https://clever.com/about/contact/) per aggiungere gli utenti alla piattaforma Clever. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente di Clever per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Clever nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Clever per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Clever con Azure AD](https://aad.portal.azure.com/)