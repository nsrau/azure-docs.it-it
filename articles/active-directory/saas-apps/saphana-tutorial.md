---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 017d693f0d71692abfb432216ca0645dee80d7df
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865230"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA

Questa esercitazione descrive come integrare SAP HANA con Azure Active Directory (Azure AD).
L'integrazione di SAP HANA con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere a SAP HANA.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAP HANA con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP HANA, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On (SSO)
- Istanza di HANA in esecuzione in qualsiasi VM IaaS pubblica, locale o di Azure oppure in istanze di grandi dimensioni di SAP in Azure
- Interfaccia Web di amministrazione di XSA e HANA Studio installati nell'istanza di HANA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione SAP HANA per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

A questo scopo, seguire queste indicazioni:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP HANA supporta l'accesso SSO avviato da **IdP**
* SAP HANA supporta il provisioning **Just-In-Time** (JIT) degli utenti

## <a name="adding-sap-hana-from-the-gallery"></a>Aggiunta di SAP HANA dalla raccolta

Per configurare l'integrazione di SAP HANA in Azure AD, è necessario aggiungere SAP HANA dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP HANA dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP HANA**, selezionare **SAP HANA** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SAP HANA nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP HANA usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP HANA.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP HANA, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP HANA](#configure-sap-hana-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SAP HANA](#create-sap-hana-test-user)**: per avere una controparte di Britta Simon in SAP HANA collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP HANA, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SAP HANA** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP HANA](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare il valore seguente: `HA100`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP HANA](https://cloudplatform.sap.com/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione SAP HANA prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Attributi utente e attestazioni** eseguire questa procedura:
 
    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Nell'elenco **Trasformazione** selezionare **ExtractMailPrefix()**.

    c. Nell'elenco **Parametro 1** selezionare **user.mail**.

    d. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Configurare l'accesso Single Sign-On di SAP HANA

1. Per configurare l'accesso Single Sign-On sul lato SAP HANA, accedere alla **console Web HANA XSA** passando al relativo endpoint HTTPS.

    > [!NOTE]
    > Nella configurazione predefinita l'URL reindirizza la richiesta a una schermata di accesso, che richiede le credenziali di un utente del database SAP HANA autenticato. L'utente che effettua l'accesso deve disporre delle autorizzazioni per eseguire attività di amministrazione di SAML.

2. Nell'interfaccia Web XSA passare a **SAML Identity Provider** (Provider di identità SAML). A questo punto selezionare il pulsante **+** nella parte inferiore della schermata per visualizzare il riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità). Seguire quindi questa procedura:

    ![Aggiungi provider di identità](./media/saphana-tutorial/sap1.png)

    a. Nel riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità) incollare i contenuti del file XML metadati scaricato dal portale di Azure nella casella **Metadata** (Metadati).

    ![Impostazioni di aggiunta del provider di identità](./media/saphana-tutorial/sap2.png)

    b. Se il contenuto del documento XML è valido, il processo di analisi estrae le informazioni necessarie per i campi **Subject, Entity ID, and Issuer** (Oggetto, ID entità e Autorità di certificazione) nell'area dello schermo **General data** (Dati generali). Vengono estratte anche le informazioni necessarie per i campi relativi agli URL nell'area dello schermo **Destination** (Destinazione), ad esempio i campi **Base URL e SingleSignOn URL (*)** (URL di base e URL Single Sign-On).

    ![Impostazioni di aggiunta del provider di identità](./media/saphana-tutorial/sap3.png)

    c. Nella casella **Name** (Nome) dell'area dello schermo **General Data** (Dati generali) immettere un nome per il nuovo provider di identità SSO SAML.

    > [!NOTE]
    > Il nome del provider di identità SAML è obbligatorio e deve essere univoco. Viene visualizzato nell'elenco di provider di identità SAML disponibili che viene visualizzato quando si seleziona SAML come metodo di autenticazione per le applicazioni SAP HANA XS da usare. Ad esempio, è possibile eseguire questa procedura nell'area dello schermo **Authentication** (Autenticazione) dello strumento di amministrazione di elementi XS.

3. Selezionare **Save** (Salva) per salvare i dettagli del provider di identità SAML e aggiungere il nuovo provider di identità SAML all'elenco di provider noti.

    ![Pulsante per il salvataggio](./media/saphana-tutorial/sap4.png)

4. In Studio HANA, nella scheda **Configuration** (Configurazione) filtrare le impostazioni all'interno delle proprietà del sistema in base a **saml**. Modificare quindi il valore del parametro **assertion_timeout** da **10 sec** a **120 sec**.

    ![Impostazione assertion_timeout](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP HANA.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP HANA**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **SAP HANA**.

    ![Collegamento a SAP HANA nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-hana-test-user"></a>Creare un utente di test di SAP HANA

Per consentire agli utenti di Azure AD di accedere a SAP HANA, è necessario effettuare il provisioning di tali utenti in SAP HANA.
SAP HANA supporta il **provisioning JIT**, che è abilitato per impostazione predefinita.

Per creare un utente manualmente, seguire questa procedura:

>[!NOTE]
>È possibile modificare l'autenticazione esterna usata dagli utenti. Questi possono autenticarsi con un sistema esterno, ad esempio Kerberos. Per informazioni dettagliate sulle identità esterne, contattare l'[amministratore di dominio](https://cloudplatform.sap.com/contact.html).

1. Aprire [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) come amministratore e quindi abilitare DB-User per l'accesso SSO SAML.

    ![Create user](./media/saphana-tutorial/sap5.png)

2. Selezionare la casella di controllo invisibile a sinistra di **SAML** e quindi selezionare il collegamento **Configura**.

3. Selezionare **Aggiungi** per aggiungere il provider di identità SAML.  Selezionare il provider di identità SAML appropriato e quindi **OK**.

4. Aggiungere un valore nel campo **External Identity** (Identità esterna), in questo caso BrittaSimon, oppure scegliere **Any** (Qualsiasi). Selezionare **OK**.

    >[!Note]
    >Se la casella di controllo **Any** (Qualsiasi) non è selezionata, il nome utente in HANA deve corrispondere esattamente al nome dell'utente nell'UPN prima del suffisso di dominio (ad esempio BrittaSimon@contoso.com diventerebbe BrittaSimon in HANA).

5. A scopo di test, assegnare tutti i ruoli **XS** all'utente.

    ![Assegnazione di ruoli](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > È consigliabile assegnare solo le autorizzazioni appropriate per i casi d'uso.

6. Salvare l'utente.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP HANA nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP HANA per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

