---
title: 'Esercitazione: Integrazione di Azure Active Directory con Marketo | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: fc98201ac97409edd9be8522532728d7bb9b15af
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927278"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Esercitazione: Integrazione di Azure Active Directory con Marketo

Questa esercitazione descrive come integrare Marketo con Azure Active Directory (Azure AD).
L'integrazione di Marketo con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Marketo.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Marketo con i loro account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Marketo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Marketo abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Marketo supporta l'accesso SSO avviato da **IDP**

## <a name="adding-marketo-from-the-gallery"></a>Aggiunta di Marketo dalla raccolta

Per configurare l'integrazione di Marketo in Azure AD, è necessario aggiungere Marketo dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Marketo** nella casella di ricerca.
1. Selezionare **Marketo** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Marketo usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Marketo.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Marketo, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso SSO di Azure AD con Britta Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare l'utente Britta Simon all'uso dell'accesso SSO di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Marketo](#configure-marketo-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di Marketo](#create-marketo-test-user)** : per avere una controparte di Britta Simon in Marketo collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Marketo** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://saml.marketo.com/sp`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. Nella casella di testo **Stato dell'inoltro** digitare un URL nel formato seguente: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta e lo stato dell'inoltro effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Marketo](https://investors.marketo.com/contactus.cfm). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Marketo** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Marketo.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni, selezionare **Marketo**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-marketo-sso"></a>Configurare l'accesso Single Sign-On di Marketo

1. Per ottenere l'ID Munchkin dell'applicazione, accedere a Marketo usando le credenziali di amministratore ed eseguire le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configurare l'accesso Single Sign-On - 1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic sul **collegamento Munchkin**.
   
    ![Configurare l'accesso Single Sign-On - 2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiare l'ID Munchkin visualizzato sullo schermo e completare il valore per l'URL di risposta nella configurazione guidata di Azure AD.
   
    ![Configurare l'accesso Single Sign-On - 3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Per configurare l'accesso Single Sign-On nell'applicazione, seguire questa procedura:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configurare l'accesso Single Sign-On - 4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic su **Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On - 5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Per abilitare le impostazioni SAML fare clic sul pulsante **Edit** (Modifica).
   
    ![Configura accesso Single Sign-On6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Impostazioni dell'accesso Single Sign-On **abilitate**.
   
    f. Incollare l'**identificatore di Azure AD** nella casella di testo **Issuer ID** (ID emittente).
   
    g. Nella casella di testo **Entity ID** (ID entità) immettere l'URL come `http://saml.marketo.com/sp`.
   
    h. Selezionare User ID Location (Percorso ID utente) come **elemento Name Identifier** (Identificatore nome).
   
    ![Configurare l'accesso Single Sign-On - 7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se l'identificatore utente non è un valore UPN, modificarlo nella scheda degli attributi.
   
    i. Caricare il certificato scaricato dalla configurazione guidata di Azure AD. Fare clic su **Save** (Salva) per salvare le impostazioni.
   
    j. Modificare le impostazioni delle pagine di reindirizzamento.
   
    k. Incollare l'**URL di accesso** nella casella **Login URL** (URL di accesso).
   
    l. Incollare l'**URL di disconnessione** nella casella **Logout URL** (URL di disconnessione).
   
    m. In **Error URL** (URL errore) copiare l'**URL istanza** di Marketo e fare clic su **Save** (Salva) per salvare le impostazioni.
   
    ![Configurare l'accesso Single Sign-On - 8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Per abilitare l'accesso Single Sign-On per gli utenti, completare le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configurare l'accesso Single Sign-On - 9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu **Security** (Sicurezza) e fare clic su **Login Settings** (Impostazioni di accesso).
   
    ![Configurare l'accesso Single Sign-On - 10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Selezionare l'opzione **Require SSO** (Richiedi SSO) e usare **Save** (Salva) per salvare le impostazioni.
   
    ![Configurare l'accesso Single Sign-On - 11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Creare un utente di test di Marketo

In questa sezione viene creato un utente chiamato Britta Simon in Marketo. Seguire questa procedura per creare un utente nella piattaforma Marketo.

1. Accedere all'app Marketo usando le credenziali di amministratore.

2. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Utente di test - 1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Passare al menu **Security** (Sicurezza) e fare clic su **Users & Roles** (Utenti e ruoli).
   
    ![Utente di test - 2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Fare clic sul collegamento **Invite New User** (Invita nuovo utente) nella scheda Users (Utenti).
   
    ![Utente di test - 3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. La procedura guidata (Invita nuovo utente) immetterà i valori per le informazioni seguenti.
   
    a. Immettere l'indirizzo di **Email** (Posta elettronica) dell'utente nella casella di testo.
   
    ![Utente di test - 4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Immettere il valore per **First Name** (Nome) nella casella di testo.
   
    c. Immettere il valore per **Last Name** (Cognome) nella casella di testo.
   
    d. Scegliere **Avanti**

6. Nella scheda **Permissions** (Autorizzazioni) selezionare i **ruoli utente** e fare clic su **Next** (Avanti).
   
    ![Utente di test - 5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Fare clic sul pulsante **Send** (Invia) per inoltrare l'invito all'utente.
   
    ![Utente di test - 6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. L'utente riceve la notifica tramite posta elettronica e deve fare clic sul collegamento e modificare la password per attivare l'account. 

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

1. Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Marketo per cui si è configurato l'accesso SSO

1. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Marketo nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Marketo per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Marketo, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

