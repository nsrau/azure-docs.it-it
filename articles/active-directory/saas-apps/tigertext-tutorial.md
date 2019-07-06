---
title: 'Esercitazione: Integrazione di Azure Active Directory con TigerText Secure Messenger | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088667"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Esercitazione: Integrazione di Azure Active Directory con TigerText Secure Messenger

Questa esercitazione descrive come integrare TigerText Secure Messenger con Azure Active Directory (Azure AD).

L'integrazione di TigerText Secure Messenger con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TigerText Secure Messenger.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TigerText Secure Messenger con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per dettagli sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TigerText Secure Messenger, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione di TigerText Secure Messenger abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato TigerText Secure Messenger con Azure AD.

TigerText Secure Messenger supporta l'accesso Single Sign-On (SSO) avviato da SP.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Aggiungere TigerText Secure Messenger da Azure Marketplace

Per configurare l'integrazione di TigerText Secure Messenger in Azure AD, è necessario aggiungere TigerText Secure Messenger da Azure Marketplace all'elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com?azure-portal=true).
1. Nel riquadro sinistro selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **+ New application** (+Nuova applicazione) nella parte superiore del riquadro.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **TigerText Secure Messenger**. Nei risultati della ricerca selezionare **TigerText Secure Messenger**, quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![TigerText Secure Messenger nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TigerText Secure Messenger usando un utente di test di nome **Britta Simon**. Per il funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in TigerText Secure Messenger.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TigerText Secure Messenger, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare l'accesso Single Sign-On di TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test di TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** per avere una controparte di Britta Simon in TigerText Secure Messenger collegata all'utente di Azure AD di nome Britta Simon.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TigerText Secure Messenger, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **TigerText Secure Messenger** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** eseguire la procedura seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TigerText Secure Messenger](common/sp-identifier.png)

    1. Nella casella **URL di accesso** immettere un URL:

       `https://home.tigertext.com`

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Il valore **Identificatore (ID entità)** non è reale. è necessario aggiornare questo valore con l'ID effettivo. Per ottenere il valore, contattare il [team di supporto di TigerText Secure Messenger](mailto:prosupport@tigertext.com). È anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate e salvarlo nel computer.

    ![Opzione per il download del file XML dei metadati federazione](common/metadataxml.png)

1. Nella sezione **Configura TigerText Secure Messenger** copiare l'URL o gli URL necessari:

   * **URL di accesso**
   * **Identificatore Azure AD**
   * **URL di disconnessione**

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configurare l'accesso Single Sign-On di TigerText Secure Messenger

Per configurare l'accesso Single Sign-On sul lato TigerText Secure Messenger, è necessario inviare il file di XML metadati federazione scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di TigerText Secure Messenger](mailto:prosupport@tigertext.com). Il team di TigerText Secure Messenger assicurerà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory**   > **Utenti** > **Tutti gli utenti**.

    ![Opzioni "Utenti" e "Tutti gli utenti"](common/users.png)

1. Selezionare **+Nuovo utente** in alto nella schermata.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** eseguire i seguenti passaggi:

    ![Riquadro Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon\@\<dominioazienda>.\<estensione>** . Ad esempio, **BrittaSimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TigerText Secure Messenger.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **TigerText Secure Messenger**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **TigerText Secure Messenger**.

    ![TigerText Secure Messenger nell'elenco delle applicazioni](common/all-applications.png)

1. Nel riquadro sinistro in **GESTISCI**fare clic su **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **+Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del riquadro.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona** nella parte inferiore del riquadro.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Creare un utente di test di TigerText Secure Messenger

In questa sezione viene creato un utente di nome Britta Simon in TigerText Secure Messenger. Collaborare con il [team di supporto di TigerText Secure Messenger](mailto:prosupport@tigertext.com) per aggiungere l'utente Britta Simon a TigerText Secure Messenger. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona **TigerText Secure Messenger** nel portale App personali, si dovrebbe accedere automaticamente alla sottoscrizione TigerText Secure Messenger per la quale è stato impostato l'accesso Single Sign-On. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
