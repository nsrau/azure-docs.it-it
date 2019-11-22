---
title: 'Esercitazione: Integrazione di Azure Active Directory con Shmoop For Schools | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ce2e51f72b9b4f709bf26ce6d92ad3861d36db
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081652"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Esercitazione: Integrare Shmoop For Schools con Azure Active Directory

Questa esercitazione descrive come integrare Shmoop For Schools con Azure Active Directory (Azure AD). Integrando Shmoop For Schools con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Shmoop For Schools.
* Abilitare gli utenti per l'accesso automatico a Shmoop For Schools con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Shmoop For Schools abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Shmoop For Schools supporta l'accesso Single Sign-On avviato da **SP**
* Shmoop For Schools supporta il provisioning utenti **JIT**

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Aggiunta di Shmoop For Schools dalla raccolta

Per configurare l'integrazione di Shmoop For Schools in Azure AD, è necessario aggiungere Shmoop For Schools dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Shmoop For Schools** nella casella di ricerca.
1. Selezionare **Shmoop For Schools** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Shmoop For Schools

Configurare e testare l'accesso SSO di Azure AD con Shmoop For Schools usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Shmoop For Schools.

Per configurare e testare l'accesso SSO di Azure AD con Shmoop For Schools, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Shmoop For Schools](#configure-shmoop-for-schools-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Shmoop For Schools](#create-shmoop-for-schools-test-user)** : per avere una controparte di B.Simon in Shmoop For Schools collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Shmoop For Schools** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Shmoop For Schools](mailto:support@shmoop.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Shmoop For Schools prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

    > [!NOTE]
    > Shmoop For Schools supporta due ruoli per gli utenti: **Teacher** (Insegnante) e **Student** (Studente). Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Oltre quelli elencati in precedenza, l'applicazione Shmoop For Schools prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME |  Attributo di origine|
    | --------- | --------------- |
    | role      | user.assignedroles |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Shmoop For Schools.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Shmoop For Schools**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-shmoop-for-schools-sso"></a>Configurare l'accesso Single Sign-On di Shmoop For Schools

Per configurare l'accesso Single Sign-On sul lato **Shmoop For Schools**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di Shmoop For Schools](mailto:support@shmoop.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-shmoop-for-schools-test-user"></a>Creare l'utente di test di Shmoop For Schools

In questa sezione viene creato un utente di nome B.Simon in Shmoop For Schools. Shmoop For Schools supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Shmoop For Schools, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Shmoop For Schools](mailto:support@shmoop.com).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Shmoop For Schools nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Shmoop For Schools per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Shmoop For Schools con Azure AD](https://aad.portal.azure.com/)