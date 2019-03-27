---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e piattaforma SAP Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b345656b30a9bb182c097a4c9e18d71a293bf420
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852549"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform

Questa esercitazione descrive come integrare SAP Cloud Platform con Azure Active Directory (Azure AD).
L'integrazione di SAP Cloud Platform con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Cloud Platform.
* È possibile abilitare gli utenti per l'accesso automatico a SAP Cloud Platform (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP Cloud Platform abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SAP Cloud Platform saranno in grado di eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>È necessario distribuire l'applicazione o la sottoscrizione a un'applicazione per l'account di SAP Cloud Platform per testare l'accesso Single Sign-On. In questa esercitazione, viene distribuita un'applicazione nell'account.
> 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud Platform supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Aggiunta di SAP Cloud Platform dalla raccolta

Per configurare l'integrazione di SAP Cloud Platform in Azure AD, è necessario aggiungere SAP Cloud Platform dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP Cloud Platform dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP Cloud Platform**, selezionare **SAP Cloud Platform** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SAP Cloud Platform nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud Platform usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creazione di un utente di test di SAP Cloud Platform](#create-sap-cloud-platform-test-user)**: per avere una controparte di Britta Simon in SAP Cloud Platform collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SAP Cloud Platform** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione **SAP Cloud Platform**. Questo è l'URL specifico dell'account di una risorsa protetta dell'applicazione SAP Cloud Platform. L'URL è basato sul formato seguente: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Questo è l'URL dell'applicazione SAP Cloud Platform che richiede l'autenticazione dell'utente.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Nella casella di testo **Identifier** si fornirà il tipo di SAP Cloud Platform con un URL nel seguente formato: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto dei client di SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) per ottenere l'URL di accesso e l'identificatore. URL di risposta che è possibile ottenere dalla sezione di gestione trust, illustrata più avanti nell'esercitazione.
    > 
4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configurare l'accesso Single Sign-On di SAP Cloud Platform

