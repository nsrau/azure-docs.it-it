---
title: 'Esercitazione: Integrazione di Azure Active Directory con BambooHR | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 8025728ffc40aca27807068eff29f5a889a8d76e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331397"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Esercitazione: Integrazione di Azure Active Directory con BambooHR

Questa esercitazione descrive come integrare BambooHR con Azure Active Directory (Azure AD). Integrando BambooHR con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a BambooHR.
* Abilitare gli utenti per l'accesso automatico a BambooHR con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di BambooHR abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* BambooHR supporta l'accesso Single Sign-On avviato da **provider di servizi**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-bamboohr-from-the-gallery"></a>Aggiunta di BambooHR dalla raccolta

Per configurare l'integrazione di BambooHR in Azure AD, è necessario aggiungere BambooHR dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **BambooHR** nella casella di ricerca.
1. Selezionare **BambooHR** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Configurare e testare l'accesso SSO di Azure AD per BambooHR

Configurare e testare l'accesso SSO di Azure AD con BambooHR usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BambooHR.

Per configurare e testare l'accesso SSO di Azure AD con BambooHR, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di BambooHR](#configure-bamboohr-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di BambooHR](#create-bamboohr-test-user)**: per avere una controparte di Britta Simon in BambooHR collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **BambooHR** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<company>.bamboohr.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | URL di risposta |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso Single Sign-On effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di BambooHR](https://www.bamboohr.com/contact.php). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up BambooHR** (Configura BambooHR) copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BambooHR.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **BambooHR**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-bamboohr-sso"></a>Configurare l'accesso Single Sign-On di BambooHR

1. In una nuova finestra accedere al sito aziendale di BambooHR come amministratore.

2. Nella home page seguire questa procedura:
   
    ![Pagina Single Sign-On di BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. Selezionare **App**.
   
    b. Nel riquadro **Apps** (App) selezionare **Single Sign-On**.
   
    c. Selezionare **SAML Single Sign-On** (Single Sign-On SAML).

3. Nel riquadro **SAML Single Sign-On** (Single Sign-On SAML) seguire questa procedura:
   
    ![Riquadro Single Sign-On SAML](./media/bamboo-hr-tutorial/IC796692.png "Single Sign-On SAML")
   
    a. Nella casella **SSO Login URL** (URL di accesso SSO) incollare l'**URL di accesso** copiato dal portale di Azure nel passaggio 6.
      
    b. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509).
   
    c. Selezionare **Salva**.

### <a name="create-bamboohr-test-user"></a>Creare un utente di test di BambooHR

Per consentire agli utenti di Azure AD di accedere a BambooHR, impostarli manualmente in BambooHR seguendo questa procedura:

1. Accedere al sito di **BambooHR** come amministratore.

2. Nella barra degli strumenti superiore selezionare **Impostazioni**.
   
    ![Pulsante Settings (Impostazioni)](./media/bamboo-hr-tutorial/IC796694.png "Impostazione")

3. Selezionare **Panoramica**.

4. Nel riquadro di sinistra selezionare **Sicurezza** > **Utenti**.

5. Digitare il nome utente, la password e l'indirizzo di posta elettronica dell'account di Azure AD valido che si vuole configurare.

6. Selezionare **Salva**.
        
>[!NOTE]
>Per configurare account utente di Azure AD è possibile anche usare le API o gli strumenti di creazione di account utente di BambooHR.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di BambooHR, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di BambooHR e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di BambooHR nel pannello di accesso, si verrà reindirizzati all'URL di accesso di BambooHR. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato BambooHR, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).