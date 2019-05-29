---
title: 'Esercitazione: Integrazione di Azure Active Directory con TurboRater | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 1e67f3c7f9413382d169837eaba50d6af18552e6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956682"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Esercitazione: Integrazione di Azure Active Directory con TurboRater

Questa esercitazione descrive come integrare TurboRater con Azure Active Directory (Azure AD).

L'integrazione di TurboRater con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TurboRater.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TurboRater con gli account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per dettagli sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TurboRater, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a TurboRater con accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

TurboRater supporta l'accesso Single Sign-On (SSO) avviato da IDP.

## <a name="add-turborater-from-the-azure-marketplace"></a>Aggiungere TurboRater da Azure Marketplace

Per configurare l'integrazione di TurboRater in Azure AD, è necessario aggiungere TurboRater da Azure Marketplace al proprio elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com?azure-portal=true).
1. Nel riquadro sinistro selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Opzione Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **+ New application** (+Nuova applicazione) nella parte superiore del riquadro.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **TurboRater**. Nei risultati della ricerca selezionare **TurboRater**, quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![TurboRater nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TurboRater in base a un utente di test di nome **B Simon**. Per il funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in TurboRater.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TurboRater, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare l'accesso Single Sign-On di TurboRater](#configure-turborater-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test TurboRater](#create-a-turborater-test-user)** in modo che vi sia un utente denominato B. Simon in TurboRater collegato a un utente di Azure AD denominato B. Simon.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TurboRater, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [TurboRater](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** eseguire la procedura seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TurboRater](common/idp-intiated.png)

    1. Nella casella **Identificatore (ID entità)** immettere un URL:

       `https://www.itcdataservices.com`

    1. Nella casella **Reply URL (Assertion Consumer Service URL)** (URL di risposta (URL del servizio consumer di asserzione)) immettere un URL con il formato seguente:

       | Environment | URL |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di TurboRater](https://www.getitc.com/support). È anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate e salvarlo nel computer.

    ![Opzione per il download del file XML dei metadati federazione](common/metadataxml.png)

1. Nel riquadro **Configura TurboRater** copiare l'URL o gli URL necessari:

   * **URL di accesso**
   * **Identificatore Azure AD**
   * **URL di disconnessione**

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Configurare l'accesso Single Sign-On di TurboRater

Per configurare l'accesso Single Sign-On sul lato TurboRater, è necessario inviare il file di XML metadati federazione scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di TurboRater](https://www.getitc.com/support). Il team TurboRater assicurerà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory**   > **Utenti** > **Tutti gli utenti**.

    ![Opzioni "Utenti" e "Tutti gli utenti"](common/users.png)

1. Selezionare **+Nuovo utente** in alto nella schermata.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** eseguire i seguenti passaggi:

    ![Riquadro Utente](common/user-properties.png)

    1. Nella casella **Name** (Nome) immettere **BSimon**.
  
    1. Nella casella **Nome utente** immettere **BSimon\@\<dominioazienda>.\<estensione>** . Ad esempio, **BSimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione B. Simon riceve l'abilitazione per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TurboRater.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **TurboRater**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **TurboRater**.

    ![TurboRater nell'elenco delle applicazioni](common/all-applications.png)

1. Nel riquadro sinistro in **GESTISCI**fare clic su **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **+Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **B. Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del riquadro.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona** nella parte inferiore del riquadro.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-turborater-test-user"></a>Creare un utente di test TurboRater

In questa sezione viene creato un utente di nome B. Simon in TurboRater. Collaborare con il [team di supporto di TurboRater](https://www.getitc.com/support) per aggiungere B. Simon come utente in TurboRater. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona **TurboRater** nel portale App personali, si dovrebbe automaticamente accedere alla sottoscrizione TurboRater per la quale è stato impostato l'accesso Single Sign-On. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Un elenco di esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
