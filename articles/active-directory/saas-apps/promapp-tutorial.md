---
title: 'Esercitazione: Integrazione di Azure Active Directory con Promapp | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e91351d4571eaa084865c5a179ed05e6c773b952
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240420"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Esercitazione: integrazione di Azure Active Directory con Promapp

Questa esercitazione descrive come integrare Promapp con Azure Active Directory (Azure AD).
Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a Promapp.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Promapp con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Promapp, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile iscriversi per ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Promapp abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Promapp supporta l'accesso SSO avviato da SP e IdP.

* Promapp supporta il provisioning utenti just-in-time.

## <a name="add-promapp-from-the-gallery"></a>Aggiungere Promapp dalla raccolta

Per configurare l'integrazione di Promapp in Azure AD, è necessario aggiungere Promapp dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Promapp**. Selezionare **Promapp** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Promapp usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Promapp.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Promapp, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Promapp](#configure-promapp-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Promapp, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione Promapp del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** completare la seguente procedura se si vuole configurare l'applicazione nella modalità avviata da IdP.

    ![Finestra di dialogo Configurazione SAML di base](common/idp-intiated.png)

    1. Nella casella **Identificatore** immettere un URL nel formato seguente:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > L'integrazione di Azure AD con Promapp è configurata solo per l'autenticazione avviata dal servizio, vale a dire che, passando a un URL di Promapp, viene avviato il processo di autenticazione, ma il campo **URL di risposta** è un campo obbligatorio.

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi**. Nella casella **URL di accesso** immettere un URL nel formato seguente:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Promapp](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Questi valori sono segnaposto. È necessario usare l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere i valori, contattare il [team di supporto di Promapp](https://www.promapp.com/about-us/contact-us/). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **Certificato (Base64)** , in base alle esigenze, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Promapp** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-promapp-single-sign-on"></a>Configurare l'accesso Single Sign-On di Promapp

1. Accedere al sito aziendale di Promapp come amministratore.

2. Nel menu nella parte superiore della finestra selezionare **Admin** (Amministratore):
   
    ![Selezionare Admin (Amministratore)][12]

3. Selezionare **Configure** (Configura):
   
    ![Selezionare Configure (Configura)][13]

4. Nella finestra di dialogo **Security** (Sicurezza) seguire questa procedura.
   
    ![Finestra di dialogo Security (Sicurezza)][14]
    
    1. Incollare l'**URL di accesso** copiato dal portale di Azure nella casella **SSO-Login URL** (URL di accesso SSO).
    
    1. Nell'elenco **SSO - Single Sign-on Mode** (SSO - Modalità Single Sign-On) selezionare **Optional** (Facoltativo). Selezionare **Salva**.

       > [!NOTE]
       > La modalità facoltativa serve solo a scopo di test. Quando si è soddisfatti della configurazione, selezionare **Required** (Obbligatorio) nell'elenco **SSO - Single Sign-on Mode** (SSO - Modalità Single Sign-On) per imporre a tutti gli utenti l'autenticazione con Azure AD.

    1. Nel Blocco note aprire il certificato scaricato nella sezione precedente. Copiare il contenuto del certificato senza la prima riga ( **-----BEGIN CERTIFICATE-----** ) o l'ultima riga ( **-----END CERTIFICATE-----** ). Incollare il contenuto del certificato nella casella **SSO-x.509 Certificate** (SSO - Certificato x.509) e quindi selezionare **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Promapp.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Promapp**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Promapp**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="just-in-time-user-provisioning"></a>Provisioning JIT

Promapp supporta il provisioning utenti just-in-time. Questa funzionalità è abilitata per impostazione predefinita. Se non esiste già un utente in Promapp, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Promapp nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Promapp per cui si è configurato l'accesso SSO. Per altre informazioni sul Pannello di accesso, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
