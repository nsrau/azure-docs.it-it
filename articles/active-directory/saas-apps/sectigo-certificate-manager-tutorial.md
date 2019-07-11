---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sectigo Certificate Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588243"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Sectigo Certificate Manager

Questa esercitazione descrive come integrare Sectigo Certificate Manager con Azure Active Directory (Azure AD).

L'integrazione di Sectigo Certificate Manager con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a Sectigo Certificate Manager.
* Gli utenti possono accedere automaticamente a Sectigo Certificate Manager con gli account Azure AD personali (Single Sign-On).
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sectigo Certificate Manager, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Sottoscrizione di Sectigo Certificate Manager abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato Sectigo Certificate Manager con Azure AD.

Sectigo Certificate Manager supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da SP**
* **Accesso Single Sign-On avviato da IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Aggiungere Sectigo Certificate Manager nel portale di Azure

Per integrare Sectigo Certificate Manager con Azure AD, è necessario aggiungere il Sectigo Certificate Manager al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra, selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **Sectigo Certificate Manager**. Nei risultati della ricerca selezionare **Sectigo Certificate Manager**, quindi selezionare **Aggiungi**.

    ![Sectigo Certificate Manager nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sectigo Certificate Manager usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sectigo Certificate Manager.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sectigo Certificate Manager, è necessario completare le procedure di base seguenti:

| Attività | DESCRIZIONE |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Consente agli utenti di usare questa funzionalità. |
| **[Configurare l'accesso Single Sign-On di Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Consente di configurare le impostazioni di accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Consente di verificare l'accesso Single Sign-On di Azure AD per un utente chiamato Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Consente a Britta Simon di usare l'accesso Single Sign-On di Azure AD. |
| **[Creare un utente di test di Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Consente di creare una controparte di Britta Simon in Sectigo Certificate Manager collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD con Sectigo Certificate Manager nel portale di Azure.

1. Nel riquadro di integrazione dell'applicazione [Sectigo Certificate Manager](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On**, selezionare la modalità **SAML** o **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** seguire questa procedura per configurare la *modalità avviata da IDP*:

    1. Nella casella **Identificatore** immettere uno degli URL seguenti:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Nella casella **URL di risposta** immettere uno degli URL seguenti:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selezionare **Imposta URL aggiuntivi**.

    1. Nella casella **Stato dell'inoltro** immettere uno degli URL seguenti:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Sectigo Certificate Manager](common/idp-relay.png)

1.  Per configurare l'applicazione in *modalità avviata da SP*, seguire questa procedura:

    * Nella casella **URL di accesso** immettere uno degli URL seguenti:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Informazioni su URL e dominio per l'accesso Single Sign-On di Sectigo Certificate Manager](common/both-signonurl.png)

1. Nella sezione **Certificato di firma SAML** nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** accanto a **Certificato (Base64)** . Selezionare un'opzione di download in base alle esigenze. Salvare il certificato nel computer.

    ![Opzione di download Certificato (Base64)](common/certificatebase64.png)

1. Nella sezione **Configura Sectigo Certificate Manager** copiare gli URL seguenti in base alle esigenze:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurare l'accesso Single Sign-On di Sectigo Certificate Manager

Per configurare l'accesso Single Sign-On sul lato Sectigo Certificate Manager, inviare il file del certificato (Base64) scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Sectigo Certificate Manager](https://sectigo.com/support). Il team di supporto di Sectigo Certificate Manager usa le informazioni inviate per assicurarsi che la connessione Single Sign-On SAML venga impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Opzioni Utenti e Tutti gli utenti](common/users.png)

1. Selezionare **Nuovo utente**.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** completare la procedura seguente:

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **brittasimon\@\<dominio-azienda>.\<estensione>\>** . Ad esempio, **brittasimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a Sectigo Certificate Manager in modo che possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Sectigo Certificate Manager**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager nell'elenco delle applicazioni](common/all-applications.png)

1. Nel menu selezionare **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi**, selezionare **Britta Simon** nell'elenco degli utenti. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Seleziona ruolo**, selezionare il ruolo dell'utente dall'elenco. Scegliere **Seleziona**.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Creare un utente di test di Sectigo Certificate Manager

In questa sezione viene creato un utente di nome Britta Simon in Sectigo Certificate Manager. Collaborare con il [team di supporto di Sectigo Certificate Manager](https://sectigo.com/support) per aggiungere l'utente alla piattaforma Sectigo Certificate Manager. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Dopo aver configurato l'accesso Single Sign-On, quando si seleziona **Sectigo Certificate Manager** nel portale App personali, viene eseguito automaticamente l'accesso a Sectigo Certificate Manager. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


