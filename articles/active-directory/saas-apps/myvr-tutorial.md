---
title: 'Esercitazione: Integrazione di Azure Active Directory con MyVR | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MyVR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b1b4bbc7-cdac-447c-83a3-bcb869159255
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12d76c8079cf904e09a020a3eb242042ffe9e544
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245143"
---
# <a name="tutorial-integrate-myvr-with-azure-active-directory"></a>Esercitazione: Integrare MyVR con Azure Active Directory

Questa esercitazione descrive come integrare MyVR con Azure Active Directory (Azure AD). Integrando MyVR con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a MyVR.
* Abilitare gli utenti per l'accesso automatico a MyVR con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di MyVR abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

* MyVR supporta l'accesso SSO avviato da **SP e IDP**.
* MyVR supporta il provisioning utenti **JIT**.

## <a name="adding-myvr-from-the-gallery"></a>Aggiunta di MyVR dalla raccolta

Per configurare l'integrazione di MyVR in Azure AD, è necessario aggiungere MyVR dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **MyVR** nella casella di ricerca.
1. Selezionare **MyVR** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con MyVR usando un utente di test di nome **B. Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MyVR.

Per configurare e testare l'accesso SSO di Azure AD con MyVR, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
1. **[Configurare l'accesso Single Sign-On di MyVR](#configure-myvr-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare l'utente di test di MyVR](#create-myvr-test-user)** : per avere una controparte di Britta Simon in MyVR collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **MyVR** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, per configurare l'applicazione in modalità avviata da **IDP**, l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

   Nella casella di testo **URL di accesso** digitare l'URL: `https://ess.virtualroster.net/ess/login.aspx`

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

   | NOME | Attributo di origine|
   | ---------------| --------------- |
   | givenname | user.givenname |
   | surname | user.surname |
   | emailaddress | user.mail |
   | employeeid | user.employeeid |

   1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.
   1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
   1. Lasciare vuota la casella **Spazio dei nomi**.
   1. Per Origine selezionare **Attributo**.
   1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.
   1. Fare clic su **OK**.
   1. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura MyVR** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-myvr-sso"></a>Configurare l'accesso SSO di MyVR

Per configurare l'accesso Single Sign-On sul lato **MyVR**, è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di MyVR](mailto:arno.vandenberg@Kronos.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B. Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MyVR.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **MyVR**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-myvr-test-user"></a>Creare l'utente di test di MyVR

In questa sezione viene creato un utente di nome B. Simon in MyVR. MyVR supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in MyVR, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro MyVR nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di MyVR per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
