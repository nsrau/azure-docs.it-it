---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sectigo Certificate Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 905ca5fd92a09b209bf099bfac0862132ec679a4
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875385"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Sectigo Certificate Manager

Questa esercitazione descrive come integrare Sectigo Certificate Manager (noto anche come SCM) con Azure Active Directory (Azure AD).

L'integrazione di Sectigo Certificate Manager con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a Sectigo Certificate Manager.
* Gli utenti possono accedere automaticamente a Sectigo Certificate Manager con gli account Azure AD personali (Single Sign-On).
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sectigo Certificate Manager, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account di Sectigo Certificate Manager.

> [!NOTE]
> Sectigo esegue più istanze di Sectigo Certificate Manager. L'istanza principale di Sectigo Certificate Manager è **https:\//cert-manager.com** e questo è l'URL usato in questa esercitazione.  Se l'account usato si trova in un'istanza diversa, è necessario modificare gli URL di conseguenza.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato Sectigo Certificate Manager con Azure AD.

Sectigo Certificate Manager supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da provider di servizi**
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

| Attività | Descrizione |
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

1. Nella sezione **Configurazione SAML di base** completare la procedura seguente:

    1. Nella casella **Identificatore (ID entità)** immettere **https:\//cert-manager.com/shibboleth** per l'istanza principale di Sectigo Certificate Manager.

    1. Nella casella **URL di risposta** immettere **https:\//cert-manager.com/Shibboleth.sso/SAML2/POST** per l'istanza principale di Sectigo Certificate Manager.
        
    > [!NOTE]
    > Anche se in generale il valore di **URL di accesso** è obbligatorio per la *modalità avviata da SP*, non è necessario eseguire l'accesso da Sectigo Certificate Manager.        

1. Facoltativamente, nella sezione **Configurazione SAML di base** completare la procedura seguente per configurare la *modalità avviata da IDP* e consentire il funzionamento di **Test**:

    1. Selezionare **Imposta URL aggiuntivi**.

    1. Nella casella **Stato dell'inoltro** immettere l'URL specifico del cliente di Sectigo Certificate Manager. Per l'istanza principale di Sectigo Certificate Manager immettere **https:\//cert-manager.com/customer/\<customerURI\>/idp**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Sectigo Certificate Manager](common/idp-relay.png)

1. Nella sezione **Attributi utente e attestazioni** completare la procedura seguente:

    1. Eliminare tutte le **attestazioni aggiuntive**.
    
    1. Selezionare **Aggiungi nuova attestazione** e aggiungere le quattro attestazioni seguenti:
    
        | Nome | Spazio dei nomi | Source (Sorgente) | Attributo di origine | Descrizione |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | Attributo | user.userprincipalname | Deve corrispondere al campo **IdP person ID** (ID persona IdP) in Sectigo Certificate Manager for Admins. |
        | mail | empty | Attributo | user.mail | Obbligatoria |
        | givenName | empty | Attributo | user.givenname | Facoltativo |
        | sn | empty | Attributo | user.surname | Facoltativo |

       ![Sectigo Certificate Manager - Aggiunta di quattro nuove attestazioni](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. Nella sezione **Certificato di firma SAML** selezionare **Download** accanto a **XML metadati federazione**. Salvare il file XML nel computer.

    ![Opzione per il download del file XML dei metadati federazione](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurare l'accesso Single Sign-On di Sectigo Certificate Manager

Per configurare l'accesso Single Sign-On sul lato Sectigo Certificate Manager, inviare il file XML dei metadati della federazione scaricato al [team di supporto di Sectigo Certificate Manager](https://sectigo.com/support). Il team di supporto di Sectigo Certificate Manager usa le informazioni inviate per assicurarsi che la connessione Single Sign-On SAML venga impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Opzioni Utenti e Tutti gli utenti](common/users.png)

1. Selezionare **Nuovo utente**.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** completare la procedura seguente:

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **brittasimon\@\<your-company-domain>.\<extension\>** . Ad esempio, **brittasimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a Sectigo Certificate Manager in modo che l'utente possa usare l'accesso Single Sign-On di Azure.

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

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Eseguire il test da Sectigo Certificate Manager (accesso Single Sign-On avviato da SP)

Aprire l'URL specifico del cliente, che per l'istanza principale di Sectigo Certificate Manager è https:\//cert-manager.com/customer/\<customerURI\>/ e selezionare il pulsante sotto **Or Sign In With** (Oppure eseguire l'accesso con).  Se la configurazione è corretta, l'accesso a Sectigo Certificate Manager verrà eseguito automaticamente.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Eseguire il test dalla configurazione dell'accesso Single Sign-On (accesso Single Sign-On avviato da IDP)

Nel riquadro di integrazione dell'applicazione **Sectigo Certificate Manager** selezionare **Single Sign-On** e selezionare il pulsante **Test**.  Se la configurazione è corretta, l'accesso a Sectigo Certificate Manager verrà eseguito automaticamente.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Eseguire il test con il portale App personali (accesso Single Sign-On avviato da IDP)

Selezionare **Sectigo Certificate Manager** nel portale App personali.  Se la configurazione è corretta, l'accesso a Sectigo Certificate Manager verrà eseguito automaticamente. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