1. In un'altra finestra del Web browser accedere al pannello di controllo di SAP Cloud Platform all'indirizzo `https://account.<landscape host>.ondemand.com/cockpit` (ad esempio https://account.hanatrial.ondemand.com/cockpit).

2. Scegliere la scheda **Trust** .
   
    ![Trust](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Trust")

3. Nella sezione Trust Management, in **Local Service Provider**, eseguire la procedura seguente:

    ![Gestione dell'attendibilità](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gestione dell'attendibilità")
   
    a. Fare clic su **Modifica**.

    b. In **Configuration Type** selezionare **Custom**.

    c. In **Local Provider Name**lasciare il valore predefinito. Copiare questo valore e incollarlo nel campo **Identifier** campo nella configurazione di Azure Active Directory per SAP Cloud Platform.

    d. Per generare una **chiave per la firma** e una coppia di chiavi del **certificato di firma**, fare clic su **Generate Key Pair**.

    e. In **Propagazione entità** selezionare **Disabilitato**.

    f. In **Force Authentication** selezionare **Disabled**.

    g. Fare clic su **Save**.

4. Dopo il salvataggio delle impostazioni **Local Service Provider**, eseguire le operazioni seguenti per ottenere l'URL di risposta:
   
    ![Ottenere i metadati](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Ottenere i metadati")

    a. Scaricare il file di metadati di SAP Cloud Platform facendo clic su **Get Metadata**.

    b. Aprire il file di metadati XML SAP Cloud piattaforma scaricato e quindi individuare il tag **ns3:AssertionConsumerService** .
 
    c. Copiare il valore dell'attributo **Percorso** e incollarlo nel campo **URL di risposta** nella configurazione di Azure Active Directory per SAP Cloud Platform.

5. Fare clic sulla scheda **Trusted Identity Provider**, quindi fare clic su **Add Trusted Identity Provider**.
   
    ![Gestione dell'attendibilità](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gestione dell'attendibilità")
   
    >[!NOTE]
    >Per gestire l'elenco dei provider di identità attendibili, è necessario aver scelto il tipo di configurazione Personalizza nella sezione Provider di servizi locale. Per il tipo di configurazione predefinito è presente una relazione di trust implicita e non modificabile per il servizio SAP ID. Per Nessuno, non sono disponibili impostazioni di trust.
    > 
    > 

6. Fare clic sulla scheda **General**, quindi fare clic su **Browse** per caricare il file di metadati scaricati.
    
    ![Gestione dell'attendibilità](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gestione dell'attendibilità")
    
    >[!NOTE]
    >Dopo aver caricato il file di metadati, i valori per **URL Single Sign-On**, **URL disconnessione singola** e **Certificato di firma** vengono popolati automaticamente.
    > 
     
7. Fare clic sulla scheda **Attributes** .

8. Nella scheda **Attributes** eseguire la procedura seguente:
    
    ![Attributi](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attributi") 

    a. Fare clic su **Add Assertion-Based Attribute** e quindi aggiungere gli attributi basati su asserzione seguenti:
       
    | Attributo di asserzione | Attributo di entità |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >La configurazione degli attributi dipende da come le applicazioni per SCP vengono sviluppate, vale a dire quali attributi sono previsti nella risposta SAML e con quale nome (attributo di entità) accedono a questo attributo nel codice.
     > 
    
    b. L'**attributo predefinito** presente nella schermata è solo a scopo illustrativo. Non è necessario per il funzionamento dello scenario.  
 
    c. I nomi e i valori per l'**attributo di entità** illustrati nella schermata dipendono dal modo in cui viene sviluppata l'applicazione. È possibile che l'applicazione richieda mapping diversi.

### <a name="assertion-based-groups"></a>Gruppi basati su asserzione

Come passaggio facoltativo, è possibile configurare gruppi basati su asserzione per il provider di identità Azure Active Directory.

L'uso dei gruppi in SAP Cloud Platform consente di assegnare dinamicamente uno o più utenti a uno o più ruoli nelle applicazioni SAP Cloud Platform, determinate dai valori degli attributi nell'asserzione SAML 2.0. 

Ad esempio, se l'asserzione contiene l'attributo "*contract=temporary*", si potrebbe volere che tutti gli utenti interessati siano aggiunti al gruppo"*TEMPORARY*". Il gruppo "*TEMPORARY*" può contenere uno o più ruoli da uno o più applicazioni distribuite nell'account di SAP Cloud Platform.
 
Usare i gruppi basati su asserzione quando si vogliono assegnare simultaneamente diversi utenti a uno o più ruoli delle applicazioni nell'account di SAP Cloud Platform. Per assegnare un singolo utente o un numero ridotto di utenti a ruoli specifici, è consigliabile di eseguirne l'assegnazione diretta nella scheda **Authorizations** (Autorizzazioni) del pannello di controllo di SAP Cloud Platform.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud Platform.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP Cloud Platform**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, digitare e selezionare **SAP Cloud Platform**.

    ![Il collegamento SAP Cloud Platform nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-cloud-platform-test-user"></a>Creare un utente di test di SAP Cloud Platform

Per consentire agli utenti di Azure AD di accedere a SAP Cloud Platform è necessario assegnare loro i ruoli in SAP Cloud Platform.

**Per assegnare un ruolo a un utente, seguire questa procedura:**

1. Accedere al pannello di controllo di **SAP Cloud Platform** .

2. Eseguire questi passaggi:
   
    ![Autorizzazioni](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizzazioni")
   
    a. Fare clic su **Authorization**.

    b. Fare clic sulla scheda **Utenti** .

    c. Nella casella di testo **User** digitare l'indirizzo di posta elettronica dell'utente.

    d. Fare clic su **Assign** per assegnare l'utente a un ruolo.

    e. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Cloud Platform nel pannello di accesso, si accederà automaticamente all'applicazione SAP Cloud Platform per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

