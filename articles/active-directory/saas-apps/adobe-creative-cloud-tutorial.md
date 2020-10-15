---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Creative Cloud | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 48009246dd491ca3f4080cf85036c66605a73272
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706993"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Creative Cloud

> [!NOTE]
> Questo articolo descrive la configurazione personalizzata basata su SAML di Adobe Admin Console per Azure Active Directory (Azure AD). Per le nuove configurazioni, si consiglia di usare il [connettore di Azure AD](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html). Il connettore di Azure AD può essere configurato in pochi minuti e consente di velocizzare il processo di attestazione del dominio, configurazione dell'accesso Single Sign-On e sincronizzazione degli utenti.

Questa esercitazione descrive come integrare Adobe Creative Cloud con Azure Active Directory (Azure AD). Integrando Adobe Creative Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Adobe Creative Cloud.
* Abilitare gli utenti per l'accesso automatico ad Adobe Creative Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Creative Cloud abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Creative Cloud supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Adobe Creative Cloud, è possibile applicare il controllo della sessione, che consente di proteggere l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Aggiungere Adobe Creative Cloud dalla raccolta

Per configurare l'integrazione di Adobe Creative Cloud in Azure AD, è necessario aggiungere Adobe Creative Cloud dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adobe Creative Cloud** nella casella di ricerca.
1. Selezionare **Adobe Creative Cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Configurare e testare l'accesso SSO di Azure AD per Adobe Creative Cloud

Configurare e testare l'accesso SSO di Azure AD con Adobe Creative Cloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Creative Cloud.

Per configurare e testare l'accesso SSO di Azure AD con Adobe Creative Cloud, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** : per avere una controparte di B.Simon in Adobe Creative Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Adobe Creative Cloud** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL `https://adobe.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. Seguire le istruzioni riportate nel passaggio 4 della sezione che descrive come **configurare l'accesso SSO per Adobe Cloud**. Durante queste operazioni è possibile aprire il **file con estensione xml dei metadati di federazione** e ottenere il valore dell'ID entità, che potrà quindi essere usato come valore identificatore nella configurazione di Azure AD. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Adobe Creative Cloud prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Adobe Creative Cloud prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Affinché il valore di attestazione della posta elettronica venga inserito nella risposta SAML, gli utenti devono avere una licenza di Microsoft 365 ExO valida.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei dati di federazione** e quindi selezionare **Scarica** per scaricare il file dei metadati XML e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Adobe Creative Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Creative Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Adobe Creative Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configurare l'accesso Single Sign-On di Adobe Creative Cloud

1. In un'altra finestra del Web browser accedere ad [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore di sistema.

1. Passare a **Settings** (Impostazioni) nella barra di spostamento in alto e quindi scegliere **Identity** (Identità). Viene visualizzato l'elenco delle directory. Selezionare la directory federata desiderata.

1. Nella pagina **Directory Details** (Dettagli directory) selezionare **Configure** (Configura).

1. Copiare l'ID entità e l'URL del servizio consumer di asserzione o l'URL di risposta. Immettere gli URL nei campi appropriati del portale di Azure.

    ![Configurare l'accesso Single Sign-On sul lato app](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Usare il valore dell'ID entità fornito da Adobe per l'**identificatore** nella finestra di dialogo **Configurare le impostazioni dell'app**.

    b. Usare il valore l'URL del servizio consumer di asserzione fornito da Adobe per l'**URL di risposta** nella finestra di dialogo **Configurare le impostazioni dell'app**.

1. Nella parte inferiore della pagina caricare il file **XML dei dati di federazione** scaricato dal portale di Azure. 

    ![File XML dei dati di federazione](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML dei metadati IdP")

1. Selezionare **Salva**.


### <a name="create-adobe-creative-cloud-test-user"></a>Creare un utente di test di Adobe Creative Cloud

Per consentire agli utenti di Azure AD di accedere ad Adobe Creative Cloud, è necessario effettuarne il provisioning in Adobe Creative Cloud. Nel caso di Adobe Creative Cloud il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore.

2. Aggiungere l'utente nella console di Adobe come ID federato e assegnarlo a un profilo di prodotto. Per informazioni dettagliate sull'aggiunta di utenti, vedere [Manage Users](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Gestire gli utenti). 

3. A questo punto, digitare l'indirizzo di posta elettronica/UPN nel modulo di registrazione, premere sulla scheda e si dovrebbe essere federati di nuovo in Azure AD:
   * Accesso Web: www\.adobe.com > sign-in (accedi)
   * All'interno dell'utilità dell'app del desktop > sign-in (accedi)
   * All'interno dell'applicazione > help (guida) > sign-in (accedi)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adobe Creative Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Creative Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Adobe Creative Cloud con Azure AD](https://aad.portal.azure.com/)

- [Configurare un'identità (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Configure Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html) (Configurare Azure per l'uso con Adobe SSO)
