---
title: 'Esercitazione: Integrazione di Azure Active Directory con HubSpot | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d74cc1665867568032bb1343e4f2c26c50fe15a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65770183"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Esercitazione: Integrazione di Azure Active Directory con HubSpot

Questa esercitazione illustra come integrare HubSpot con Azure Active Directory (Azure AD).

L'integrazione di HubSpot con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere ad HubSpot.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a HubSpot con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con HubSpot, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a HubSpot con accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato HubSpot con Azure AD.

HubSpot supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da provider di servizi**
* **Accesso Single Sign-On avviato da IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Aggiungere HubSpot nel portale di Azure

Per integrare HubSpot con Azure AD, è necessario aggiungere HubSpot all'elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **HubSpot**. Nei risultati della ricerca selezionare **HubSpot** e quindi selezionare **Aggiungi**.

    ![HubSpot nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con HubSpot usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HubSpot.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HubSpot, è necessario completare le procedure di base seguenti:

| Attività | DESCRIZIONE |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Abilitare gli utenti all'uso di questa funzionalità. |
| **[Configurare l'accesso Single Sign-On per HubSpot](#configure-hubspot-single-sign-on)** | Configurare le impostazioni dell'accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Testare l'accesso Single Sign-On di Azure AD per un utente di nome Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD. |
| **[Creare un utente di test di HubSpot](#create-a-hubspot-test-user)** | Crea una controparte di Britta Simon in HubSpot collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD con HubSpot nel portale di Azure.

1. Nel riquadro di integrazione dell'applicazione **HubSpot** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** o **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** seguire questa procedura per configurare la *modalità avviata da IDP*:

    1. Nella casella **Identificatore** immettere un URL con il modello seguente: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<ID cliente\>.

    1. Nella casella **URL di risposta** immettere un URL con il modello seguente: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<ID cliente\>.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Per formattare gli URL, è anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Per configurare l'applicazione in *modalità avviata da SP*:

    1. Selezionare **Impostare URL aggiuntivi**.

    1. Nella casella **URL di accesso** immettere **https:\//app.hubspot.com/login**.

    ![Opzione Impostare URL aggiuntivi](common/metadata-upload-additional-signon.png)

1. Nella sezione **Certificato di firma SAML** nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** accanto a **Certificato (Base64)** . Selezionare un'opzione di download in base ai requisiti. Salvare il certificato nel computer.

    ![Opzione di download Certificato (Base64)](common/certificatebase64.png)

1. Nella sezione **Configura HubSpot** copiare gli URL seguenti in base ai requisiti:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurare l'accesso Single Sign-On per HubSpot

1. Aprire una nuova scheda del browser e accedere all'account di amministratore di HubSpot.

1. Selezionare l'icona **Impostazioni** nell'angolo superiore destro della pagina.

    ![L'icona Impostazioni in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selezionare **Account Defaults** (Impostazioni predefinite account).

    ![Opzione Account Defaults (Impostazioni predefinite account) in HubSpot](./media/hubspot-tutorial/config2.png)

1. Scorrere verso il basso fino alla sezione **Security** (Sicurezza) e quindi selezionare **Set up** (Configura).

    ![Opzione Set up (Configura) in HubSpot](./media/hubspot-tutorial/config3.png)

1. Nella sezione **Set Up Single Sign-On** (Configura Single Sign-On) seguire questa procedura:

    1. Nella casella **Audience URl (Service Provider Entity ID)** (URI destinatari - ID entità provider di servizio) selezionare **Copia** per copiare il valore. Nel riquadro **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **Identificatore**.

    1. Nella casella **Sign on URl, ACS, Recipient, or Redirect** (URI di accesso, ACS, destinatario o reindirizzamento) selezionare **Copia** per copiare il valore. Nel riquadro **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **URL di risposta**.

    1. In HubSpot, nella casella **Identity Provider Identifier or Issuer URL** (Identificatore provider identità o URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. In HubSpot, nella casella **Identity Provider Single Sign-On URL** (URL Single Sign-On provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nel Blocco note di Windows aprire il file del certificato (Base64) scaricato. Selezionare e copiare il contenuto del file. Incollarlo quindi in HubSpot nella casella  **X.509 Certificate** (Certificato X.509).

    1. Selezionare **Verifica**.

        ![Sezione Set Up Single Sign-On (Configura Single Sign-On) in HubSpot](./media/hubspot-tutorial/config4.png)

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

    1. Selezionare **Create**.

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a HubSpot in modo che possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **HubSpot**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco di applicazioni selezionare **HubSpot**.

    ![HubSpot nell'elenco delle applicazioni](common/all-applications.png)

1. Nel menu selezionare **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona**.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-hubspot-test-user"></a>Creare un utente di test di HubSpot

Per consentire agli utenti di Azure AD di accedere a HubSpot, è necessario effettuarne il provisioning in HubSpot. Nel caso di HubSpot il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente in HubSpot:

1. Accedere al sito aziendale di HubSpot come amministratore.

1. Selezionare l'icona **Impostazioni** nell'angolo superiore destro della pagina.

    ![L'icona Impostazioni in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selezionare **Users & Teams** (Utenti e team).

    ![Opzione Users & Teams (Utenti e team) in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selezionare **Create user** (Crea utente).

    ![Opzione Create user (Crea utente) in HubSpot](./media/hubspot-tutorial/user2.png)

1. Nella casella **Add email addess(es)** (Aggiungi indirizzi di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente nel formato brittasimon\@contoso.com e quindi selezionare **Avanti**.

    ![Casella Add email addess(es) (Aggiungi indirizzi di posta elettronica) nella sezione Create users (Crea utenti) in HubSpot](./media/hubspot-tutorial/user3.png)

1. Nella sezione **Create users (Crea utenti)** selezionare ogni scheda. In ogni scheda impostare le opzioni e le autorizzazioni appropriate per l'utente. Quindi selezionare **Avanti**.

    ![Schede nella sezione Create users (Crea utenti) in HubSpot](./media/hubspot-tutorial/user4.png)

1. Fare clic su **Send** (Invia) per inviare l'invito all'utente.

    ![L'opzione Send (Invia) in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > L'utente viene attivato dopo aver accettato l'invito.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Dopo aver configurato l'accesso Single Sign-On, quando si seleziona **HubSpot** nel portale App personali, viene eseguito automaticamente l'accesso a HubSpot. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
